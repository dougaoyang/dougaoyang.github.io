---
layout: post
title: Python时间日期处理
date: 2017-09-21
categories:
- 技术
tags:
- python
- datetime
description: Python时间日期处理
---

## 引入对象

```
>>> import datetime
```

## datetime基本操作

### 1.获取datetime

```
>>> datetime.datetime.now()
datetime.datetime(2017, 9, 21, 15, 46, 32, 745801)
>>> 
>>> datetime.datetime(2017,9,11,12,23,45)
datetime.datetime(2017, 9, 11, 12, 23, 45)
```

### 2.获取date

```
>>> datetime.today()
datetime.datetime(2017, 9, 21, 15, 46, 32, 745801)
>>> 
>>> datetime.date(2017,9,11)
datetime.date(2017, 9, 11)
>>> 
>>> datetime.datetime(2017,9,11,12,23,45).date()
datetime.date(2017, 9, 11)
```

### 3.获取前N天/后N天

```
>>> datetime.date.today() - datetime.timedelta(days=1)
datetime.date(2017, 9, 20)
>>>
>>> datetime.date.today() + datetime.timedelta(days=1)
datetime.date(2017, 9, 22)
```

### 4.获取当天开始和结束时间

```
>>> datetime.datetime.combine(datetime.date.today(), datetime.time.min)
datetime.datetime(2017, 9, 21, 0, 0)
>>> datetime.datetime.combine(datetime.date.today(), datetime.time.max)
datetime.datetime(2017, 9, 21, 23, 59, 59, 999999)
```

### 5.获取本周开始/结束日期

```
>>> today = datetime.date.today()
>>> today
datetime.date(2017, 9, 21)
>>> today.weekday()
3
>>> monday = today-datetime.timedelta(today.weekday())
>>> monday
datetime.date(2017, 9, 18)
>>> sunday = today + datetime.timedelta(6 - today.weekday())
>>> sunday
datetime.date(2017, 9, 24)
```

## 关系转换

### datetime <=> string

```
>>> datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
'2017-09-21 16:03:44'
>>>
>>> datetime.datetime.strptime("2017-09-21 16:05:06", "%Y-%m-%d %H:%M:%S")
datetime.datetime(2017, 9, 21, 16, 5, 6)
```

### datetime <=> date

```
>>> datetime.datetime.now().date()
datetime.date(2017, 9, 21)
>>>
>>> today = datetime.date.today()
>>> datetime.datetime.combine(today, datetime.time())
datetime.datetime(2017, 9, 21, 0, 0)
>>> datetime.datetime.combine(today, datetime.time.min)
datetime.datetime(2017, 9, 21, 0, 0)
```

### datetime <=> timestamp

```
>>> import time
>>> now = datetime.datetime.now()
>>> now.timetuple()
time.struct_time(tm_year=2017, tm_mon=9, tm_mday=21, tm_hour=16, tm_min=16, tm_sec=48, tm_wday=3, tm_yday=264, tm_isdst=-1)
>>> time.mktime(now.timetuple())
1505981808.0
>>>
>>>datetime.datetime.fromtimestamp(1505981808.0)
datetime.datetime(2017, 9, 21, 16, 16, 48)
```

