---
layout: post
title: Pandas的索引转换为列数据
date: 2017-09-22
categories:
- 技术
description: pandas索引放到列中
---

## 问题描述

```
>>> df = pd.DataFrame(np.arange(12).reshape((4,3)), index=[['a','a','b','b'], [1,2,1,2]], columns=['green','blue','red'])
>>> df.index.names=['key1','key2']
>>> df
           green  blue  red
key1 key2
a    1         0     1    2
     2         3     4    5
b    1         6     7    8
     2         9    10   11
```

转换成

```
  key1  key2  green  blue  red
0    a     1      0     1    2
1    a     2      3     4    5
2    b     1      6     7    8
3    b     2      9    10   11
```

## 解决问题

可以看到这是带有MultiIndex索引的DataFrame对象

首先，可以先来尝试一下普通的DataFrame:

```
>>> normal_df = pd.DataFrame(np.arange(12).reshape((4,3)), columns=['green','blue','red'])
>>> normal_df
   green  blue  red
0      0     1    2
1      3     4    5
2      6     7    8
3      9    10   11
```

最简单的：

```
>>> normal_df['index1'] = normal_df.index
>>> normal_df
   green  blue  red  index1
0      0     1    2       0
1      3     4    5       1
2      6     7    8       2
3      9    10   11       3
```

或者使用 [pandas.DataFrame.reset_index](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.reset_index.html)

```
>>> normal_df.reset_index()
   index  green  blue  red
0      0      0     1    2
1      1      3     4    5
2      2      6     7    8
3      3      9    10   11
```


现在来看最上面的DataFrame，需要使用 reset_index 方法

可以看下reset的level参数：

```
level : int, str, tuple, or list, default None
Only remove the given levels from the index. Removes all levels by default
```

该对象包含两个索引[key1, key1]，要移除索引放到列上时，就需要指定索引： 0(key1), 1(key2)

```
>>> df.reset_index(level=0)
     key1  green  blue  red
key2
1       a      0     1    2
2       a      3     4    5
1       b      6     7    8
2       b      9    10   11
>>>
>>> df.reset_index(level=1)
      key2  green  blue  red
key1
a        1      0     1    2
a        2      3     4    5
b        1      6     7    8
b        2      9    10   11
>>>
>>> df.reset_index(level='key1')
     key1  green  blue  red
key2
1       a      0     1    2
2       a      3     4    5
1       b      6     7    8
2       b      9    10   11
>>>
>>> df.reset_index(level=[0,1])
  key1  key2  green  blue  red
0    a     1      0     1    2
1    a     2      3     4    5
2    b     1      6     7    8
3    b     2      9    10   11
>>>
>>> df.reset_index(level=['key1','key2'])
  key1  key2  green  blue  red
0    a     1      0     1    2
1    a     2      3     4    5
2    b     1      6     7    8
3    b     2      9    10   11
```







