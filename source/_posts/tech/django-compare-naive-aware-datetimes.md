---
layout: post
title: "Django datetime: can't compare offset-naive and offset-aware datetimes"
date: 2017-09-22
categories:
- 技术
tags:
- Django
- datetime
description: django中timezones问题offset-naive和offset-aware
---

## 问题描述

```
>>> import datetime
>>> from django.utils import timezone
>>> naive = datetime.datetime.utcnow()
>>> aware = timezone.now()
>>> naive == aware
Traceback (most recent call last):
...
TypeError: can't compare offset-naive and offset-aware datetimes
```

## 解决问题

当遇到这种问题，首先要了解一下 `offset-naive` 和 `offset-aware` 是如何生成的

- 由Django生成的datetime，例如，从model的字段中读取的值，当Django开启时区支持*USE_TZ = True*，该值就是`aware`
- 由Python原生生成的datetime，就是`naive`

因此，问题的解决就是改变一下datetime即可，aware=>naive 或者 naive=>aware

创建一个aware datetime

```
>>> import pytz
>>> aware = datetime.datetime.utcnow().replace(tzinfo=pytz.UTC)
```

这样就生成了一个UTC时区的创建一个 aware datetime

另外一个方法

在Django中一个配置USE_TZ，该配置会影响`django.utils.timezone.now()`函数的返回，当 *USE_TZ = False* 返回 naive datetime，当 *USE_TZ = True* 返回 aware datetime

因此， 配置 *USE_TZ = False* 后即可与 datetime.timedelta 等进行计算比较等操作


