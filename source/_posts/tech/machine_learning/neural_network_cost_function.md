---
layout: post
title: 神经网络代价函数和反向传播算法
date: 2020-01-18 19:00
categories:
- 技术
tags:
- 机器学习
- 神经网络
description: 了解神经网络的代价函数，还有对代价函数求导的方法，反向传播算法的使用过程。
mathjax: true
---

<img width="50%" src="/images/ml_18.jpg" alt="多层神经网络">

定义一些参数：
$L$ = 神经网络的层数
$s_l$ = $l$层的单元数，但不包括偏差单元
$K$ = 输出层的单元数，二元分类是1，大于二的就是k

逻辑回归的代价函数为：
$$ J(\theta)=-\frac{1}{m} \sum_{i=1}^m [y^{(i)} \log(h_\theta(x^{(i)})) + (1-y^{(i)}) \log(1-h_\theta(x^{(i)}))] + \frac{\lambda}{2m} \sum_{j=1}^n \theta_j^2 $$


神经网络的代价函数：
$h_\theta(x^{(i)})$ 是一个K维向量。$(h_\theta(x))_i$是输出向量的第i个单元。

$$ J(\theta)=-\frac{1}{m} \sum_{i=1}^{m}\sum_{k=1}^{K} [y_k^{(i)}\log((h_\theta(x^{(i)}))^k) + (1-y_k^{(i)})\log(1-(h_\theta(x^{(i)}))^k) ] + \frac{\lambda}{2m} \sum_{l=1}^{L-1}\sum_{i=1}^{s_l}\sum_{j=1}^{s_{l+1}}(\theta_{ji}^{(l)})^2 $$

*PS：$(h_\theta(x^{(i)}))^k$ 这里的$k$应该是下标，这样写的原因是markdown解析不出来，囧～～*

左侧两个求和的是将输出的每个单元的回归代价相加，右侧的三次和是将所有的$\theta$单元的平方相加

## 最小化$J(\theta)$

想要计算神经网络的代价函数$J(\theta)$的最小化，需要计算$\frac{\partial}{\partial \theta_{i,j}^{(l)}} J(\theta)$

在神经网络算法中，最小化代价函数一般是使用**反向传播算法（Backpropagation）**来计算

定义一个参数：
$a_j^{(l)}$ = 第$l$层，第$j$个节点的激励函数返回值。
$\delta_j^{(l)}$ = 第$l$层，第$j$个节点的误差。

### 计算过程

1. #### 初始化$\Delta_{i,j}^{(l)}=0$，每一层的每一个元素都是0；
2. #### 设置 $a^{(1)}=x^{(i)}$；
3. #### 正向计算每层的单元$a^{(l)}$，$l$=2,3,...,L

最上面的神经网络模型为例：
$a^{(1)}=x$
$z^{(2)}=\Theta^{(1)}a^{(1)}$
$a^{(2)}=g(z^{(2)})$，添加$a_0^{(2)}=1$
$z^{(3)}=\Theta^{(2)}a^{(2)}$
$a^{(3)}=g(z^{(3)})$，添加$a_0^{(3)}=1$
$z^{(4)}=\Theta^{(3)}a^{(3)}$
$a^{(4)}=h_\theta(x)=g(z^{(4)})$

4. #### 计算L层（输出层）误差$\delta^{(L)}=a^{(L)}-y^{(i)}$

$y^{(i)}$是训练集的真实输出值。上例中，$\delta^{(4)}=a^{(4)}-y^{(i)}$

5. #### 计算L-1,L-2,...,2层误差，$\delta^{(L-1)},\delta^{(L-2)},...,\delta^{(2)}$；

L层之前的层的误差计算公式
$\delta^{(l)}=(\Theta^{(l)})^T\delta^{(l+1)} \cdot*g\prime(z^{(l)}),l \in [2,3,...L-1]$

其中$g\prime(z^{(l)})=a^{(l)} \cdot* (1-a^{(l)})$
因此误差计算公式等于：
$$ \delta^{(l)}=(\Theta^{(l)})^T\delta^{(l+1)} \cdot* a^{(l)} \cdot* (1-a^{(l)}),l \in [2,3,...L-1] $$
这里没有第一层误差，因为第一层就是输入值，不存在误差。

6. #### 计算$\Delta$，$\Delta_{i,j}^{(l)} := \Delta_{i,j}^{(l)} + a_j^{(i)}\delta_i^{(l+1)}$，用向量方式表示$\Delta^{(l)} := \Delta^{(l)} + \delta^{(l+1)}(a^{(l)})^T $，其中$l \in [1,2,3,...,L-1]$

7. #### 在所有的样本集中，重复2-6步骤，计算出$\Delta^{(l)}$;

8. #### 这里要加上正则化的过程：

$D_{i,j}^{(l)} = \frac{1}{m}\Delta_{i,j}^{(l)}, j=0$
$D_{i,j}^{(l)} = \frac{1}{m}(\Delta_{i,j}^{(l)} + \lambda\Theta_{i,j}^{(l)}), j\neq 0$

最终获得的代价函数的导数$\frac{\partial}{\partial \theta_{i,j}^{(l)}} J(\Theta)=D_{i,j}^{(l)}$
