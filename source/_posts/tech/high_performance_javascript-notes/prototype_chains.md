---
layout: post
title: JavaScript原型链分析
date: 2015-01-19
categories:
- 技术
description: JavaScript 中的对象是基于原型的。原型是其他对象的基础，定义并实现了一个新对象所必须具有的成员。这一概念完全不同于传统面向对象编程中“类”的概念，它定义了创建新对象的过程。原型对象为所有给定类型的对象实例所共享，因此所有实例共享原型对象的成员。
---

## 1.前言
JavaScript 中的对象是基于原型的。原型是其他对象的基础，定义并实现了一个新对象所必须具有的成员。这一概念完全不同于传统面向对象编程中“类”的概念，它定义了创建新对象的过程。原型对象为所有给定类型的对象实例所共享，因此所有实例共享原型对象的成员。

## 2.对象的创建方式

__通过`{}`来创建对象__

```JavaScript
var obj = {location:'天朝',hobby:'台球'};
```

__通过`new Object`创建对象__

```JavaScript
var obj = new Object();
```

__通过构造函数创建对象__

```JavaScript
function Dog() {
    this.leg = 4;
    this.tail = 1;
    this.bark = function () {
        alert('汪汪');
    }
}
var hei = new Dog();
```

## 3.构造函数
构造函数就是构造一个对象(一堆属性)的一个规则,造完后就和该对象无关了。

分析一下上面的构造函数实例化`new Dog()`的过程

1. 首先构造了一个空对象
2. 把this指向该空对象
3. 开始执行函数(词法分析+语句执行)
4. 返回该对象

这里需要注意的是构造函数的返回值  与 普通函数调用的返回值 的不同。  
普通函数调用时, 如有return,则返回该return值,如果没return,或者 'return;', 则返回值为undefined。  
对于构造函数创建对象,遇到return返回,依然成立，但返回值，如果是`{...}`或者new表达式则返回return的值，否则是构造函数的函数体形成的新对象。

一个栗子：

```JavaScript
function Pig() {
    this.leg = 4;
    this.hei = '300KG';
    return false;  // Pig {leg: 4, hei: "300KG"}
    return 'hehe';  // Pig {leg: 4, hei: "300KG"}
    return {leg:2,hei:'150KG'}  // Object {leg: 2, hei: "150KG"}
}
```

可以看到两种方式返回的结果并不一样，当return的值为对象是返回的就不是Pig对象，是一个新的Object了。

## 4.对象的原型
对象可以有两种类型的成员：实例成员（也称作“own”成员）和原型成员。实例成员直接存在于实例自身，而原型成员则从对象原型继承。

对象通过一个内部属性绑定到它的原型，Firefox， Safari，和 Chrome 中开放了该属性，叫做`__proto__`。

对象的原型决定了一个实例的类型。默认情况下，所有对象都是 Object 的实例

```JavaScript
var obj = {location:'天朝',hobby:'台球'};
console.log(obj.__proto__)  // Object {}
```

## 5.实例与原型的关系

```JavaScript
var cat = { name: "kitty", age: "3"};
console.log(cat.toString()); //"[object Object]"
```

此代码中， cat 对象有两个实例成员： name 和 age。但它并没有定义 toString()，但是它被调用了，也没有抛出错误。 toString()函数就是一个 cat 对象继承的原型成员。

处理对象成员的过程与变量处理十分相似。当 cat.toString()被调用时，对成员进行名为“toString”的搜索，首先从对象实例开始，如果 cat 没有名为 toString 的成员，那么就转向搜索原型对象，在那里发现了toString()方法并执行它。通过这种方法， cat 可以访问它的原型所拥有的每个属性或方法。

确定一个对象是否具有特定名称的实例成员可以使用hasOwnProperty()；确定对象是否具有某个名称的成员，你可以使用操作符 in。接着上面的：

```JavaScript
console.log(cat.hasOwnProperty("name")); //true
console.log(cat.hasOwnProperty("toString")); //false
console.log("name" in cat); //true
console.log("toString" in cat); //true
```

## 6.原型链
js中没有类的概念, 只有对象的概念。所以一切类型都是一个实例对象，它们继承原型的所有方法。也可以用“构造器（prototype）”创建一个类型的原型。

```JavaScript
var kitty = {color:'yellow',bark:function () {alert('喵喵')},climb:function () {alert('我在树上')}}
function Tiger (){
    this.color = 'yellow and black';
    this.bark = function () {
        alert('吼吼');
    }
}
// 给构造函数声明原型, 那么构造出的对象,就会有一个祖先:即该原型
Tiger.prototype = kitty;
var heihu = new Tiger();
heihu.bark(); // 吼吼
heihu.climb(); // 我在树上
console.log(heihu.__proto__);  // Object {color: "yellow", bark: function, climb: function}
console.log(heihu.__proto__.__proto__); // Object {}
```

Tiger构造函数用于创建一个heihu实例，heihu 的原型（`__proto__`） 是 kitty对象， kitty的原型是 Object。这就创建了一个原形链，heihu继承了它们两个的所有成员，当调用heihu.bark()时，由于对象包含该成员，直接返回；当调用heihu.climb()，发现实例对象中没有，就沿着原型链找到kitty，在它的成员中找到，然后返回。

和作用域链类似，当调用对象成员时，深入原形链越深，搜索的速度就会越慢。虽然使用优化 JavaScript引擎的现代浏览器表现良好，但是老的浏览器，特别是 IE，每深入原形链一层都会增加性能损失。因此要减少原型链的遍历深度和次数。
