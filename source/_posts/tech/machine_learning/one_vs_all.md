---
layout: post
title: 分类算法中多元分类
date: 2020-01-15 20:00
categories:
- 技术
tags:
- 机器学习
- 非监督学习
description: 判断垃圾邮件，这个分类问题的输出是[0, 1]，只有固定的两个输出值，这称之为二元分类问题。在生活中有许多另外的分类问题，给定一张数字的图像，判断是数字几；自动将邮件归类，归为工作，朋友，家人等等，这就是一个多元分类问题了。
mathjax: true
---

判断垃圾邮件，这个分类问题的输出是[0, 1]，只有固定的两个输出值，这称之为二元分类问题。
在生活中有许多另外的分类问题，给定一张数字的图像，判断是数字几；自动将邮件归类，归为工作，朋友，家人等等，这就是一个多元分类问题了。

原来的二元分类$y\in[0,1]$，多元分类就需要扩展一下$y\in[1,2,3,...,n]$

## 问题说明

假如我们有一组训练集$X$，输出有三项$y\in[1,2,3]$，我们可以将问题拆分成3个二元问题，在每一个问题中，预测$y$的输出值的概率；
即先预测为1的概率，再预测为2的概率，再预测为3的概率，比较三个概率的大小，取其中最大的概率，就是$y$最可能的输出值。

再一般化一点：
$y\in[1,2,3,...,n]$
y有n个输出，所以要分成n个二元问题，n个预测函数：
$h_\theta^{(1)}(x)=P(y=1|x; \theta)$ ；y=1的概率
$h_\theta^{(2)}(x)=P(y=2|x; \theta)$ ；y=2的概率
...
$h_\theta^{(n)}(x)=P(y=n|x; \theta)$ ；y=n的概率
然后比较这n个预测函数的输出值，取其最大概率的一个。

### 多元问题的输出

在实际应用上，多元问题的输出并不是一个值，而是一个向量。
例如：$y\in[1,2,3]$，$y$的输出值有三个可能，因此，$y$的输出是一个三维的向量$\left[ \begin{matrix} y_1 \\\ y_2 \\\ y_3 \end{matrix} \right]$，其中$y_1,y_2,y_3\in[0,1]$，向量的三个值都是取0和1这两个值。
比如说$y=\left[ \begin{matrix} 1 \\\ 0 \\\ 0 \end{matrix} \right]$，就是说明y最大概率的值是1；$y=\left[ \begin{matrix} 0 \\\ 0 \\\ 1 \end{matrix} \right]$，说明最大概率的值是3。

### 举例说明

在处理多元问题时，处理训练集的输出$y$时，应当将输出的实际标量转换成向量的形式：
例如，$y\in[1,2,3]$，$y$在1,2,3中选择。
我们有5个样本集，忽略输入，观察到输出为：
$y=\left[ \begin{matrix} 
2 \\\ 1 \\\ 1 \\\ 3 \\\ 2
\end{matrix} \right]$
要方便计算，将向量的各个值在转换一下：
$y=\left[ \begin{matrix} 
0&1&0 \\\ 1&0&0 \\\ 1&0&0 \\\ 0&0&1 \\\ 0&1&0
\end{matrix} \right]$
可以看出原来是一个五维的向量，每个值是标量，经过转换后每一个标量都转成一个向量，形成的一个 $5\times3$ 的矩阵，可以看到，第一行第二列的元素为1，其他为0，对应这原来y内的第一个标量2。以此类推，一一对应。

这是训练集的输出，再看一下预测值的处理，经过分类算法的计算，得出的5个样本的预测函数值为：
$h_\theta(x)=\left[ \begin{matrix} 
0.2&0.9&0.1 \\\ 0.78&0.13&0.3 \\\ 0.88&0.3&0.2 \\\ 0.2&0.1&0.92 \\\ 0.2&0.98&0.1
\end{matrix} \right]$
比较这个矩阵的每一行中三列，这三列就是三个预测函数的预测值。比较后记录下最大的值是第几列：
$h_\theta(x)=\left[ \begin{matrix} 
2 \\\ 1 \\\ 1 \\\ 3 \\\ 2
\end{matrix} \right]$
这样就训练好了一个多元分类的样本集。

