---
layout: post
title: JavaScript在访问和操作DOM对象的注意点
date: 2015-01-22
categories:
- 技术
description: 在浏览器中，DOM实现和JavaScript实现是保持相互独立的，这意味着两个独立的部分以功能接口连接就会带来性能损耗。每当 JavaScript 要访问DOM时，就需要调用接口。这个过程中就会产生性能损耗，次数越多，损耗越大。我们应该减少JavaScript 访问DOM的次数，尽量在JavaScript部分完成。
---

## 1.前言
在浏览器中，DOM实现和JavaScript实现是保持相互独立的，这意味着两个独立的部分以功能接口连接就会带来性能损耗。每当 JavaScript 要访问DOM时，就需要调用接口。这个过程中就会产生性能损耗，次数越多，损耗越大。我们应该减少JavaScript 访问DOM的次数，尽量在JavaScript部分完成。

## 2.DOM 访问和修改
每当 JavaScript 要访问DOM时，会产生性能损耗，而访问或修改元素最坏的情况是使用循环执行此操作，特别是在 HTML 集合中使用循环。

```JavaScript
function innerHTMLLoop() {
    for (var count = 0; count < 10000; count++) {
        document.getElementById('here').innerHTML += 'a';
    }
}

function innerHTMLLoop2() {
    var content = '';
    for (var count = 0; count < 10000; count++) {
        content += 'a';
    }
    document.getElementById('here').innerHTML += content;
}
```

上面的例子中，`innerHTMLLoop` 循环更新页面的内容，并且是访问两次，一次读取，一次写入。`innerHTMLLoop2` 使用局部变量存储更新后的内容，在循环结束时一次性写入。两种写法当然是第二种更有效率

![运行结果][img:1]

可以看到简直是‘惨不忍睹’啊。这些结果清楚地表明，你访问 DOM 越多，代码的执行速度就越慢。因此，一般经验法则是：减少访问的次数，尽量在JavaScript中完成操作。

## 3.HTML集合
HTML 集合是用于存放 DOM 节点引用的类数组对象。下列函数的返回值就是一个集合：

- document.getElementsByName()
- document.getElementsByClassName()
- document.getElementsByTagName()

下列属性也属于 HTML 集合：

- document.images             页面中所有的`<img>`元素
- document.links              页面中所有的`<a>`元素
- document.forms              页面中所有的表单元素
- document.forms[0].elements  页面中第一个表单的所有字段

这些方法和属性返回 HTMLCollection 对象，是一种类似数组的列表。它们不是数组（因为它们没有诸如 push()或 slice()之类的方法），但是提供了一个 length 属性，和数组一样你可以使用索引访问列表中的元素。

HTML 集合是一个“虚拟存在，意味着当底层文档更新时，它们将自动更新”。

HTML 集合实际上在查询文档。 当更新文档时，每次都要重复执行这种查询操作。例如读取集合中元素的数目（也就是集合的 length）。这正是低效率的来源。

### 3.1 集合的循环

```JavaScript
var alldivs = document.getElementsByTagName('div');
for (var i = 0; i < alldivs.length; i++) {
    document.body.appendChild(document.createElement('div'))
}
```

注意这段代码是一个错误的代码，它并不是简单地遍历页面的div元素，然后添加一个新的div，它是一个死循环，因为alldivs.length在每次迭代循环中会增加，它反映了底层文档的当前状态。

因此，不建议用集合的length属性做循环条件，因为它意味着每次都要重新运行查询过程。可以将一个集合 coll 拷贝到数组arr 中，或者用一个变量，len将length属性缓存起来。

```JavaScript
function toArray(coll) {
    for (var i = 0, a = [], len = coll.length; i < len; i++) {
        a[i] = coll[i];
    }
    return a;
}
// 设置一个集合，并把它拷贝到一个数组：
var coll = document.getElementsByTagName('div');
var arr = toArray(coll);
function loopCollection() {
    for (var count = 0; count < coll.length; count++) {
    }
}
function loopCopiedArray() {
    for (var count = 0; count < arr.length; count++) {
    }
}
function loopCacheLengthCollection() {
    var coll = document.getElementsByTagName('div'),
    len = coll.length;
    for (var count = 0; count < len; count++) {
    }
}
```

上面的例子中采用了三种循环方式：`loopCollection`每次迭代访问集合的length属性时，集合都会更新，会产生明显的性能损耗。`loopCopiedArray`则是将集合 coll 拷贝到数组arr 中，这样就避免了访问集合。`loopCacheLengthCollection`是将集合的 length 属性缓存到一个变量中，然后在循环判断条件中使用这个变量，这样也避免了访问集合。

后两种的运行速度一样快

在chrome下循环100,000个节点

![运行结果][img:2]

### 3.2 使用局部变量访问集合元素

一般来说，对于任何类型的 DOM 访问，如果同一个 DOM 属性或方法被访问一次以上，最好使用一个局部变量缓存此 DOM 成员。当遍历一个集合时，第一个优化是将集合引用存储于局部变量，并在循环之外缓存 length 属性。然后，如果在循环体中多次访问同一个集合元素，那么使用局部变量缓存它。

__速度最慢__

```JavaScript
function collectionGlobal() {
    var coll = document.getElementsByTagName('div'),
    len = coll.length,
    name = '';
    for (var count = 0; count < len; count++) {
        name = document.getElementsByTagName('div')[count].nodeName;
        name = document.getElementsByTagName('div')[count].nodeType;
        name = document.getElementsByTagName('div')[count].tagName;
    }
    return name;
};
```

__速度有所提升__

```JavaScript
function collectionLocal() {
    var coll = document.getElementsByTagName('div'),
    len = coll.length,
    name = '';
    for (var count = 0; count < len; count++) {
        name = coll[count].nodeName;
        name = coll[count].nodeType;
        name = coll[count].tagName;
    }
    return name;
};
```

__速度最快__

```JavaScript
function collectionNodesLocal() {
    var coll = document.getElementsByTagName_r('div'),
    len = coll.length,
    name = '',
    el = null;
    for (var count = 0; count < len; count++) {
        el = coll[count];
        name = el.nodeName;
        name = el.nodeType;
        name = el.tagName;
    }
    return name;
};
```

因此，访问集合时，通过局部引用访问集合可以带来速度提升；当多次访问时，使用缓存集合项来提升性能。

## 4.页面重绘和重排版

当浏览器下载完所有页面 HTML 标记， JavaScript， CSS，图片之后，它解析文件并创建两个内部数据结构：

- 一棵 DOM 树：表示页面结构
- 一棵渲染树：表示 DOM 节点如何显示

渲染树中为每个需要显示的 DOM 树节点存放至少一个节点，而隐藏的DOM元素则没有。渲染树上的节点称为“框”或者“盒”，符合 CSS 模型的定义，将页面元素看作一个具有填充、边距、边框和位置的盒。一旦 DOM 树和渲染树构造完毕，浏览器就可以显示（绘制）页面上的元素了。

当 DOM 改变影响到元素的几何属性（宽和高），浏览器需要重新计算元素的几何属性，而且其他元素的几何属性和位置也会因此改变受到影响。浏览器使渲染树上受到影响的部分失效，然后重构渲染树。这个过程被称作重排版。

不是所有的 DOM 改变都会影响几何属性。例如，改变一个元素的背景颜色不会影响它的宽度或高度。在这种情况下，只需要重绘（不需要重排版），因为元素的布局没有改变。

重绘和重排版是负担很重的操作，可能导致网页应用的用户界面失去相应。所以，十分有必要尽可能减少这类事情的发生。

### 4.1 何时页面重排版

在下述情况中会发生重排版：

- 添加或删除可见的 DOM 元素
- 元素位置改变
- 元素尺寸改变（因为边距，填充，边框宽度，宽度，高度等属性改变）
- 内容改变，例如，文本改变或图片被另一个不同尺寸的所替代
- 最初的页面渲染
- 浏览器窗口改变尺寸


### 4.2 查询元素导致刷新渲染树改变

在大多数浏览器中，是通过队列化的方式来修改和批量显示优化重排版。但是还有一些操作（获取布局信息）会导致强迫队列刷新，并立即重排版：

- offsetTop, offsetLeft, offsetWidth, offsetHeight
- scrollTop, scrollLeft, scrollWidth, scrollHeight
- clientTop, clientLeft, clientWidth, clientHeight
- getComputedStyle() (currentStyle in IE)（在 IE 中此函数称为 currentStyle）

获取布局信息需要返回最新的数据， 所以浏览器不得不立即运行渲染队列中待改变的项目并重新排版以返回正确的值。

因此，当在改变页面样式时，最好不要使用前面列出的那些属性。无论你的操作是否改变的页面的布局信息，都将刷新渲染队列，导致重排版。

一个栗子：

__分次重排版__

```JavaScript
var computed,
tmp = '',
bodystyle = document.body.style;
if (document.body.currentStyle) { // IE, Opera
    computed = document.body.currentStyle;
} else { // W3C
    computed = document.defaultView.getComputedStyle(document.body, '');
}

bodystyle.color = 'red';
tmp = computed.backgroundColor;
bodystyle.color = 'white';
tmp = computed.backgroundImage;
bodystyle.color = 'green';
tmp = computed.backgroundAttachment;
```

__统一重排版__

```JavaScript
bodystyle.color = 'red';
bodystyle.color = 'white';
bodystyle.color = 'green';
tmp = computed.backgroundColor;
tmp = computed.backgroundImage;
tmp = computed.backgroundAttachment;
```

在第一个例子中，body 元素的前景色被改变了三次，每次改变之后，都访问 computed 的风格。访问的属性 backgroundColor, backgroundImage, 和 backgroundAttachment 与颜色改变无关。但是，浏览器仍然需要刷新渲染队列并重排版，因为 computed 的风格被查询而引发。

第二个例子，则是在所有改变都完成以后，再访问 computed 的风格，因此性能会得到提升：

![运行结果][img:3]


### 4.3 最小化重绘和重排版

重排版和重绘代价昂贵，所以，提高程序响应速度一个好策略是减少此类操作发生的机会。为减少发生次数，将多个 DOM 和风格改变合并到一个批次中一次性执行。

当需要对 DOM 元素进行多次修改时，你可以通过以下步骤减少重绘和重排版的次数：

1. 从文档流中摘除该元素
2. 对其应用多重改变
3. 然后将元素带回文档中

在此过程引发两次重排版——第一步引发一次，第三步引发一次。如果你忽略了这两个步骤，那么第二步中每次改变都将引发一次重排版。

有三种办法可以在文档流中摘除元素

1. 隐藏元素，进行修改，然后再显示它。
2. 使用一个文档片断在已存 DOM 之外创建一个子树，然后将它拷贝到文档中。
3. 将原始元素拷贝到一个脱离文档的节点中，修改副本，然后覆盖原始元素。

第一种方法很简单，改变 display 属性即可

第二种方法：文档片断是一个轻量级的 document 对象，它被设计专用于更新、移动节点之类的任务。  

```JavaScript
var fragment = document.createDocumentFragment();
appendDataToElement(fragment, data);
document.getElementById('mylist').appendChild(fragment);
```

第三种方法：

```JavaScript
var old = document.getElementById('mylist');
var clone = old.cloneNode(true);
appendDataToElement(clone, data);
old.parentNode.replaceChild(clone, old);
```

推荐尽可能使用文档片断（第二种解决方案）因为它涉及最少数量的 DOM 操作和重排版。

## 5.缓冲布局信息

浏览器通过队列化修改和批量运行的方法，尽量减少重排版次数。当查询布局信息如偏移量、滚动条位置，或风格属性时，浏览器刷队列并执行一些修改操作，以返回最新的数值。最好是尽量减少对布局信息的查询次数，查询时将它赋给局部变量，并用局部变量参与计算。

## 6.将元素提出动画流

在动画处理中使用绝对坐标，避免修改时对页面其他元素的影响

使用绝对坐标定位页面动画的元素，使它位于页面布局流之外。当它的尺寸改变时，就不会推移页面中其他元素的位置，而只是覆盖其他元素。

启动元素动画。当它扩大时，其他元素的坐标并没有改变，换句话说，其他元素并没有因为“动画元素”的扩大而随之下移，而是任由动画元素覆盖。

动画结束时，将其他元素的位置下移到动画元素下方，界面“跳”了一下。

## 7.总结

DOM 访问和操作是现代网页应用中很重要的一部分。Javascript每次访问DOM时都会产生性能消耗。为减少 DOM 访问和操作中的性能损失，要注意以下几点：

- 最小化 DOM 访问，在 JavaScript 端做尽可能多的事情。
- 在反复访问的地方使用局部变量存放 DOM 引用.
- 将集合的 length 属性缓存到一个变量中，在迭代中使用这个变量。如果经常操作这个集合，可以将集合拷贝到数组中。
- 注意重绘和重排版；批量修改风格，离线操作 DOM 树，缓存并减少对布局信息的访问。
- 动画中使用绝对坐标，使用拖放代理。


[img:1]: /images/20150122144500.png "运行结果"
[img:2]: /images/20150122154637.png "运行结果"
[img:3]: /images/20150122162931.png "运行结果"

