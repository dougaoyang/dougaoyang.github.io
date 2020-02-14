---
layout: post
title: 推荐系统和协同过滤算法
date: 2020-02-08 07:00
categories:
- 技术
tags:
- 机器学习
- 非监督学习
description: 推荐系统是目前非常流行的机器学习应用。特征值对机器学习是非常重要的，而对特征值的选择会直接影响到算法的好坏，推荐系统能够自动帮助学习一些优良的特征值，帮助更好的实现算法。
mathjax: true
---

推荐系统是目前非常流行的机器学习应用。
特征值对机器学习是非常重要的，而对特征值的选择会直接影响到算法的好坏，推荐系统能够自动帮助学习一些优良的特征值，帮助更好的实现算法。

## 举例说明
以电影评分和推荐电影为例

先定义几个变量：
$n_u$=用户人数
$n_m$=电影数量
$r(i,j)=1$ 表示用户$j$评价了电影$i$
$y(i,j)$= 用户$j$对电影$i$的评分，只有在$r(i,j)=1$的时候才会有

首先电影评分分为0-5星。我们有4个用户和5部电影：

|  电影   | Alice(1) | Bob(2) | Carol(3) | Dave(4) |
|  :-  | :-  | :- | :- | :- |
| Love at last(1) | 5 | 5 | 0 | 0 |
| Romance forever(2) | 5 | ? | ? | 0 |
| Cute puppies of love(3) | ? | 4 | 0 | ? |
| Nonstop car chases(4) | 0 | 0 | 5 | 4 |
| Swords vs. karate(5) | 0 | 0 | 5 | ? |

上表中$n_u=4,n_m=5$，电影$i=1,2,3$为爱情片，$i=4,5$为动作片，打问号的表示没有评分。

上面的表格中可以看到Alice和Bob对爱情电影评分很高，对动作片评分很低，Carol和Dave则相反。

现在给每部电影添加两个特征值：$x_1$表示浪漫指数，$x_2$表示动作指数：

|  电影   | Alice(1) | Bob(2) | Carol(3) | Dave(4) | $x_1$(浪漫) | $x_2$(动作) |
|  ----  | ----  | ---- | ---- | ---- | ---- | ---- |
| Love at last(1) | 5 | 5 | 0 | 0 | 0.9 | 0 |
| Romance forever(2) | 5 | ? | ? | 0 | 1 | 0.01 |
| Cute puppies of love(3) | ? | 4 | 0 | ? | 0.99 | 0 |
| Nonstop car chases(4) | 0 | 0 | 5 | 4 | 0.1 | 1 |
| Swords vs. karate(5) | 0 | 0 | 5 | ? | 0 | 1 |

用矩阵的形式来表示每个电影的特征值:
$ x^{(1)}=\left[ \begin{matrix} 0 \\\ 0.9 \\\ 0  \end{matrix} \right],
  x^{(2)}=\left[ \begin{matrix} 0 \\\ 1 \\\ 0.01  \end{matrix} \right],
  x^{(3)}=\left[ \begin{matrix} 0 \\\ 0.99 \\\ 0  \end{matrix} \right],
  x^{(4)}=\left[ \begin{matrix} 0 \\\ 0.1 \\\ 1  \end{matrix} \right],
  x^{(5)}=\left[ \begin{matrix} 0 \\\ 0 \\\ 1  \end{matrix} \right] $

想要预测问号的值，这是一个线性回归的问题。
对于用户$j$来说，要预测他对电影$i$的评分值，应用线性回归的模型，当通过算法获得来一个参数$\theta^{(j)}$，通过这个参数，计算$(\theta^{(j)})^T \cdot x^{(i)}$，即可预测出评分值。

假设要预测用户1对电影3的评分：用户1的参数$\theta^{(1)} = \left[ \begin{matrix} 0 \\\ 5 \\\ 0  \end{matrix} \right]$，计算他对电影3的评分：$(\theta^{(1)})^T \cdot x^{(3)} = 4.95$，即可预测他的评分为5星。

下面就是对每个用户，应用线性回归模型即可预测出他们对电影的评分。

用公式来表示一下：
对一个用户$j$，他的线性回归公式：
$$ min_{\theta^{(j)}} = \frac{1}{2m^{(j)}} \sum_{i:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} - y^{(i,j)})^2 + \frac{\lambda}{2m^{(j)}}\sum_{k=1}^n(\theta_k^{(j)})^2 $$
这就是常用的线性回归模型。

下面在公式上约去常数$m^{(j)}$项，这并不影响最小化代价函数：
$$ min_{\theta^{(j)}} = \frac{1}{2} \sum_{i:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} - y^{(i,j)})^2 + \frac{\lambda}{2}\sum_{k=1}^n(\theta_k^{(j)})^2 $$

然后计算所有用户加在一起的代价函数公式：
$$ min_{\theta^{(1)},...,\theta^{(n_u)}} = \frac{1}{2} \sum_{j=1}^{n_u} \sum_{i:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} - y^{(i,j)})^2 + \frac{\lambda}{2}\sum_{j=1}^{n_u}\sum_{k=1}^n(\theta_k^{(j)})^2 $$

对该公式应用梯度下降求最小值：
当$k=0$：
$$ \theta_k^{(j)} := \theta_k^{(j)} - \alpha \left(  \sum_{i:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} - y^{(i,j)})x_k^{(i)} \right) $$

当$k \not= 0$：
$$ \theta_k^{(j)} := \theta_k^{(j)} - \alpha \left(  \sum_{i:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} - y^{(i,j)})x_k^{(i)} + \lambda\theta_k^{(j)} \right) $$

## 协同过滤（Collaborative Filtering）

在一个电影网站中，很难去获得一部电影的浪漫指数和动作指数是多少，这个参数很难人为的去判断。为了解决这个问题，可以使用特征寻找器（*feature finders.*）

现在我们不知道电影的特征值是多少,$ x^{(i)}=\left[ \begin{matrix} 0 \\\ ? \\\ ?  \end{matrix} \right]$，但是我们通过某种途径得知用户对各种类型电影的喜爱程度，是喜欢动作电影还是喜欢爱情电影。$\theta_1$表示喜欢爱情电影的参数，$\theta_2$表示喜欢动作电影的参数

|  电影   | Alice(1) | Bob(2) | Carol(3) | Dave(4) |
|  ----  | ----  | ---- | ---- | ---- |
| Love at last(1) | 5 | 5 | 0 | 0 |
| Romance forever(2) | 5 | ? | ? | 0 |
| Cute puppies of love(3) | ? | 4 | 0 | ? |
| Nonstop car chases(4) | 0 | 0 | 5 | 4 |
| Swords vs. karate(5) | 0 | 0 | 5 | ? | 
|$\theta_1$(浪漫)| 5 | 5 | 0 | 0 |
|$\theta_2$(动作)| 0 | 0 | 5 | 5 |

用矩阵的形式来表示每个用户的关于电影特征的参数值:
$ \theta^{(1)}=\left[ \begin{matrix} 0 \\\ 5 \\\ 0  \end{matrix} \right],
  \theta^{(2)}=\left[ \begin{matrix} 0 \\\ 5 \\\ 0  \end{matrix} \right],
  \theta^{(3)}=\left[ \begin{matrix} 0 \\\ 0 \\\ 5  \end{matrix} \right],
  \theta^{(4)}=\left[ \begin{matrix} 0 \\\ 0 \\\ 5  \end{matrix} \right]$

对一个电影$i$，要获得它的特征值$ x^{(i)}=\left[ \begin{matrix} ? \\\ ? \\\ ?  \end{matrix} \right]$，也可以看作一个线性回归问题。
同样的预测函数可以写作：$h = (\theta^{(j)})^T \cdot x^{(i)} = (x^{(i)})^T \cdot \theta^{(j)} $

那么对一个电影$i$，它的代价函数则是：
$$ min_{x^{(i)}} = \frac{1}{2} \sum_{j:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} - y^{(i,j)})^2 + \frac{\lambda}{2}\sum_{k=1}^n(x_k^{(i)})^2 $$

然后计算所有电影加在一起的代价函数公式：
$$ min_{x^{(1)},...,x^{(n_m)}} = \frac{1}{2}  \sum_{i=1}^{n_m} \sum_{j:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} - y^{(i,j)})^2 + \frac{\lambda}{2}\sum_{i=1}^{n_m}\sum_{k=1}^n(x_k^{(i)})^2 $$

同样的应用梯度下降来最小化代价函数。

通过上面的说明：
当我们有电影的特征值$x$时，可以预测出用户的属性$\theta$；当有用户的属性$\theta$可以预测出电影的特征值$x$，这样交替运行，就可以使系统更加完善。这就是基本的协同过滤算法。

## 算法公式

将上面的两个式子合并，同时最小化特征值和参数：
$$ J(x,\theta)=\frac{1}{2} \sum_{(i,j):r(i,j)=1}((\theta^{(j)})^Tx^{(i)} - y^{(i,j)})^2 +  \frac{\lambda}{2}\sum_{i=1}^{n_m}\sum_{k=1}^{n}(x_k^{(i)})^2  +  \frac{\lambda}{2}\sum_{j=1}^{n_u}\sum_{k=1}^{n}(\theta_k^{(j)})^2  $$

*PS：该式子中的$x$和$\theta$都是n维的向量，它们的偏差单元$x_0$和$\theta_0$都被移除了。*

### 算法步骤
1. 随机初始化$x^{(1)},...,x^{(n_m)},\theta^{(1)},...,\theta^{(n_u)}$一个很小的值。
2. 使用梯度下降算法来最小化代价函数$J$。

$$ x_k^{(i)} :=  x_k^{(i)} - \alpha \frac{\partial}{\partial x_k^{(i)} }J(x,\theta) := x_k^{(i)} - \alpha \left(  \sum_{j:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} - y^{(i,j)})x_k^{(i)} + \lambda x_k^{(i)} \right) $$

$$ \theta_k^{(j)} :=  x_k^{(i)} - \alpha \frac{\partial}{\partial \theta_k^{(j)} }J(x,\theta) := \theta_k^{(j)} - \alpha \left(  \sum_{i:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} - y^{(i,j)})x_k^{(i)} + \lambda\theta_k^{(j)} \right) $$

这样下来可以对用户尚未评分的电影，通过预测评分大小来推荐电影。
对用户已评分的电影，可以根据评分和用户的属性参数来获得更好的电影特征值。

## 其他应用

协同过滤算法还可以用来推荐相似的产品，假如当用户看了一个电影$i$之后，可以判断其他电影和该电影的相似度来推荐，相似度的公式为$||x^{(i)} - x^{(j)}||$，当该式子越小时相似度越高，就可以据此来推荐电影。

## 其他事项
在上述电影网站中，除了上面的四个用户，又有一个新用户加入，他没有对任何电影评分，要预测他对某一个电影的评分，通常采用的方法取评过该电影评分的平均值$\mu$来当作预测值。

