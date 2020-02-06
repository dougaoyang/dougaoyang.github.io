---
layout: post
title: Pandas中字符串和日期转换
date: 2017-09-21
categories:
- 技术
tags:
- pandas
- datetime
description: Pandas中字符串和日期转换
---

定义一个DataFrame

```
>>> data = {'state': ['US','Ohio','Nevada'], 'date':['2001-09-12 21:11:09', '2001-09-13 06:32:19', '2002-02-23 04:12:19']}
>>> df = pd.DataFrame(data)
>>> df
                  date   state
0  2001-09-12:21 11:09      US
1  2001-09-13:06 32:19    Ohio
2  2002-02-23:04 12:19  Nevada
>>> df['date']
0    2001-09-12 21:11:09
1    2001-09-13 06:32:19
2    2002-02-23 04:12:19
Name: date, dtype: object
```

可以看到date列的dtype为`object`，现在需要转换成日期格式

可以使用[to_datetime](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.to_datetime.html#pandas.to_datetime)来转换：

```
>>> pd.to_datetime(df['date'])
0   2001-09-12 21:11:09
1   2001-09-13 06:32:19
2   2002-02-23 04:12:19
Name: date, dtype: datetime64[ns]
```

Pandas已经自动将该列由`object`转成`datetime64[ns]`

