---
layout: post
title: 查准率（Precision）和召回率（Recall）
date: 2020-01-27 14:00
categories:
- 技术
tags:
- 机器学习
description: 如何解决偏斜类问题，查准率和召回率的定义以及使用，以及使用F值来判断一个学习算法的性能。
mathjax: true
---

在处理分类问题时，会遇到一种情况：

假设一个二元分类问题：假设我们的预测算法是：$h_\theta(x)=0$，这个算法忽略特征值$x$，不管任何情况下都是预测$h_\theta(x)$等于0。

毫无疑问这是一个糟糕的算法，但是在测试集中，99%的样本输出$y=1$，1%的样本输出$y=0$，这样计算预测算法的误差率的时候，会的到1%的误差率，这就是很糟糕的情况，一个完全错误的算法得到了一个正确率很高的测试结果。

这种情况称之为**偏斜类（Skewed Classes）**的问题。

## 解决问题的办法

处理这种情况，需要参考查准率和召回率

<table border="1">
    <tr>
        <th colspan="2" rowspan="2"></th>
        <th colspan="2" align="center">真实结果</th>
    </tr>
    <tr>
        <td>1</td>
        <td>0</td>
    </tr>
    <tr><th rowspan="4">预测结果</th></tr>
    <tr>
        <td>1</td>
        <td>TP(真阳性)</td>
        <td>FP(假阳性)</td>
    </tr>
    <tr>
        <td>0</td>
        <td>FN(假阴性)</td>
        <td>TN(真阴性)</td>
    </tr>
</table>

上图的表格中，提到了四个概念。
**TP(真阳性)** 预测为真的样本中确实为真的数量。
**FP(假阳性)** 预测为真的样本中确实为假的数量。
**FN(假阴性)** 预测为假的样本中确实为真的数量。
**TN(真阴性)** 预测为假的样本中确实为假的数量。

举个例子来说明

预测某些病人有没有得癌症。
假设有100个样本，真实情况是有10个得癌症的，通过预测函数遇到到了有12个得了癌症，其中有8个是真实得癌症的。
这种情况下:
TP=8
FP=12-8=4
FN=10-8=2
TN=(100-12)-2=86

## 准确率 Accuracy
正确预测为1，正确预测为0的样本比率，公式为：$\frac{TP+TN}{ALL}$
上例中准确率为 $\frac{8+86}{100}=0.94$

## 查准率 Precision

查准率是指在所有预测为1的样本中预测正确的比率，公式为：$\frac{TP}{TP+FP}$
上例中查准率为 $\frac{8}{8+4}=0.667$

## 召回率 Recall

召回率是指在所有真正为1的样本中预测正确的比率，公式为：$\frac{TP}{TP+FN}$
上例中召回率为 $\frac{8}{8+2}=0.8$

在最开始偏斜类问题中 TP=0，召回率为0，因此那个预测算法是错误的。

## 查准率和召回率的关系

在分类问题中，$h_\theta(x) \geq 0.5$ 是我们就预测为1，$h_\theta(x) < 0.5$ 是我们就预测为0；

边界条件就是0.5
当提高边界值时，即$h_\theta(x) \geq 0.7$，查准率会提高，召回率会下降；
当减小边界值时，即$h_\theta(x) \geq 0.3$，召回率会提高，查准率会下降。

<img width="50%" src="/images/ml_23.jpg" alt="">

查准率和召回率之间的变化关系和上图类似，变化的曲线可能不是上图的平滑关系。大方向两者是相反的增长。

## 判断一个学习算法的性能

![][1]

要判断一个学习学习算法需要综合考虑查准率和召回率，可以使用 **F值（F-Score）** 来综合评价。
公式为：2$\frac{P*R}{P+R}$

通过上面，可以得出算法1的性能比较好。



[1]: /images/ml_24.jpg


