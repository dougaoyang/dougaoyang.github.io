---
layout: post
title: 分类和逻辑回归算法（Logistic Regression）
date: 2020-01-09 18:00
categories:
- 技术
description: 了解分类算法的特点，以及S型函数的作用和决策边界在分类算法中的应用。
mathjax: true
---

**分类**是监督学习的一种，是目前使用很广泛的一种算法。

比如说：
- 判断垃圾邮件
- 判断交易是否是骗人的
- 判断肿瘤是否是恶性的

这些例子有一个共同的特点，它们的输出$y\in \left[0, 1\right]$
0: 表示负类（negative class），例如，不是垃圾邮件；
1: 表示正类（positive class），例如，是垃圾邮件。

把那一类归为 “正类” 一般是看输出值所占比例的大小，占小比例的一般被当作 “正类” 。

当然$y$也可以有多个输出，例如$y\in \left[0, 1,2,3...n\right]$, 这是一个多类的问题，在了解多类问题前，先了解一下二元分类（binary classification problem）的情况。
![二元分类的输入输出图][1]

上图是随着肿瘤的大小，判断恶性肿瘤的散点图。可以看出输出只在0和1这两个值之间交替

我们先忽略$y$是离散值，采用旧的线性回归算法来尝试预测$y$。 先使用线性回归的预测函数

$$ h_\theta(x) = \theta_0x_0 + \theta_1x_1 = \theta^TX $$

这样得出的预测是一条直线，但是这样的预测函数，我们会得到$h_\theta > 1$或者$h_\theta < 0$的情况出现，这样的结果是不准确的。我们期望是有一个预测函数，它的输出值在0到1之间：

$$ h_\theta(x) = g(\theta^TX) $$

$g$函数是一个关于指数的S型函数（Sigmoid Function）：

$$ g(z) = \frac{1}{1+e^{-z}} $$

其中参数 $z=\theta^TX$

所以分类问题的预测函数可以写成：

$$ h_\theta(x) = \frac{1}{1+e^{-\theta^TX}} $$
![$g$函数的曲线图][2]

这是$g$函数的曲线图，从图中，可以看出输出值的范围在0到1之间，这个满足我们需要的条件，当$z$越小是就无限趋向于0，反之则无限趋向于1。

该函数得到的是连续的值，而分类算法需要的到的是离散的数（0, 1），因此我们可以将$g$函数得到的值看成是输出某个值的概率。

假设使用该预测函数得到$h(\theta)=0.7$，我们可以认为肿瘤是恶性的概率是70%，可以用一个式子表示：
$$ h(\theta)=P(y=1|x; \theta) $$
该式子表示在给定特征$x$和对应的参数$\theta$下，$y=1$的概率。

因为$y$只能在(0, 1)的范围内，所以
$$ P(y=1|x; \theta)+P(y=0|x; \theta) = 1 $$

## 决策边界(decision boundary)

假设预测当函数返回值$h(\theta)\geq 0.5$ 时认定$y=1$，即当概率大于50%则认为会发生。
$h(\theta)\geq 0.5 → y=1$
$h(\theta)< 0.5 → y=0$

当参数$z\geq 0$时，$g(z)\geq 0.5$，这样就要求:
$\theta^TX \geq 0$

所以预测结果可以写成：
$\theta^TX \geq 0 → y=1$
$\theta^TX < 0 → y=0$

因此$\theta^TX$就是决策边界，当大于等于的就是$y=1$，当小于的就是$y=0$。

例子：
假如预测函数$h(\theta)=g(\theta_0+\theta_1x_1+\theta_2x_2)$，以及参数$(\theta_0,\theta_1,\theta_2)$分别为(-3,1,1)，这样这个预测函数的决策边界就是：
$$ -3+x_1+x_2 \geq 0 $$

![决策边界][3]

上图红线就是决策边界，在红线的右上方就是$y=1$，左下方就是$y=0$。

除了线性的决策边界，还可以是更复杂的非线性的。例如
$h(\theta)=g(\theta_0+\theta_1x_1^2+\theta_2x_2^2)$。且$\theta_0=-1,\theta_1=1,\theta_2=1$
这个的决策边界就是：
$$ -1 + x_1^2 + x_2^2 \geq 0 $$
这个的图像就是一个圆形。

![非线性条件决策边界][4]

在圆形之外的$y=1$，在圆内的就是$y=0$。

[1]: /images/ml_11.jpg
[2]: /images/ml_12.jpg
[3]: /images/ml_13.jpg
[4]: /images/ml_14.jpg

