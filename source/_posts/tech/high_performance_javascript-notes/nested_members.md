---
layout: post
title: JavaScript对象的嵌套成员对性能的影响
date: 2015-01-20
categories:
- 技术
description: 对象成员中可能会包含其他成员，例如全局对象window包含成员location，而location又包含其他成员。window.location.href，在这种写法中，没遇到一个点号，JavaScript 引擎就要在对象成员上执行一次解析过程。很明显，当成员嵌套越深，访问速度越慢。location.href总是快于 window.location.href，如果访问的不是对象的实例属性，那么，在这种写法中，没遇到一个点号，JavaScript 引擎还要在每个点上搜索原形链，这将需要更长时间。
---

## 1.前言
对象成员中可能会包含其他成员，例如全局对象window包含成员location，而location又包含其他成员。window.location.href，在这种写法中，没遇到一个点号，JavaScript 引擎就要在对象成员上执行一次解析过程。很明显，当成员嵌套越深，访问速度越慢。location.href总是快于 window.location.href，如果访问的不是对象的实例属性，那么，在这种写法中，没遇到一个点号，JavaScript 引擎还要在每个点上搜索原形链，这将需要更长时间。

## 2.缓存对象成员的值
当在一个函数里多次调用一个对象成员时，需要注意：可以将成员对象缓存起来，放入一个局部变量，将搜索次数减少为一次。

看下面这个栗子：

```JavaScript
function hasEitherClass(element, className1, className2){
    return element.className == className1 || element.className == className2;
}
function hasEitherClassByCache(element, className1, className2){
    var currentClassName = element.className;
    return currentClassName == className1 || currentClassName == className2;
}

```

可以看到`hasEitherClass`中，element.className 被访问了两次，而在这个函数中，它的值并不会改变。所以又一次多余的搜索过程。在`hasEitherClassByCache`中变量被缓存了，成员搜索只进行了一次，因此速度回有所提升。

可以比较一下结果：

```JavaScript
var el = document.getElementsByTagName('input')[0];
Test.run( '无缓存' ); 
for( var i=0; i<99999; i++ ) {
    hasEitherClass(el,'className1','className2');
}
Test.stop();
Test.run( '有缓存' ); 
for( var i=0; i<99999; i++ ) {
    hasEitherClassByCache(el,'className1','className2');
}
Test.stop(); 
Test.output( '缓存成员对速度的影响' ); 
```

![运行结果][img:1]


一般来说，如果在同一个函数中你要多次读取同一个对象属性，并且它不会发生改变，最好将它存入一个局部变量。以局部变量替代属性，避免多余的属性查找带来性能开销。在处理嵌套对象成员时这点特别重要，它们会对运行速度产生影响。



[img:1]: /images/20150121113349.jpg "运行结果"

