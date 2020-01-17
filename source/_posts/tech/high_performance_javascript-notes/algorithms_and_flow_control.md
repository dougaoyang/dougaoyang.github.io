---
layout: post
title: JavaScript流程控制对性能的影响
date: 2015-01-29
categories:
- 技术
description: 代码整体结构是执行速度的决定因素之一。 代码量少不一定运行速度快， 代码量多也不一定运行速度慢。性能损失与代码组织方式和具体问题解决办法直接相关
---

## 1.前言

代码整体结构是执行速度的决定因素之一。 代码量少不一定运行速度快， 代码量多也不一定运行速度慢。性能损失与代码组织方式和具体问题解决办法直接相关。

## 2.循环

在各种编程语言中，循环占了很大一部分，因此它是提高性能所需要关注的重点之一。理解 JavaScript 中循环对性能的影响至关重要，因为死循环或者长时间运行的循环会严重影响用户体验。

### 2.1 循环的类型

- for循环
- while循环
- do-while循环
- for-in循环

在四种循环中，前三种的性能差别不大，而第四种循环则明显慢于前三种。

for-in循环中由于每次迭代操作要搜索实例或原形的属性,for-in 循环每次迭代都要付出更多开销。因此除了遍历数量不详的对象外，要避免使用该循环。如果你迭代遍历一个有限的,已知的属性列表,使用其他循环类型更快:

```Javascript
var obj = {"prop1":1, "prop2":2},
    props = ["prop1", "prop2"];

for(var i=0; i<props.length; i++){
    process(obj[props[i]]);
}
```

在其他三种类型的循环中，选择哪种方式，应该基于需求。

- 每次迭代干什么
- 迭代的次数

可以根据这两种来书写合理的循环结构

### 2.2 减少每次循环的工作量

一个典型的数组处理循环,可使用三种循环的任何一种。最常用的代码写法如下:

```Javascript
for (var i=0; i < items.length; i++){
    process(items[i]);
}

var j=0;
while (j < items.length){
    process(items[j++]]);
}

var k=0;
do {
    process(items[k++]);
} while (k < items.length);
```

在每个循环中,每次运行循环体都要发生如下几个操作:

1. 在控制条件中读一次属性(items.length)
2. 在控制条件中执行一次比较(i < items.length)
3. 比较操作,察看条件控制体的运算结果是不是 true(i < items.length == true)
4. 一次自加操作(i++)
5. 一次数组查找(items[i])
6. 一次函数调用(process(items[i]))

在这些步骤中，有些是可以精简的。

精简后：

```Javascript
for (var i=items.length; i--; ){
    process(items[i]);
}

var j = items.length;
while (j--){
    process(items[j]]);
}

var k = items.length-1;
do {
    process(items[k]);
} while (k--);
```


首先减少对象成员和数组项查找的次数。在上个例子中每次循环都查找 items.length。这是一种浪费,因为该值在循环体执行过程中不会改变,可以将此值存入一局部变量中。

还可以通过改变他们的顺序提高循环性能。倒序循环是编程语言中常用的性能优化方法。使用倒序循环,并在控制条件中使用了减法。每个控制条件只是简单地与零进行比较。实际上,控制条件已经从两次比较(迭代少于总数吗?它等于 true 吗?)减少到一次比较(它等于 true 吗?)。

在精简后每个循环中,发生了:

1. 在控制条件中进行一次比较(i == true)
2. 一次减法操作(i--)
3. 一次数组查询(items[i])
4. 一次函数调用(process(items[i]))

### 2.3 减少迭代次数

减少循环的迭代次数的方法，最广为人知的限制循环迭代次数的模式称作“达夫设备”。关于“达夫设备”可一自行google，不做详细描述。

## 3. 条件表达式

### 3.1 if-else 与 switch 比较

使用 if-else 或者 switch 的流行理论是基于测试条件的数量:条件数量较大,倾向于使用 switch 而不是if-else。

在易读性上，如果条件较少时,if-else 容易阅读,而条件较多时 switch更容易阅读。

只有在条件数量很大时，switch才比if-else快，两者之间的区别是，条件体的工作量对if-else的性能影响较大。


###3.2 优化 if-else

优化 if-else 的目标总是最小化找到正确分支之前所判断条件体的数量。最简单的优化方法是将最常见的条件体放在首位。

```Javascript
if (value < 5) {
    //do something
} else if (value > 5 && value < 10) {
    //do something
} else {
    //do something
}
```

这段代码只有当 value 值经常小于 5 时才是最优的。在if-else条件的排列要按照概率从大到小的顺序排列。

##4. 总结

- for,while,do-while 循环的性能特性相似,谁也不比谁更快或更慢。
- 只在遍历一个属性未知的对象时使用 for-in 循环。
- 改善循环性能的办法是减少每次迭代中的运算量,并减少循环迭代次数。
- switch 与 if-else 的性能差别不大，可以根据可读性来选择使用哪一种方法



