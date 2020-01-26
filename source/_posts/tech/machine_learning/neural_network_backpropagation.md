---
layout: post
title: 神经网络的反向传播算法的使用
date: 2020-01-20 21:00
categories:
- 技术
description: 在使用神经网络时的一些操作，比如参数展开，还有随机初始化参数以及使用梯度校验验证反向传播的正确性。
mathjax: true
---

## 参数展开

在神经网络中，我们有一些参数矩阵，例如：
$\Theta^{(1)},\Theta^{(2)},\Theta^{(3)}...$
$D^{(1)},D^{(2)},D^{(3)}...$

有时为了使用方便，需要将这些矩阵统一到一个向量中去

```Matlab
thetaVector = [ Theta1(:); Theta2(:); Theta3(:); ]
deltaVector = [ D1(:); D2(:); D3(:) ]
```

这样就合并了三个矩阵，
假如$\Theta^{(1)}$ 是一个$5 \times 6$的矩阵，$\Theta^{(2)}$是一个$5 \times 6$的矩阵，$\Theta^{(3)}$是一个$1 \times 6$的矩阵
则 thetaVector 是一个$66 \times 1$的矩阵

当要使用时，同样要将这一个向量，拆分成三个矩阵：

```Matlab
Theta1 = reshape(thetaVector(1:30),5,6)
Theta2 = reshape(thetaVector(31:60),5,6)
Theta3 = reshape(thetaVector(61:66),1,6)
```

## 随机初始化

在逻辑回归里，使用梯度下降算法时，需要初始化$\Theta$，这里可以设置成零向量$n\times 1$，元素全为0的向量。
但是在神经网络里，这样做不行。

举例说明，假设$\Theta_{i,j}^{(l)}=0$
$ a_1^{(2)} = g(\theta_{10}^{(1)}x_0+\theta_{11}^{(1)}x_1+\theta_{12}^{(1)}x_2+\theta_{13}^{(1)}x_3)=g(0) $
$ a_2^{(2)} = g(\theta_{20}^{(1)}x_0+\theta_{21}^{(1)}x_1+\theta_{22}^{(1)}x_2+\theta_{23}^{(1)}x_3)=g(0) $
...

这样$a_1^{(2)}=a_2^{(2)}=a_{s_l}^{(2)}$

当反向传播时$\delta^{(l)}$会发现每层的每个节点都是一样的，这样算法就会出现对称问题。
因此初始化的时候要随机一些初始值。初始化不同的随机址会打破对称问题。

将$\Theta_{i,j}^{(l)}$内每个值都随机在$[-\epsilon, \epsilon]$ 之间。（$-\epsilon \leq \Theta_{i,j}^{(l)} \leq \epsilon$）

例如:
Theta1 = rand(10,11) * (2*init_epsilon) - init_epsilon;
Theta2 = rand(1,11) * (2*init_epsilon) - init_epsilon;

rand(x,y)是一个初始化一个$x \times y$ 维的，其内之在0, 1之间的随机值。

## 梯度检验（Gradient Checking）

在使用反向传播算法时，因为反向传播有很多复杂的细节，这些细节会导致一些bug，虽然代价函数的值在减小，但是可能的结果和实际的还是有很大误差。
梯度检验可以减少这种错误的概率。

在数学上
$\frac{\partial}{\partial \Theta} J(\Theta) \approx \frac{J(\Theta+\epsilon) + J(\Theta-\epsilon)}{2\epsilon}$

*PS：$\epsilon$这个和初始化随机的参数不一样*

$\epsilon$ 是一个很小的值，例如$10^{-4}$


对于多个$\Theta$

$\frac{\partial}{\partial \Theta_j^{(l)}} J(\Theta) \approx \frac{J(\Theta_1,...,\Theta_j+\epsilon,...,\Theta_n) + J(\Theta_1,...,\Theta_j-\epsilon,...,\Theta_n)}{2\epsilon}$

```Matlab
epsilon = 1e-4;
for i = 1:n,
  thetaPlus = theta;
  thetaPlus(i) += epsilon;
  thetaMinus = theta;
  thetaMinus(i) -= epsilon;
  gradApprox(i) = (J(thetaPlus) - J(thetaMinus))/(2*epsilon)
end;
```

将反向传播计算的DVec和梯度校验计算的gradApprox比较一下，两者一致则说明算法没有问题。但是在最终使用反向传播取训练样本集时，关掉梯度校验，因为这个非常耗时。


## 隐藏层

每个隐藏层的单元数理论上越多准确度越好，但是实际上为了和计算成本平衡，需要选择何时的隐藏层单元数量。一般隐藏层单元数目稍大于输入层的特征值数。
隐藏层的数量越多，算法准确度越高，相应的计算成本也会增高，一般来说1个隐藏层。如果有多个隐藏层，建议在每个隐藏层中使用相同数量的单元。

