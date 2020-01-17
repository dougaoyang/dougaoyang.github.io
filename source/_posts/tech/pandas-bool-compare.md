---
layout: post
title: 'Pandas逻辑运算: The truth value of a Series is ambiguous'
date: 2017-09-22
categories:
- 技术
description: pandas对于逻辑运算的解析
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
>>> df[(df['pop']>3) and (df['pop']<4)]
ValueError: The truth value of a Series is ambiguous. Use a.empty, a.bool(), a.item(), a.any() or a.all().
```

## 解决问题

原因是由于在python中 `or` 和 `and` 的声明需要 `truth-values`, 即真实的True或者False

但是`df['pop']>3`返回并不是True或False，而仍然是一个Series，所以在pandas中这样使用被认为是不明确(ambiguous)


因此需要使用位运算符(bitwise)即 `|`(or) 或者 `&`(and):

```
>>> df[(df['pop']>3) & (df['pop']<4)]
   pop   state
2  3.2  Nevada
```

---

## 附加说明

如果想实现 and 或者 or 的效果，可以使用 [logical_or][logical_or] 和 [logical_and][logical_and] 或者 位运算符(bitwise)即  `|` (or) 和 `&` (and)

- logical_or:

```
>>> import numpy as np
>>> np.logical_or(x, y)
```

或者 `|` 运算符

```
>>> x | y
```

- logical_and:

```
>>> import numpy as np
>>> np.logical_and(x, y)
```

或者 `&` 运算符

```
>>> x & y
```

当使用位运算符时，因为运算符优先级的问题，请将两边用括号包起来。[查看详情](https://docs.python.org/3/reference/expressions.html#operator-precedence)

除此之外还有 一些其他逻辑操作 `logical_not` 和 `logical_xor`... [查看详情](https://docs.scipy.org/doc/numpy/reference/routines.logic.html)

---

## 注意

当使用`bool`来操作`pandas.Series`时会返回一个异常：

```
>>> x = pd.Series([1])
>>> bool(x)
ValueError: The truth value of a Series is ambiguous. Use a.empty, a.bool(), a.item(), a.any() or a.all().
```

下列操作也会抛出异常：

```
>>> x or x
ValueError: The truth value of a Series is ambiguous. Use a.empty, a.bool(), a.item(), a.any() or a.all().
>>> x and x
ValueError: The truth value of a Series is ambiguous. Use a.empty, a.bool(), a.item(), a.any() or a.all().
>>> if x:
...     print('fun')
ValueError: The truth value of a Series is ambiguous. Use a.empty, a.bool(), a.item(), a.any() or a.all().
>>> while x:
...     print('fun')
ValueError: The truth value of a Series is ambiguous. Use a.empty, a.bool(), a.item(), a.any() or a.all().
```

这些隐式调用其实也是将之转为bool类型，因此仍会错误，除了上述4个声明外，还有一些操作会产生同样的效果(类似any, all, filter, ...)


[logical_or]: https://docs.scipy.org/doc/numpy/reference/generated/numpy.logical_or.html
[logical_and]: https://docs.scipy.org/doc/numpy/reference/generated/numpy.logical_and.html
