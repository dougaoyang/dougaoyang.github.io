---
layout: post
title: K均值算法介绍
date: 2020-02-06 13:00
categories:
- 技术
tags:
- 聚类
- 非监督学习
description: 了解非监督学习的概念，以及聚类算法中常用的K均值算法的使用。
mathjax: true
---

从没有标记过的数据中学习称之为非监督学习。
在非监督学习中，通过算法来定义一些数据的结构，将数据分别聚合到这些子集中，这种算法称之为聚类算法。

K均值 (K-means) 算法是最常用的一种聚类算法。

## K-means算法

$[x^{(1)},x^{(2)},x^{(3)},...,x^{(m)}]$
假设有如上的数据集，可以看到只有输入$x$，没有输出$y$。

下面说明一下K均值算法的过程

1. 首先确认聚类的簇的个数，$K=?$
2. 随机初始化K个聚类的中心点，$\mu_1,\mu_2,...,\mu_k$
3. 集群分配：将所有的数据集分配到里离它最近的聚类中心，并用$c^{(i)}$表示聚类中心的索引。例如第3个样本$x^{(3)}$，离它最近的一个聚类中心是第2个($\mu_2$)，那个$c^{(3)}=2$
*通过比较$||x^{(i)}-\mu_k||^2$的大小，算出$x^{(i)}$距离最近的中心点。*
4. 移动质心：每个聚类中心所分配到的样本，求出这些样本的平均值，然后将原来的聚类中心移到新的平均值点上。
5. 重复3和4步骤，直到找到正确的聚类的簇。

## 优化目标

### 参数说明：
- $c^{(i)}$=样本$x^{(i)}$所分配到的聚类中心点的索引
- $\mu_k$=第k个聚类中心
- $\mu_{c^{(i)}}$=样本$x^{(i)}$所分配到的聚类中心点

K均值算法的代价函数为：
$$ J(c^{(1)},...,c^{(m)},\mu_1,...\mu_k) = \frac{1}{m}\sum_{i=1}^m ||x^{(i)}-\mu_{c^{(i)}}||^2 $$

优化目标就是使用上面的代价函数最小化所有参数。

上述步骤中
第3步集群分配，是通过找到离样本最近的聚类中心点来最小化代价函数；
第4步移动质心，是通过改变样本和聚类中心点的距离来最小代价函数。
**在K均值算法中，代价函数是一直下降的，不可能出现上升的情况。**

## 初始化聚类中心

聚类中心的个数 $K$一般都是小于样本数量$m$的，因此可以随机取$K$个样本来作为聚类中心。

步骤
- 确保聚类中心个数小于样本数量；
- 随机取K个样本；
- 将K个样本分配到聚类中心$\mu_1,...\mu_k$.

这样做的优点是方便快捷，缺点是不一定能够找到最佳的聚类中心，容易陷入局部最优。
这种陷入局部最优的情况在聚类中心过少时一般会出现，一般在$K<10$的情况下，解决办法是多次执行该步骤，比较代价函数的值，取最小值。

## 聚类中心数量的选择

聚类中心数量的选择没有固定的方法，跟主观上的判断有很大关系，也跟业务，以及一些客观条件，以及使用K均值算法的目标有关。
