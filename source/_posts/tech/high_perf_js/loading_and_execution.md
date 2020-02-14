---
layout: post
title: JavaScript脚本在HTML页面中的加载和运行情况
date: 2015-01-15
categories:
- 技术
tags:
- javascript
description: HTML页面的加载速度与JavaScript的阻塞特性有着密切的关系，当JavaScript运行时其他的事情不能被浏览器处理，浏览器需要空闲下来等待JavaScript运行结束。
---

## 1.前言
HTML页面的加载速度与JavaScript的阻塞特性有着密切的关系，当JavaScript运行时其他的事情不能被浏览器处理，浏览器需要空闲下来等待JavaScript运行结束。

`<script>`标签的出现使整个页面因脚本解析、运行而出现等待。不论实际的JavaScript 代码是内联的还是包含在一个不相干的外部文件中，页面下载和解析过程必须停下，等待脚本完成这些处理，然后才能继续。这是页面生命周期必不可少的部分，因为脚本可能在运行过程中修改页面内容。

## 2.脚本位置
众所周知，一个`<script>`标签可以放在HTML文档的`<head>`或`<body>`标签中，并且可以多次出现。而脚本放置的位置对页面的加载速度有着重要的影响。

首先看一个例子：

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script src="http://libs.baidu.com/jquery/1.9.0/jquery.js"></script>
    <script src="http://libs.baidu.com/bootstrap/3.0.3/js/bootstrap.js"></script>
    <script src="http://libs.baidu.com/highcharts/2.2.5/highcharts.js"></script>
</head>
<body>
    <img src="http://www.baidu.com/img/bdlogo.png" alt="">
    <p>Hello,World~~</p>
</body>
</html>
```
页面运行情况（IE8）

![页面运行情况][1]

上图中，可以看出JavaScript文件阻塞了其他文件（图片）的下载过程。

当然，这是IE8下面的情况。在大多数现代浏览器中已经支持并行下载，不会出现这样的延时情况了，如Chrome、FireFox和Opera等，我们会看到有多个文件同时下载，并没有延时的情况。

但是为了适应各浏览器的情况（PS:IE），防止脚本阻塞其他页面资源的下载过程， 所以推荐的办法是： 将所有`<script>`标签放在尽可能接近`<body>`标签底部的位置，尽量减少对整个页面下载的影响。例如：

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <p>Hello,World~~</p>
    <img src="http://www.baidu.com/img/bdlogo.png" alt="">
    <script src="http://libs.baidu.com/jquery/1.9.0/jquery.js"></script>
    <script src="http://libs.baidu.com/bootstrap/3.0.3/js/bootstrap.js"></script>
    <script src="http://libs.baidu.com/highcharts/2.2.5/highcharts.js"></script>
</body>
</html>
```
页面运行情况（IE8）

![页面运行情况][2]


## 3.脚本数量
减少页面的`<script>`的总数也可以改善页面的加载速度。每当页面解析碰到一个`<script>`标签时，紧接着有一段时间用于代码执行。最小化这些延迟时间可以改善页面的整体性能。

每个 HTTP 请求都会产生额外的性能负担，下载一个 100KB 的文件比下载四个 25KB 的文件要快。总之，减少引用外部脚本文件的数量。例如，可以讲一个网页的多个JavaScript文件整合成一个文件，这样就只需一个`<script>`标签，可以提升性能。

## 4.非阻塞脚本
保持 JavaScript 文件短小，并限制 HTTP 请求的数量，只是创建反应迅速的网页应用的第一步。一个应用程序所包含的功能越多，所需要的 JavaScript 代码就越大，保持源码短小并不总是一种选择。尽管下载一个大 JavaScript 文件只产生一次 HTTP 请求，却会锁定浏览器一大段时间。

非阻塞脚本的秘密在于，等页面完成加载之后，再加载 JavaScript 源码。从技术角度讲，这意味着在window 的 load 事件发出之后开始下载代码。有几种方法可以实现这种效果。

### 4.1 动态脚本元素
DOM允许你使用 JavaScript 动态创建 HTML 的几乎全部文档内容，而`<script>`元素与页面其他元素没有什么不同。一个新的`<script>`元素可以非常容易地通过标准 DOM 函数创建：

```JavaScript
var script = document.createElement ("script");
script.type = "text/javascript";
script.src = "file1.js";
document.getElementsByTagName("head")[0].appendChild(script);
```
新的`<script>`元素加载 file1.js 源文件。此文件当元素添加到页面之后立刻开始下载。此技术的重点在于：无论在何处启动下载，文件的下载和运行都不会阻塞其他页面处理过程。你甚至可以将这些代码放在`<head>`部分而不会对其余部分的页面代码造成影响。

当动态加载的脚步文件中需要调用页面其他脚本里面的内容是，就需要跟踪脚本下载完成并准备妥善。

需要注意的是，除了Firefox和Opera，其他浏览器并不保证脚本文件的加载顺序，因此，可以将下载操作串连一起来保证加载顺序，例如：

Firefox, Opera, Chorme 和 Safari 3+会在`<script>`节点接收完成之后发出一个 load 事件。你可以监听这一事件，以得到脚本准备好的通知。

而IE 支持另一种实现方式， 它发出一个 readystatechange 事件。 `<script>`元素有一个 readyState属性，它的值随着下载外部文件的过程而改变。 readyState 有五种取值：

“uninitialized”默认状态  
“loading”下载开始  
“loaded”下载完成  
“interactive”下载完成但尚不可用  
“complete”所有数据已经准备好  

在实际应用中，最常用到的就是“loaded”和“complete”状态，IE对这两个状态的解析并不准确，不能确保哪一个一定能够获取，因此，最保险的就是两个状态都监视，当有一个状态出现时，就删除 readystatechange 事件句柄（保证事件不会被处理两次）。

下面的函数封装了标准实现和 IE 实现所需的功能：

```JavaScript
function loadScript(url, callback){
    var script = document.createElement ("script")
    script.type = "text/javascript";
    if (script.readyState){ //IE
        script.onreadystatechange = function(){
        if (script.readyState == "loaded" || script.readyState == "complete"){
            script.onreadystatechange = null;
            callback();
        }
    };
    } else { //Others
        script.onload = function(){
            callback();
        };
    }
    script.src = url;
    document.getElementsByTagName("head")[0].appendChild(script);
}
```

调用方法：

```JavaScript
loadScript("file1.js", function(){
    alert("File is loaded!");
});
```

需要注意的是，除了Firefox和Opera，其他浏览器并不保证脚本文件的加载顺序，因此，可以讲下载操作串连一起来保证加载顺序，例如：

```JavaScript
loadScript("file1.js", function(){
    loadScript("file2.js", function(){
        loadScript("file3.js", function(){
            alert("All files are loaded!");
        });
    });
});
```

如果多个文件的次序十分重要，更好的办法是将这些文件按照正确的次序连接成一个文件。独立文件可以一次性下载所有代码（由于这是异步进行的，使用一个大文件并没有什么损失）。

### 4.2 XHR 脚本注入
另一个以非阻塞方式获得脚本的方法是使用 XMLHttpRequest(XHR)对象将脚本注入到页面中。此技术首先创建一个 XHR 对象，然后下载 JavaScript 文件，接着用一个动态`<script>`元素将 JavaScript 代码注入页面。例如：

```JavaScript
var xhr = new XMLHttpRequest();
xhr.open("get", "file1.js", true);
xhr.onreadystatechange = function(){
    if (xhr.readyState == 4){
        if (xhr.status >= 200 && xhr.status < 300 || xhr.status == 304){
            var script = document.createElement ("script");
            script.type = "text/javascript";
            script.text = xhr.responseText;
            document.body.appendChild(script);
        }
    }
};
xhr.send(null);
```
这样做会创建一个带有内联代码的`<script>`元素。一旦新`<script>`元素被添加到文档，代码将被执行，并准备使用。

此方法最主要的限制是： JavaScript 文件必须与页面放置在同一个域内，不能从 CDN 下载，因此大型网页通常不采用 XHR 脚本注入技术。

### 4.3 推荐的非阻塞模式

推荐的向页面加载大量 JavaScript 的方法：

第一种是先加载一个js文件，里面就只包含了动态加载脚本所需的代码：`loadScript函数`。然后用调用它来加载页面其他元素。

```HTML
    <script type="text/javascript" src="loader.js"></script>
    <script type="text/javascript">
        loadScript("the-rest.js", function(){
            Application.init();
        });
    </script>
```

第二种与第一种类似，只是将`loadScript函数`嵌入在页面中，这可以避免另一次 HTTP 请求。

```HTML
    <script type="text/javascript">
        function loadScript(url, callback){
            // 函数内容
        }
        loadScript("the-rest.js", function(){
            Application.init();
        });
    </script>
```

页面运行情况（IE8）
![页面运行情况][3]

## 5.总结

`<script>`元素会阻塞页面的加载，有几种方法可以减少 JavaScript 对性能的影响：

- 将`<script>`标签放置在页面的底部，紧靠`</body>`标签的上方。
- 将脚本成组打包，减少页面`<script>`标签的数量，不论外部脚本文件还是内联代码都是如此。
- 非阻塞方式下载 JavaScript：1、动态创建`<script>`元素，用它下载并执行代码；2、用 XHR 对象下载代码，并注入到页面中

**这里需要注意的是。我们发现非阻塞方式后，总共的时间比将js一起放在尾部所花的时间要长，这是由于IE8+的浏览器支持 JavaScript 脚本并行下载。因此，如果使用的是IE8+、Chrome2+和FireFox3.5+等现代浏览器，不推荐使用非阻塞的方式，只需采用其他两种方式优化即可。**


[1]: /images/20150115191022.jpg "页面运行情况"
[2]: /images/20150116100430.jpg "页面运行情况"
[3]: /images/20150116101240.jpg "页面运行情况"
