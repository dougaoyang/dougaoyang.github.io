---
layout: post
title: html移动端自适应宽度
date: 2014-11-15
categories:
- 技术
tags:
- html
description: wap2.0网页的head里加入下面这条元标签，在iPhone的浏览器中页面将以原始大小显示，并不允许缩放...
---

## 1. 添加元标签
wap2.0网页的head里加入下面这条元标签，在iPhone的浏览器中页面将以原始大小显示，并不允许缩放。

<!--more-->

```html
<meta name="viewport" content="width=device-width,minimum-scale=1.0,maximum-scale=1.0;user-scalable=0;" />
```

## 2. 参数说明

width - viewport的宽度 

height - viewport的高度

initial-scale - 初始的缩放比例

minimum-scale - 允许用户缩放到的最小比例

maximum-scale - 允许用户缩放到的最大比例

user-scalable - 用户是否可以手动缩放
