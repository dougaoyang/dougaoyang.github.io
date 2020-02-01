---
layout: post
title: 梯度下降算法 (Gradient Descent)
date: 2019-12-30 07:00
categories:
- 技术
tags:
- 线性回归
description: 已知了代价函数 J,如何通过梯度下降来最小化代价函数，从而获得正确的预测函数。
mathjax: true
---


已知了代价函数：
$$ J(\theta_0, \theta_1) = \frac{1}{2m} \sum_{i=0}^m(h_\theta(x^{(i)})-y^{(i)})^2 $$

我们需要一个算法来最小化 $J(\theta_0, \theta_1)$，而梯度下降算法可以解决这个问题。

梯度下降算法不仅可以应用于多种函数的求解，不仅限于线性回归问题。
梯度下降算法可以解决更一般的问题，例如 $J(\theta_0,\theta_1,\ldots,\theta_n)$ 求解该代价函数的最小值。


### 算法流程

回到线性回归上来，还是使用 $J(\theta_0, \theta_1)$ 来说明。

- 将$\theta_0, \theta_1$设置一个初始值，可以是任意值，但通常会设置成0；
- 不断的改变$\theta_0, \theta_1$的值，让$J(\theta_0, \theta_1)$的值一直减小，直到找到$J$的最小值或局部最小值。

**工作流程**

![][1]

这是一个随 $\theta_0, \theta_1$ 变化而导致 $J$ 变化的图表。我们希望最小化这个函数，根据流程，先初始化一个$\theta_0, \theta_1$的值。

把这个图像想像成一座上，初始化的试过就是把你放在山的某处，现在你要下到山的最下面，也就是山谷底。
现在把自己旋转360度，寻找一个最快下山的方向，然后迈出一步，接着重复上次的过程继续找一个最快下山的方向迈出一步，重复上面的步骤，迈出一步又一步，直到一个局部最低点的位置。

下降的轨迹
![][2]

当初始位置向右偏移一点，下降的轨迹就不一样了。
![][3]

这样我们得到了两个局部最低点，当初始位置不同时会得到不同的局部最优解，这是梯度下降算法的一个特点。

取得下降方向的方法就是取代价函数的导数（即一个函数的切线），切线的斜率就是那个点的导数。当斜率趋向于0时就说明代价函数的值在下降。而每一步的步长则是由学习率（learning rate）$\alpha$ 来表示。

例如：上图中每个 “星” 之间的距离就是由参数 $\alpha$ 来确定，当 $\alpha$ 较大时，步长就较大，同样的，$\alpha$较小时，步长就会变小；前进的方向则是由 $J(\theta_0, \theta_1)$ 出的偏导数来决定。根据不同的起点，可能会在不同的终点结束。

### 定义

梯度下降算法的定义是：

*重复下面的步骤直到$J(\theta_0, \theta_1)$收敛*
$$ \theta_j:=\theta_j−\alpha \frac{\partial}{\partial \theta_j} J(\theta_0, \theta_1), j \epsilon \left(0, 1\right) $$

$j$ 表示索引，此例中可取0, 1

**注意：**
当改变参数 $\theta_0, \theta_1, \ldots$ 时，需要注意的一点就是要同时改变这些参数，这对于获得正确的代价函数是非常重要的。

$\color{green}{下面这个例子是正确的}$：
$temp0:=\theta_0−\alpha \frac{\partial}{\partial \theta_0} J(\theta_0, \theta_1)$
$temp1:=\theta_1−\alpha \frac{\partial}{\partial \theta_1} J(\theta_0, \theta_1)$
$\theta_0:=temp0$
$\theta_1:=temp1$
这样的一个步骤是正确的。


$\color{red}{下面这个例子是错误的}$：
$temp0:=\theta_0−\alpha \frac{\partial}{\partial \theta_0} J(\theta_0, \theta_1)$
$\theta_0:=temp0$
$temp1:=\theta_1−\alpha \frac{\partial}{\partial \theta_1} J(\theta_0, \theta_1)$
$\theta_1:=temp1$


用一个例子来说明梯度下降算法：

有一个单变量线性回归的预测函数，同时将$\theta_0=0$,这样预测函数就是：$h_\theta(x) = \theta_1x$
这样梯度下降算法就是重复计算:
$$ \theta_1:=\theta_1−\alpha \frac{\partial}{\partial \theta_1} J(\theta_1) $$

![][4]

红线就代表的代价函数的导数，即在这一点的斜率，上图的点上我们可以知道 $\frac{\partial}{\partial \theta_1} J(\theta_1) \geq 0$，
代入式子中，我们可以得到$\theta_1$在变小，向左靠拢。

![][5]

当初始 $\theta_1$ 在左边，它的斜率是负数，我们可以得到$\theta_1$在变大，向右靠拢。

当接近J函数的最底部时，$\frac{\partial}{\partial \theta_1} J(\theta_1)$ 接近0。当到最理想的情况下，$\frac{\partial}{\partial \theta_1} J(\theta_1) = 0$，在这张情况下我们可以得到：$\theta_1:=\theta_1−\alpha*0$。即$\theta_1$ 不会再变化。

由上图可知，当学习率$\alpha$ 过小时，我们需要经过很多步才能到达最低点，耗时会更长。而当$\alpha$ 过大时，可能会越过最低点，导致无法收敛

![][6]

总结一下：当越接近局部最小值的地方时，偏导数 $\frac{\partial}{\partial \theta_1} J(\theta_1)$ 就越小，在$\alpha$不变的情况下，$\theta_1$ 下降的步子也就越来越小，当达到局部最优时，$\theta_1$就不变了，因此，我们不需要减小$\alpha$的值。


## 应用到线性回归问题上


已知线性回归的预测函数：
$$ h_\theta(x) = \theta_0 + \theta_1x $$

将预测函数代入到代价函数中并应用数学求导，可得到：

当j=0时:
$$\theta_0:=\theta_0 - \alpha \frac{1}{m} \sum_{i=0}^m(h_\theta(x_i)-y_i)$$
当j=1时：
$$\theta_1:=\theta_1 - \alpha \frac{1}{m} \sum_{i=0}^m((h_\theta(x_i)-y_i)x_i)$$

在线性回归问题中，我们从一个假设的$\theta_0, \theta_1$开始，重复应用这些梯度下降方程，之后我们的预测函数就会越来越精确。



[1]: /images/ml_5.jpg "流程1"
[2]: /images/ml_6.gif "动态1"
[3]: /images/ml_7.gif "动态2"
[4]: /images/ml_8.jpg
[5]: /images/ml_9.jpg
[6]: /images/ml_10.jpg

