---
layout: post
title: 核函数的概念以及在SVM上的应用
date: 2020-02-01 08:00
categories:
- 技术
tags:
- 机器学习
- SVM
description: 了解核函数的概念，高斯核函数的公式，以及核函数在支持向量机算法上的应用。
mathjax: true
---

## 高斯核函数

激励函数中$\theta^Tx=\theta_0 + \theta_1x_1 + \theta_2x_2 + ... + \theta_nx_n$

现在准备用新的特征值$f_1,f_2,...$ 来替换 $x_1,x_2,...$

将$f$定义为两个向量的相似度：

例如，有一个标记向量$l^{(i)}$，某个样本的特征向量$x$和其的相似度为：
$$ f_i = similarity(x, l^{(i)}) = exp(-\frac{|| x-l^{(i)} ||^2}{2\sigma^2})$$

*PS：$|| x ||$ 是 x向量的范数。*

该核函数又称之为**高斯核函数（Gaussian Kernels）**。

当$x$和$l^{(i)}$很相似时，$f_i \approx 1$；当两者差距很大时，$f_i \approx 0$。

高斯核函数中有个关键参数$\sigma$，它的大小决定了，该函数值的变化速度。当$\sigma$很小时，$f_i$的变化就会很快，两个向量一点细微的差距就会本放大。当$\sigma$变大时，则相反：

<div style="display:flex;">
<img  src="/images/ml_28.jpg" alt="">
<img  src="/images/ml_29.jpg" alt="">
<img  src="/images/ml_30.jpg" alt="">
</div>

举个例子，假设设计三个新的特征变量$f_1,f_2,f_3$：
当$\theta_0 + \theta_1f_1 + \theta_2f_2 + \theta_3f_3 \geq 0$时，通过预测函数$h(\theta)$预测为1。

我们假设$\theta_0=-0.5, \theta_1=1, \theta_2=1, \theta_3=0$

当样本x接近$l_1$时，$f_1 \approx 1$，$f_2 \approx 0$，$f_3 \approx 0$，可以得出上述表达式为$-0.5 + 1 + 0 + 0 = 0.5$，可以预测该样本的输出为1，以此类推，可以得出在标记向量$l_1,l_2$附近的向量即预测为1，远离的预测为0：

<img width="40%" src="/images/ml_31.jpg" alt="">

## 应用到支持向量机上

### 选取标记向量

在实际应用中，将每个样本作为标记向量。
假设有样本$x^{(1)},x^{(2)},...,x^{(m)}$，同样的，将每个样本都定义为标记点，$l^{(1)},l^{(2)},...,l^{(m)}$，即：$x^{(1)}=l^{(1)},x^{(2)}=l^{(2)},...,x^{(m)}=l^{(m)}$。

对于某一个样本$(x^{(i)},y^{(i)})$来说：
$f_1^{(i)}$ = similarity$(x^{(i)}, l^{(1)})$
$f_2^{(i)}$ = similarity$(x^{(i)}, l^{(2)})$
...
$f_i^{(i)}$ = similarity$(x^{(i)}, l^{(i)})=1$
...
$f_m^{(i)}$ = similarity$(x^{(i)}, l^{(m)})$

*PS：在这其中，第$i$个样本向量和第$i$个标记向量是同一个，所以值为1。*


对于某一个样本的新的特征向量$f^{(i)}=\left[ \begin{matrix} f^{(i)}_0 \\\ f^{(i)}_1 \\\ f^{(i)}_2 \\\ ... \\\ f^{(i)}_m \end{matrix}\right]$，同样的$f^{(i)}_0$始终为1，$f$是一个m+1的向量。

当使用新的特征值后，当$\theta^Tf \geq 0$时，即可预测输出值为1。

支持向量机的代价函数为：
$$ J(\theta)=-C \sum_{i=1}^m [y^{(i)} cost_1(\theta^Tx) + (1-y^{(i)}) cost_0(\theta^Tx)] + \frac{1}{2} \sum_{j=1}^n \theta_j^2 $$

将新的特征值替换到支持向量机的代价函数中去：

$$ J(\theta)=-C \sum_{i=1}^m [y^{(i)} cost_1(\theta^Tf^{(i)}) + (1-y^{(i)}) cost_0(\theta^Tf^{(i)})] + \frac{1}{2} \sum_{j=1}^m \theta_j^2 $$

可以看到将$x^{(i)}$替换成了$f^{(i)}$，$\sum_{j=1}^m \theta_j^2$中是统计从1到m的参数了，因为，新的特征向量是m+1维向量，而且$\theta_0$不用修正。

核函数也可以用到逻辑回归上，但是这样计算成本会很高，速度会慢许多。而在SVM上，有许多针对核函数的优化方法，使得核函数在SVM上运行良好。


## SVM使用事项

在使用SVM时一般都是使用第三方包提供的SVM优化算法，我们仅需提供：
- 参数C
- 选择核函数

### 参数C
参数C可以看作$\frac{1}{\lambda}$，与$\lambda$相关的：
- C过大时，会导致高方差，过拟合的问题；
- C过小时，会导致高偏差，欠拟合的问题。

因此需要选取折中的C值，可以通过选取多个C值，然后在交叉验证集上简直多个C值的误差，选择最小的。

### 核函数选择
在核函数的选择上，一般有两种，一个是不用核函数，一个是高斯核函数。

不用核函数也称为线性核函数。它和逻辑回归的算法效果类似。当训练集有大量的特征值，但是样本数量不是太多时，这时拟合一个线性的边界条件会有比较好的效果，也即一般会不使用核函数或使用线性核函数。 

高斯核函数 则需要去选择$\sigma^2$

**参数$\sigma^2$**

- 当$\sigma^2$过小时，$f$核函数的变化速度会很剧烈，会导致高方差的现象；
- 当$\sigma^2$过大时，$f$核函数的变化速度会很平缓，会导致高偏差的现象。

当训练集的特征值不是特别多，而且有大量的样本数量时可以选择高斯核函数。

当使用高斯核函数时，一个很重要的操作就是特征值的缩放，因为计算相似度时，会计算$|| x-l^{(i)} ||^2$，当每个特征值的取值范围相差很大时，求得的范数会受范围大的特征值的影响。为了避免这种情况，需要缩放到相近的范围内。

 
## 多分类问题

在处理多元分类问题时，与逻辑分类和神经网络类似，也是训练多个SVM训练器，然后比较取性能最好的一个。
更方便的方式是使用第三方的库函数来实现多元分类问题。 


## 逻辑回归和SVM

定义：n = 样本的特征值数量，m = 样本的数量。

- 当n相对于m来时很大时，比如n=10000，m=1000时，这时选择逻辑回归或者不使用核函数的SVM；

- 当n比较小，m中等大小，比如n=1000，m=10000时，选择SVM（使用高斯核函数）的效果会比较好； 

- 当n比较小，m非常大时，比如n=1000，m=50000+时，这时通常手动添加更多的特征值，然后使用逻辑回归或者不使用核函数的SVM来处理。


而神经网络算法，通常都会比这两者来的慢，且需要良好的设计才能取得较好的效果。而且使用SVM时不用担心遇到局部最优的问题。
