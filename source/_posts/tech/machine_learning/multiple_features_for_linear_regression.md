---
layout: post
title: 多变量线性回归问题
date: 2020-01-03 21:00
categories:
- 技术
description: 多变量线性回归问题上梯度下降算法的使用，用向量形式表现预测函数。
mathjax: true
---

房价的例子，当只有一个特征变量时，线性回归的预测函数：
$$ h_\theta(x) = \theta_0 + \theta_1x $$

现在有了多个特征变量：

|尺寸$x_1$ | 房间数$x_2$ | 房屋年份$x_3$ | 价格$y$|
| :--| :-- | :-- | :-- |
|2104 | 5 | 45 | 460|
|1416 | 3 | 40 | 232|
|1534 | 3 | 30 | 315|
|852  | 2 | 36 | 178|
|...  | ...| ...| ...|

上面的图表展示了多个和房屋价格相关的变量。

下面定义一些变量
- **m** = 训练数据集数量
- **n** = 特征变量数
- **$x^{(i)}$** = 第 i 个训练样本
- **$x_j^{(i)}$** = 第 i 个训练样本的 第 j 个特征变量

上面的表格中
$m=4$
$n=3$
$x^{(2)}=\left[ \begin{matrix} 1416 \\\ 3 \\\ 40 \\\ \cdots \end{matrix} \right]$
$x_3^{(2)}=40$

在多变量条件下，预测函数为
$$ h_\theta(x)= \theta_0+\theta_1x_1+\theta_2x_2+\cdots+\theta_nx_n $$

为了方便，我们自定义一个变量$x_0=1$

预测函数即为：
$$ h_\theta(x)= \theta_0x_0+\theta_1x_1+\theta_2x_2+\cdots+\theta_nx_n $$


用向量的方式定义 $x$ 和 $\theta$

$
x=\left[ \begin{matrix} x_0 \\\ x_1 \\\ x_2  \\\ \cdots \\\ x_n \end{matrix} \right],
\theta=\left[ \begin{matrix} \theta_0 \\\ \theta_1 \\\ \theta_2 \\\ \cdots \\\ \theta_n \end{matrix} \right]
$

用向量的方式表示预测函数：

$$ h_\theta(x) = \left[ \begin{matrix} \theta_0 & \theta_1 & \cdots & \theta_n \end{matrix} \right] \left[ \begin{matrix} x_0 \\\ x_1 \\\ \cdots \\\ x_n \end{matrix} \right] = \theta^Tx $$

用梯度算法来解决这个问题。

重复直到$J(\theta)$收敛 {
$ \theta_j:=\theta_j - \alpha \frac{1}{m} \sum_{i=0}^m((h_\theta(x^{(i)})-y^{(i)})x_j^{(i)}), j \epsilon \left(0, 1, 2,...,n\right) $
}

比如说：
$ \theta_0:=\theta_0 - \alpha \frac{1}{m} \sum_{i=0}^m((h_\theta(x^{(i)})-y^{(i)})x_0^{(i)}) $,
$ \theta_1:=\theta_1 - \alpha \frac{1}{m} \sum_{i=0}^m((h_\theta(x^{(i)})-y^{(i)})x_1^{(i)}) $,
$ \theta_2:=\theta_2 - \alpha \frac{1}{m} \sum_{i=0}^m((h_\theta(x^{(i)})-y^{(i)})x_2^{(i)}) $,
$...$

重复上面的下降知道代价函数收敛。这就是梯度下降的算法过程。
