---
layout: post
title: 逻辑回归模型的代价函数
date: 2020-01-11 08:00
categories:
- 技术
description: 了解逻辑回归的代价函数方程
mathjax: true
---

假设有训练集有m个样本
$$ (x^{(1)},y^{(1)}),(x^{(2)},y^{(2)}),(x^{(3)},y^{(3)}),...,(x^{(m)},y^{(m)}) $$
有n个特征值
$$ x \in \left[ \begin{matrix} x_0 \\\ x_1 \\\ x_2 \\\ ... \\\ x_n \end{matrix} \right], x_0=1,y \in [0, 1] $$
预测函数
$$ h_\theta(x) = \frac{1}{1+e^{-\theta^Tx}} $$

与线性回归类似，先要获得逻辑回归的代价函数：
$$ J(\theta)=\frac{1}{m} \sum_1^m Cost(h_\theta(x^{(i)}), y^{(i)}) $$

这个和线性回归的代价函数类似，只是替换了：
$$ Cost(h_\theta(x), y) = \frac{1}{2} (h_\theta(x^{(i)})-y^{(i)})^2 $$

上面的是线性回归的 $Cost$ 函数的值，在逻辑回归模型中，该函数为：
$$ Cost(h_\theta(x), y) = \begin{cases} -\log(h_\theta(x)), y=1 \\\ -\log(1-h_\theta(x)), y=0 \end{cases} $$

在这样的情况下
- 当 $y=1, h_\theta(x)=1$ 时，可以得到 $Cost(h_\theta(x), y)=0$；
- 当 $y=1, h_\theta(x)=0$ 时，可以得到 $Cost(h_\theta(x), y)→\infty$；
- 当 $y=0, h_\theta(x)=0$ 时，可以得到 $Cost(h_\theta(x), y)=0$；
- 当 $y=0, h_\theta(x)=1$ 时，可以得到 $Cost(h_\theta(x), y)→\infty$；

这样总结一下就是当 $y=h_\theta(x)$ ,代价函数的值为0，当两者不等时，单价函数的值为去穷大，这符合代价函数的定义。

两种情况下的$Cost$函数可以合并为一个：
$$ Cost(h_\theta(x), y) = -y \log(h_\theta(x)) - (1-y) \log(1-h_\theta(x)) $$

这样$\color{red}{逻辑回归的代价函数}$：

$$ J(\theta)=-\frac{1}{m} \sum_1^m [y^{(i)} \log(h_\theta(x^{(i)})) + (1-y^{(i)}) \log(1-h_\theta(x^{(i)}))]  $$

用向量的形式来表示则是：

$$ h = g(X\theta) $$
$$ J(\theta)=\frac{1}{m} \cdot (-y^T \log(h) -(1-y)^T \log(1-h)) $$

