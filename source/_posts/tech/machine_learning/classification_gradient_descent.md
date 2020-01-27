---
layout: post
title: 关于逻辑回归模型的梯度下降算法
date: 2020-01-11 10:00
categories:
- 技术
description: 了解逻辑回归的梯度下降算法和公式，以及用向量化的方式来求解。
mathjax: true
---

逻辑回归的代价函数：

$$ J(\theta)=-\frac{1}{m} \sum_1^m [y^{(i)} \log(h_\theta(x^{(i)})) + (1-y^{(i)}) \log(1-h_\theta(x^{(i)}))]  $$

与线性回归一样，它的梯度下降算法类似：

重复直到$J(\theta)$收敛 {
$  \theta_j:=\theta_j−\alpha \frac{\partial}{\partial \theta_j} J(\theta) $
}

计算$\frac{\partial}{\partial \theta_j} J(\theta)$后会得到：
$$ \theta_j:=\theta_j−\alpha \frac{1}{m} \sum_{i=0}^m((h_\theta(x^{(i)})-y^{(i)})x_j^{(i)}), j \epsilon \left(0, 1, 2,...,n\right) $$

计算后得到的和线性回归的看上去没有区别，但是两者的$h_\theta(x)$不同。
线性回归的是：$h_\theta(x) = \theta^Tx$
逻辑回归的是：$h_\theta(x) = \frac{1}{1+e^{-\theta^Tx}} $

因为预测函数改变了，所以两者的梯度下降算法是不一样的。

## 举例说明

假设数据集有5个样本，每个样本有2个特征值，即$m=5,n=2$如下：

$$ X = \left[ \begin{matrix}
34.62 & 78.02 \\\ 30.29 & 3.89 \\\ 35.85 & 72.9 \\\ 60.18 & 86.31 \\\ 79.03 & 75.34
\end{matrix} \right], y = \left[ \begin{matrix}
0 \\\ 0 \\\ 0 \\\ 1 \\\ 1
\end{matrix} \right]$$

初始化$\theta=\left[ \begin{matrix} 0\\\0\\\0 \end{matrix} \right]$，然后在输入矩阵加上一列$x_0=1$，
$ X = \left[ \begin{matrix}
1 & 34.62 & 78.02 \\\ 1 & 30.29 & 3.89 \\\ 1 & 35.85 & 72.9 \\\ 1 & 60.18 & 86.31 \\\ 1 & 79.03 & 75.34
\end{matrix} \right]$

先计算预测函数$h$：
$h=g(X\theta)$
$X\theta = \left[ \begin{matrix}
1 & 34.62 & 78.02 \\\ 1 & 30.29 & 3.89 \\\ 1 & 35.85 & 72.9 \\\ 1 & 60.18 & 86.31 \\\ 1 & 79.03 & 75.34
\end{matrix} \right] \left[ \begin{matrix}
0 \\\ 0 \\\ 0
\end{matrix} \right] = \left[ \begin{matrix}
0 \\\ 0 \\\ 0
\end{matrix} \right]$
代入$g$函数，可以的得到预测结果$h=\left[ \begin{matrix}
\frac{1}{1+e^0} \\\ \frac{1}{1+e^0} \\\ \frac{1}{1+e^0}\\\ \frac{1}{1+e^0}\\\ \frac{1}{1+e^0}
\end{matrix} \right]=\left[ \begin{matrix}
0.5 \\\ 0.5 \\\ 0.5 \\\ 0.5 \\\ 0.5
\end{matrix} \right]$

代入J的公式
$$ J(\theta)=\frac{1}{m} \cdot (-y^T \log(h) -(1-y)^T \log(1-h)) $$
此时代价函数$J$的值为：$\color{red}{0.69315}$

下面计算第一次梯度下降的梯度值，代入下面的公式
$$ \theta = \theta - \frac{\alpha}{m} X^T(h-y) $$

即：
$ \theta = \theta - \alpha\frac{1}{5} \cdot \left[ \begin{matrix}
1 & 1 & 1 & 1 & 1 \\\ 34.62 & 30.29 & 35.85 & 60.18 & 79.03 \\\ 78.02 & 3.89 & 72.9 & 86.31 & 75.34
\end{matrix} \right] \left[ \begin{matrix}
0.5 - 0 \\\ 0.5-0 \\\ 0.5-0 \\\ 0.5-1 \\\ 0.5-1
\end{matrix} \right] = \left[ \begin{matrix}
0 \\\ 0 \\\ 0
\end{matrix} \right] - \alpha \cdot \left[ \begin{matrix}
0.1 \\\ -3.846 \\\ 3.317
\end{matrix} \right]$

假设$\alpha=0.01$，则$\theta=\left[ \begin{matrix}
0.001 \\\ 0.03846 \\\ -0.03317
\end{matrix} \right]$

再次计算代价函数$J$为：$\color{red}{0.51494}$

这个例子用矩阵和向量来进行了代价函数和梯度下降的计算。

