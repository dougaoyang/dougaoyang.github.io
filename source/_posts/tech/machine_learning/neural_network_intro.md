---
layout: post
title: 神经网络算法介绍
date: 2020-01-16 10:00
categories:
- 技术
description: TODO
mathjax: true
---

在解决分类问题时，可以用逻辑回归算法，但当解决复杂的非线性分类器时，这并不是一个好的选择。
如果用逻辑回归来解决，首先要构造一个包含很多非线性项的逻辑回归函数。使用逻辑回归会构造一个s型函数$g$。当多项式足够多，足够复杂，会有一个非常扭曲的决策边界。这可能会出现过拟合的情况。
另一个问题，复杂的非线性分类器会包含有很多的特征项，要包含所有的特征项时很困难的事情，而且计算成本过大。

比如要识别一个图像是不是汽车，就要检测图像的每一个像素，这是一个非常大的计算量。因此神经网络是一个很好的选择。

## 神经网络模型

![逻辑单元][1]

这是一个最简单的神经网络的模型。左侧的是三个特征值的输入，右侧是一个输出，这是一个二元问题的神经网络模型。

一般在处理神经网络时，和逻辑回归一样，需要添加一个$x_0$的默认特征项。在神经网络里，这称之为偏置单位
$$ x=\left[ \begin{matrix}
x_0 \\\ x_1 \\\ x_2 \\\ x_3
\end{matrix} \right], \theta=\left[ \begin{matrix}
\theta_0 \\\ \theta_1 \\\ \theta_2 \\\ \theta_3
\end{matrix} \right]$$
还有关于$h(\theta)$的函数
$$ h_\theta(x) = \frac{1}{1+e^{-\theta^Tx}} $$
在神经网络里这个成为激励函数，这是神经网络的术语，它是和逻辑回归里相同的函数。在这种情况下，激励函数的参数$\theta$称之为权重。

看一个复杂一点的模型：

![多层神经网络][2]

上图中，第一层是**输入层**，然后进入第二层，最后输出预测函数，也就是第三层，**输出层**。
输入层和输出层之间的节点层，称之为**隐藏层**。上图中有一个隐藏层。
隐藏层中的$a_i^{(j)}$是第$j$层的第$i$个单元。和输入层一样，在计算时会添加一个偏置单元$a_0^{(j)}=1$
$$ a^{(j)}=\left[ \begin{matrix}
a_0^{(j)} \\\ a_1^{(j)} \\\ a_2^{(j)} \\\  a_3^{(j)}
\end{matrix} \right]$$

$\theta^{(j)}$是从第$j$层到第$j+1$层的映射参数矩阵。

## 计算过程

隐藏层的激活节点和输出层的输出函数计算过程如下：
$ a_0^{(2)} = 1 $
$ a_1^{(2)} = g(\theta_{10}^{(1)}x_0+\theta_{11}^{(1)}x_1+\theta_{12}^{(1)}x_2+\theta_{13}^{(1)}x_3) $
$ a_2^{(2)} = g(\theta_{20}^{(1)}x_0+\theta_{21}^{(1)}x_1+\theta_{22}^{(1)}x_2+\theta_{23}^{(1)}x_3) $
$ a_3^{(2)} = g(\theta_{30}^{(1)}x_0+\theta_{31}^{(1)}x_1+\theta_{32}^{(1)}x_2+\theta_{33}^{(1)}x_3) $
$ h_\theta(x) = a_1^{(3)} = g(\theta_{10}^{(2)}a_0^{(2)}+\theta_{11}^{(2)}a_1^{(2)}+\theta_{12}^{(2)}a_2^{(2)}+\theta_{13}^{(2)}a_3^{(2)})$

$\theta^{(j)}$是一个矩阵，关于它的维度：
在第$j$层，该层有$s_j$个单元，而第$j+1$层有$s_k$个单元，则$\theta^{(j)}$会是一个$s_k\times s_j + 1$维矩阵

举个例子，假如有一个三层的神经网络，第一层有2个输入特征值，第二层是3个单元，最后第三层输出1个预测结果：
$\theta^{(1)}$s是一个$3\times 3$的矩阵
$\theta^{(2)}$s是一个$1\times 4$的矩阵


为了方便，将激励函数中的参数用变量$z$替换：
$ a_0^{(2)} = 1 $
$ a_1^{(2)} = g(z_1^{(2)}) $
$ a_2^{(2)} = g(z_2^{(2)}) $
$ a_3^{(2)} = g(z_3^{(2)}) $

其中$z$为：
$ z_k^{(2)}= \theta_{k,0}^{(1)}x_0 + \theta_{k,1}^{(1)}x_1 + ... + \theta_{k,n}^{(1)}x_n $

### 向量形式

用向量的形式来表示：

$x=\left[ \begin{matrix}
x_0 \\\ x_1 \\\ x_2 \\\ x_3
\end{matrix} \right], z^{(2)}= \left[ \begin{matrix}
z_1^{(2)} \\\ z_2^{(2)} \\\ z_2^{(2)}
\end{matrix} \right]$

$z^{(2)} = \theta^{(1)}x$
$a^{(2)} = g(z^{(2)})$

第三层：

$z^{(3)} = \theta^{(2)}z^{(2)}$

类推到通常情况：
$z^{(j)} = \theta^{(j-1)}a^{(j-1)}$
$a^{(j)} = g(z^{(j)})$
最后一步
$h_\theta(x) = a^{(j+1)} = g(z^{(j+1)})$


## 举例说明

### 1.预测“与” AND

$$ \left[ \begin{matrix}
x_0 \\\ x_1 \\\ x_2
\end{matrix} \right] → \left[ \begin{matrix}
g(z^{(2)})
\end{matrix} \right] → h_\theta(x) $$

其中$x_0 = 1$

我们要计算“与”，其中$x1,x2 \in [0,1]$，$y=x_1$ && $x_2$
设置$\theta^{(1)} = \left[ \begin{matrix}
-30 & 20 & 20
\end{matrix} \right]$

通过上面的公式
$h_\theta(x) = \theta^{(1)}x = g(-30+20x_1+20x_2)$

$x_1=0,x_2=0$，$h_\theta(x)=g(-30)\approx 0$
$x_1=0,x_2=1$，$h_\theta(x)=g(-10)\approx 0$
$x_1=1,x_2=0$，$h_\theta(x)=g(-10)\approx 0$
$x_1=1,x_2=1$，$h_\theta(x)=g(10)\approx 1$

满足”与“的逻辑。

### 2.预测“或” OR

与 预测“AND”的神经网络模型一样，我们只是调整一下：$\theta^{(1)} = \left[ \begin{matrix}
-10 & 20 & 20
\end{matrix} \right]$

$h_\theta(x) = \theta^{(1)}x = g(-10+20x_1+20x_2)$

$x_1=0,x_2=0$，$h_\theta(x)=g(-10)\approx 0$
$x_1=0,x_2=1$，$h_\theta(x)=g(10)\approx 1$
$x_1=1,x_2=0$，$h_\theta(x)=g(10)\approx 1$
$x_1=1,x_2=1$，$h_\theta(x)=g(30)\approx 1$

满足”或“的逻辑。

### 3.预测“异或” XOR

$$ \left[ \begin{matrix}
x_0 \\\ x_1 \\\ x_2
\end{matrix} \right] → \left[ \begin{matrix}
a_0^{(2)} \\\ a_1^{(2)} \\\ a_2^{(2)}
\end{matrix} \right] → \left[ \begin{matrix}
g(z^{(3)})
\end{matrix} \right] → h_\theta(x) $$

其中$x_0=1,a_0^{(2)}=1$

$\theta^{(1)} = \left[ \begin{matrix} 
-10 & 20 & 20 \\\ 40 & -30 & -30
\end{matrix} \right], \theta^{(2)} = \left[ \begin{matrix}
-30 & 20 & 20
\end{matrix} \right] $

$a^{(2)} = \theta^{(1)}x$，$h_\theta(x)=a^{(3)}=\theta^{(2)}a^{(2)}$


$x_1=0,x_2=0$，$a^{(2)}=\left[ \begin{matrix} 
1 \\\ g(-10)\\\ g(40)
\end{matrix} \right]\approx\left[ \begin{matrix} 
1 \\\ 0 \\\ 1
\end{matrix} \right]$，$h_\theta(x)=g(-10)\approx 0$

$x_1=0,x_2=1$，$a^{(2)}=\left[ \begin{matrix} 
1 \\\ g(10) \\\ g(10)
\end{matrix} \right]\approx\left[ \begin{matrix} 
1 \\\ 1 \\\ 1
\end{matrix} \right]$，$h_\theta(x)=g(10)\approx 1$

$x_1=1,x_2=0$，$a^{(2)}=\left[ \begin{matrix} 
1 \\\ g(10) \\\ g(10)
\end{matrix} \right]\approx\left[ \begin{matrix} 
1 \\\ 1 \\\ 1
\end{matrix} \right]$，$h_\theta(x)=g(10)\approx 1$

$x_1=1,x_2=1$，$a^{(2)}=\left[ \begin{matrix} 
1 \\\ g(30) \\\ g(-20)
\end{matrix} \right]\approx\left[ \begin{matrix} 
1 \\\ 1 \\\ 0
\end{matrix} \right]$，$h_\theta(x)=g(-10)\approx 0$

这符合“异或”的逻辑。


[1]: /images/ml_16.jpg
[2]: /images/ml_17.jpg

