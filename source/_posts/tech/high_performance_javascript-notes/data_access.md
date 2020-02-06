---
layout: post
title: JavaScript数据访问对性能的影响
date: 2015-01-17
categories:
- 技术
tags:
- Javascript
description: 在JavaScript作用域链分析中介绍了函数的作用域链。在函数运行后，会生成激活对象,每遇到一个变量，js都要根据作用域链来决定从哪里获得和存储数据。<br>此过程搜索运行函数的作用域链,搜索工作从运行函数的激活目标之作用域链的前端开始，如果找到了,那么就使用这个变量;如果没找到,搜索工作将进入作用域链的下一个对象。此过程持续运行,直到变量被找到,或者没有更多对象可用于搜索,这种情况下标识符将被认为是未定义的。<br>而这种搜索过程影响了 JavaScript 的运行性能。
---

## 1.前言
在 [JavaScript作用域链分析][link:1] 中介绍了函数的作用域链。在函数运行后，会生成激活对象,每遇到一个变量，js都要根据作用域链来决定从哪里获得和存储数据。

此过程搜索运行函数的作用域链,搜索工作从运行函数的激活目标之作用域链的前端开始，如果找到了,那么就使用这个变量;如果没找到,搜索工作将进入作用域链的下一个对象。此过程持续运行,直到变量被找到,或者没有更多对象可用于搜索,这种情况下标识符将被认为是未定义的。

而这种搜索过程影响了 JavaScript 的运行性能。


## 2.全局变量和局部变量分析
在一个函数运行的作用域链上，一个变量所处的位置愈深，它的读写速度就愈慢。因此，局部变量的访问速度是最快的，而全局变量的访问通常是最慢的。

可以看一下比较结果

代码

```JavaScript
var global = 'hello'    	
function parse_part(){
	function a(){
		function b(){
			var part = 'hello'
			part += ' world'
		}
		b()
	}		
	a()
}
function parse_global(){
	function a(){
		function b(){
			global += ' world'
		}
		b()
	}		
	a()
}
Test.run( '局部变量' ); 
for( var i=0; i<9999; i++ ) {
	parse_part()
}
Test.stop(); 
Test.run( '全局变量' ); 
for( var i=0; i<9999; i++ ) {
	parse_global()
}
Test.stop(); 
Test.output( 'js局部变量和全局变量性能比较' ); 
```

运行结果

IE8下

![ie下比较结果][img:1]

chrome39下

![chrome下比较结果][img:2]


在IE下运行多次基本上都是局部变量的速度快。而在Chrome下多次运行，两者的速度相差无几，而且速度快了两个数量级，这是由于Chrome优化js引擎的结果。

一般来说，对于浏览器，变量所处位置愈深，它的速度就愈慢。而在现代浏览器中，由于JavaScript引擎被优化，这种性能损失几乎就没有了。但是在那些没有优化过的浏览器里（IE），依然要注意这一点。

通过以上信息,在没有优化 JavaScript 引擎的浏览器中,最好尽可能使用局部变量。一个好的经验法则是:用局部变量存储本地范围之外的变量值。

下面举个栗子：

```JavaScript
function initUI(){
    var bd = document.body,
        links = document.getElementsByTagName("a"),
        i = 0,
        len = links.length;
    while(i < len){
        update(links[i++]);
    }
    document.getElementById("go-btn").onclick = function(){
        start();
    };
    bd.className = "active";
}
```

此函数包含三个对 document 的引用,document 是一个全局对象。搜索此变量,必须遍历整个作用域链,直到最后在全局变量 window 对象中找到它。我们可以将document这个全局变量的引用存储在一个局部变量里，在下面的代码中使用这个全局变量：

```JavaScript
function initUI(){
    var doc = document,
        bd = doc.body,
        links = doc.getElementsByTagName("a"),
        i = 0,
        len = links.length;
    while(i < len){
        update(links[i++]);
    }
    doc.getElementById("go-btn").onclick = function(){
        start();
    };
    bd.className = "active";
}
```

现在访问全局变量的次数是 1 次,而不是 3 次。用 doc 替代 document 更快,因为它是一个局部变量。通过这个简单的函数，我们可以发现，当变量的数量增加时，性能会的到明显的提升。

## 3.闭包中变量分析
闭包允许函数访问局部范围之外的数据。举一个栗子：

```JavaScript
function f1(){
    var n=999;
    function f2(){
        n+=1;
        alert(n);
    }
    return f2;
}
var result=f1();
result(); // 999
result(); // 1000
```

可以看到，函数f1的激活对象是函数f2和变量n，当函数f2运行时，变量n读取的是f1的激活对象里面的n变量。这是闭包的一个性能关注点，当经常访问函数范围之外的变量时，每次访问都会导致一些性能损失。

根据上文中对局部变量和全局变量的分析，可以将范围之外的变量存入局部变量中，然后直接访问局部变量即可。

## 4.总结
JavaScript 中变量的访问是按照作用域链的规定来搜索的，在该过程中会造成性能损失。在未优化的浏览器（PS:特别是IE）中需要特别关注这一点。在局部变量、范围外变量和全局变量中读取中，可以尽量将之存入局部变量中，使用局部变量访问。


[link:1]: /2015/01/16/scope_chains.html  "JavaScript作用域链分析"
[img:1]: /images/20150118003219.jpg "ie下比较结果"
[img:2]: /images/005.jpeg "chrome下比较结果"
