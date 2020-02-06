---
layout: post
title: JavaScript采用事件托管提高事件处理的效率
date: 2015-01-23
categories:
- 技术
tags:
- Javascript
description: 当页面中存在大量元素,而且每个元素有一个或多个事件句柄与之挂接(例如 onclick)时,可能会影响性能。连接每个句柄都是有代价的,无论其形式是加重了页面负担(更多的页面标记和 JavaScript 代码)还是表现在运行期的运行时间上。你需要访问和修改更多的 DOM 节点,程序就会更慢,特别是因为事件挂接过程都发生在 onload(或 DOMContentReady)事件中,而onload则是网页的一个繁忙时间段。挂接事件占用了处理时间,另外,浏览器需要保存每个句柄的记录,占用更多内存。当这些工作结束时,这些事件句柄中的相当一部分根本不需要(因为并不是 100%的按钮或者链接都会被用户点到),所以很多工作都是不必要的
---

## 1.前言

当页面中存在大量元素,而且每个元素有一个或多个事件句柄与之挂接(例如 onclick)时,可能会影响性能。连接每个句柄都是有代价的,无论其形式是加重了页面负担(更多的页面标记和 JavaScript 代码)还是表现在运行期的运行时间上。你需要访问和修改更多的 DOM 节点,程序就会更慢,特别是因为事件挂接过程都发生在 onload(或 DOMContentReady)事件中,而onload则是网页的一个繁忙时间段。挂接事件占用了处理时间,另外,浏览器需要保存每个句柄的记录,占用更多内存。当这些工作结束时,这些事件句柄中的相当一部分根本不需要(因为并不是 100%的按钮或者链接都会被用户点到),所以很多工作都是不必要的。

我们可以利用一个简单的方式来处理这种情况：事件托管。

## 2.事件运行的阶段

DOM事件的运行分为三个阶段

- 事件捕获
- 事件到达目标
- 事件冒泡

在IE中不支持事件捕获。

## 3.事件托管

事件托管主要依赖的是事件冒泡。一个例子：

```HTML
<html>
<head></head>
<body>
    <ul id="menu">
        <li><a href="#a">a</a></li>
        <li><a href="#b">b</a></li>
        <li><a href="#c">c</a></li>
    </ul>
</body>
</html>
```

在这个HTML中，点击`#a`标签，点击事件首先被`<a>`标签获取到，然后它沿着DOM树向上冒泡，被`li`元素收到，然后是`ul`和`div`，最后到达DOM顶层。而这时如果在它的父元素上加一个事件句柄，是可以收到它的子元素的事件通知的。

```JavaScript
document.getElementById('menu').onclick = function(e) {
    //兼容IE
    e = e || window.event;
    var target = e.target || e.srcElement;

    if (target.nodeName !== 'A') {
        return;
    }

    ... // 需要对a标签所做的处理
}
```	

当然，我们也可以在DOM最顶级元素上面添加事件，这样页面上就只有一个事件句柄，大大减少了浏览器内存的消耗，提高了运行速度。

```JavaScript
document.onclick = function(e) {
    //兼容IE
    e = e || window.event;
    var target = e.target || e.srcElement,
        targetNodeName = target.nodeName;

    if (targetNodeName == 'A') {
        // 一些处理...
    }elseif(targetNodeName == 'li'){
        // 一些处理...
    }else{
        // 一些处理...
    }
}

```

采用事件托管除了可以减少页面的句柄，提高性能。而且也能够避免获取不到ajax加载出来的元素，如果采用一般的方法给ajax加载的元素添加事件，由于添加事件是在页面的onload过程的产生的，而那是这些元素还没有加载到页面里面，因此也就获取不到了，采用事件托管可以有效的避免这种情况的发生（PS：深有感触啊！）


