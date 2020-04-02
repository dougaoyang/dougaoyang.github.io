---
layout: post
title: Pandas的行数据转换为列表
date: 2017-09-22
categories:
- 技术
tags:
- python
- pandas
description: pandas行转换成list
---

## 问题描述

```
>>> data = {'state': ['US','Ohio','Nevada'], 'pop':[2.5, 4.3, 3.2]}
>>> df = pd.DataFrame(data)
>>> df
   pop   state
0  2.5      US
1  4.3    Ohio
2  3.2  Nevada
```

现在需要转换成

```
[[2.5, 'US'], [4.3, 'Ohio'], [3.2, 'Nevada']]
```

## 解决问题

可以使用 [iterrows](http://pandas.pydata.org/pandas-docs/dev/generated/pandas.DataFrame.iterrows.html) 实现:

```
>>> temp=[]
>>> for row in df.iterrows():
...     index, data = row
...     temp.append(data.tolist())
...
>>> temp
[[2.5, 'US'], [4.3, 'Ohio'], [3.2, 'Nevada']]
```

当然这是一种办法，还有一种更 python 的办法，pandas中提供方法转换:

```
>>> df.values.tolist()
[[2.5, 'US'], [4.3, 'Ohio'], [3.2, 'Nevada']]
```


