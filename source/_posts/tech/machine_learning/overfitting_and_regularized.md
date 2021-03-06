---
layout: post
title: 过拟合（Overfitting）和正则化（Regularized）
date: 2020-01-14 19:00
categories:
- 技术
tags:
- 机器学习
- 算法优化
description: 了解什么是欠拟合和过拟合，以及使用正则化来解决过拟合问题，正则化后的的代价函数和梯度下降算法的使用。
mathjax: true
---

在应用线性回归和逻辑回归去解决某些机器学习的实际应用时，它们可能会出现一种叫做“过度拟合”（Overfitting）的问题。这会使得学习的结果很糟糕。

## 什么是过拟合

用预测房价的线性回归的例子：

![线性回归图表][1]

最左边一张图片用一条直线来拟合数据，这种情况下随着x轴的变大，房价增长的幅度一直不变，这样的结果误差很大，这意味这该算法没有很好的拟合训练数据，
这种称之为 **“欠拟合”**；

中间一张图用的二次曲线来拟合数据，拟合情况良好，稍有偏差；

右边一张图用了四次项，拟合的更好，在图中，它贯穿了所有样本，但是它的曲线很扭曲，这不是一个很好的预测房价的模型，这称之为 **“过拟合”**；

### 定义

**过拟合：** 假如有很多特征值，且学习算法能够很好的拟合训练集，但是在新的样本上却表现的性能很差。

数据拟合有两个极端，当预测函数过于简单或者使用的相关特征值过少，就会出现**欠拟合（高偏差high bias）**的情况。
另一个极端，当函数过于复杂，使用的非常多的特征值，使得学习算法在训练样本上非常适合，但是不能推广到新的样本集上，这就是**过拟合（高方差high variance）**

过拟合问题除了出现在线性回归问题上，也会出现在逻辑回归上。

## 解决办法

1. 减少特征值的数量
可以通过观察手动删掉一些特征值，或者用模型选择算法来达到目的。
2. 正则化
保留所有特征值，但是减少参数$\theta_j$的大小，因为似乎每个特征值都或多或少的在预测函数起了作用。

这两种方法的本质类似，就是弱化不必要的特征值，从而解决过拟合。

## 代价函数

假设线性回归的预测函数为：
$$ h(\theta) = \theta_0+\theta_1x+\theta_2x^2+\theta_3x^3+\theta_4x^4 $$

这是一个四次项的公式，通过上面的可以知道这是一个过拟合的预测函数，需要解决过拟合，需要忽略后面的三次和四次项，这要改一下代价函数：

$$ J(\theta) = \frac{1}{2m} \sum_{i=0}^m(h_\theta(x^{(i)})-y^{(i)})^2 +1000\cdot \theta_3^2 +1000\cdot \theta_4^2 $$

在原油的代价函数上我们添加了$1000\cdot \theta_3^2$和$1000\cdot \theta_4^2$这两个，为了使代价函数尽量小，我们就需要让$\theta_3$和$\theta_4$尽量小，接近于零。

这样就给了一个正则化的思路。将关联小的特征值的参数趋向于0，使得预测函数从过拟合的状态修正过来。

正则化后的代价函数为：
$$ J(\theta)= \frac{1}{2m} \sum_{i=0}^m (h_\theta(x^{(i)})-y^{(i)})^2 + \lambda \sum_{j=1}^n\theta_j^2 ] $$

其中$\lambda$为正则化参数，为了平衡代价函数。$\lambda$需要选择合适的值。

可以看到，在代价函数后面添加了$\lambda \sum_{j=1}^n\theta_j^2$ 这一项。注意，这一项中的$j$ 是从1到m的，因为$x_0$固定为0，不需要修正，所以参数$\theta_0$，不需要添加进来。

## 线性回归正则化

梯度下降算法：
重复 {
$$ \theta_0 = \theta_0-\alpha\frac{1}{m} \sum_{i=1}^m (h_\theta(x^{(i)})-y^{(i)})x_0^{(i)} $$
$$ \theta_j = \theta_j -\alpha [\frac{1}{m}\sum_{i=1}^m (h_\theta(x^{(i)})-y^{(i)})x_j^{(i)} +\frac{\lambda}{m}\theta_j ], j \in [1,2,3,...,n] $$
}

同样的$\theta_0$不需要修正

下面的式子可以稍作调整：
$$ \theta_j = \theta_j(1-\alpha\frac{\lambda}{m}) -\alpha\frac{1}{m}\sum_{i=1}^m (h_\theta(x^{(i)})-y^{(i)})x_j^{(i)}, j \in 1,2,3,...,n] $$

可以看出$\theta_j$是在原来梯度下降的基础上有减去了$\alpha\frac{\lambda}{m}\theta_j$

## 逻辑回归正则化

代价函数：
$$ J(\theta)=-\frac{1}{m} \sum_{i=1}^m [y^{(i)} \log(h_\theta(x^{(i)})) + (1-y^{(i)}) \log(1-h_\theta(x^{(i)}))] + \frac{\lambda}{2m} \sum_{j=1}^n \theta_j^2 $$

同样的$\theta_0$不需要修正

它的梯度下降算法是：

重复 {
$$ \theta_0 = \theta_0-\alpha\frac{1}{m} \sum_{i=1}^m (h_\theta(x^{(i)})-y^{(i)})x_0^{(i)} $$
$$ \theta_j = \theta_j -\alpha [\frac{1}{m}\sum_{i=1}^m (h_\theta(x^{(i)})-y^{(i)})x_j^{(i)} +\frac{\lambda}{m}\theta_j ], j \in [1,2,3,...,n] $$
}


## 正规方程（Normal Equation）

在求解线性回归时，除了梯度下降，还有一种正规方程的方式直接求解出$\theta$。使用正规方程的也需要正则化。

正规方程的公式为：
$$ \theta=(X^TX)^{-1}X^Ty $$

需要作出修改：
$$ \theta=(X^TX + \lambda\cdot L)^{-1}X^Ty $$

其中L是一个$n+1$的方阵 $L=\left[ \begin{matrix}
0&&&& \\\ &1&&& \\\ &&1&& \\\ &&&...& \\\ &&&&1
\end{matrix} \right]$，L是一个对角线矩阵，对角线第一个元素为0。


[1]: /images/ml_15.jpg


