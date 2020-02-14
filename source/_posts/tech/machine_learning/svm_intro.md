---
layout: post
title: 支持向量机算法（SVM）介绍
date: 2020-01-31 11:00
categories:
- 技术
tags:
- 机器学习
- SVM
description: 了解分类算法中一种强大的算法，支持向量机。以及它的代价函数说明。
mathjax: true
---

在解决复杂的非线性分类问题时，除了逻辑回归和神经网络，还有一种更为强大的算法：叫做**支持向量机（Support Vector Machines）**，简称**SVM**。


## 代价函数

在分类问题上，常用的激励函数为：
$$ h_\theta(x) = \frac{1}{1+e^{-\theta^Tx}} $$

逻辑回归的cost函数：
$$ Cost(h_\theta(x), y) = -y \log(h_\theta(x)) - (1-y) \log(1-h_\theta(x)) $$

代入激励函数，得到：
$$ Cost(h_\theta(x), y) = -y \log(\frac{1}{1+e^{-\theta^Tx}}) - (1-y) \log(1-\frac{1}{1+e^{-\theta^Tx}}) $$

上述式子中：
当样本的输出$y$为1时：$Cost(h_\theta(x), y) = -\log(\frac{1}{1+e^{-z}}), z=\theta^Tx$

当样本的输出$y$为0时：$Cost(h_\theta(x), y) = -\log(1-\frac{1}{1+e^{-z}}), z=\theta^Tx$

该式子的值随着z的变化曲线：
<div style="display:flex;">
<img  src="/images/ml_25.jpg" alt="">
<img  src="/images/ml_26.jpg" alt="">
</div>

可以看到当$y=1$时，该式子的值随着z值的增大而减小；当$y=0$时，该式子的值随着z值的增大而增大。

下面开始构建向量机
将上面的变化曲线做一下简化：

![][1]

- 当$y=1$时，以$z=1$为分界点，当$z\geq 1$，式子的值为0，当小于1时，是一条线性变化的直线；
用 **$cost_1(z)$**来表示该曲线。
- 当$y=0$时，以$z=-1$为分界点，当$z\leq -1$，式子的值为0，当大于-1时，是一条线性变化的直线；
用 **$cost_0(z)$**来表示该曲线。


逻辑回归的代价函数为：
$$ J(\theta)=-\frac{1}{m} \sum_{i=1}^m [y^{(i)} \log(h_\theta(x^{(i)})) + (1-y^{(i)}) \log(1-h_\theta(x^{(i)}))] + \frac{\lambda}{2m} \sum_{j=1}^n \theta_j^2  $$

将$cost_1(z)$和$cost_0(z)$代入上述式子：

$$ J(\theta)=-\frac{1}{m} \sum_{i=1}^m [y^{(i)} cost_1(\theta^Tx) + (1-y^{(i)}) cost_0(\theta^Tx)] + \frac{\lambda}{2m} \sum_{j=1}^n \theta_j^2 $$

为了方便计算，将式子乘以$m$；再将$\lambda$化去，用$C$代替：

$$ J(\theta)=-C \sum_{i=1}^m [y^{(i)} cost_1(\theta^Tx) + (1-y^{(i)}) cost_0(\theta^Tx)] + \frac{1}{2} \sum_{j=1}^n \theta_j^2 $$

上述便是SVM的代价函数。
$C$值是一种控制参数权重的方法，和逻辑分类的$\lambda$的作用类似。区别是更关心前一个式子的优化还是后一个式子的优化。为了方便，可以简单的把C和$\lambda$的关系比做$C=\frac{1}{\lambda}$。

通过代价函数 和 $cost_1(z)$和$cost_0(z)$ 的变化曲线，可以得知：

- 当$y=1$：$\theta^Tx \geq  1$条件下，代价函数的值趋向于0；
- 当$y=0$：$\theta^Tx \leq -1$条件下，代价函数的值趋向于0；

上述条件比起在逻辑回归中，$\theta^Tx \geq 0$时预测值为1，$\theta^Tx < 0$预测值为0，支持向量机的要求更加严格。

## 决策边界

支持向量机在选择决策边界时会选择一个离样本间距最大的边界，因此支持向量机有时被称为**大间距分类器**。


[1]: /images/ml_27.jpg

 