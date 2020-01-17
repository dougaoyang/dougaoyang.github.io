---
layout: post
title: bootstrap中modal的data-remote属性无效的问题
date: 2014-11-10
toc: false
categories:
- 技术
description: 最近在公司更新一个项目是发现一个问题，就是bootstrap的模态框的data-remote属性没有效果，但是发现老项目里是有效果的，发现很奇怪，Google后终于发现问题所在，于是记录下来...
---

## 1. 问题

最近在公司更新一个项目是发现一个问题，就是bootstrap的模态框的data-remote属性没有效果，但是发现老项目里是有效果的，发现很奇怪，Google后终于发现问题所在，于是记录下来。

链接地址 <http://stackoverflow.com/questions/18378720/bootstrap-3-with-remote-modal>

原因是在bootstrap3.1之后modal的一些属性发生了变化，远程的内容要插入到`.modal-content`选择器里面。可以看一下两者的区别。

## 2. Bootstrap跨版本区别

### 2.1. Bootstrap3.1之前的版本

#### 模态框页	

```html
<a href="#" data-toggle="modal" data-target="#myModal">
    <img src="images/ball.gif" alt="Add Account"/>
</a>

<div class="modal fade"
     id="myModal"
     tabindex="-1"
     data-remote="./popups/remotePage.html"
     role="dialog"
     data-backdrop="static"
     data-keyboard="false"
>
    ...
</div>
```

#### 远程页面parentPage.html

```html
<div class="modal-dialog">
    <div class="modal-content">
        <div class="modal-header">
        Header
        </div>
        <div class="modal-body">
            One fine body...
        </div>
        <div class="modal-footer">Footer
        </div>
    </div>
</div>
```

远程内容直接插入到`.modal`节点里面。

### 2.2. Bootstrap3.1之后的版本

#### 模态框页	

```html
<a href="./popups/remotePage.html" data-toggle="modal" data-target="#myModal">
    <img src="images/ball.gif" alt="Add Account"/>
</a>

<div class="modal fade"
     id="myModal"
     tabindex="-1"
     role="dialog"
     data-backdrop="static"
     data-keyboard="false"
>
    <div class="modal-dialog">
        <div class="modal-content">
            ....
        </div>
    </div>
</div>
```

#### 远程页面parentPage.html

```html
<div class="modal-header">Header</div>
<div class="modal-body">
    One fine body...
</div>
<div class="modal-footer">Footer</div>
```

远程内容直接插入到`.modal-content`节点里面。

## 3. 总结

这是bootstrap跨版本所产生的问题，因此当我们使用开源的框架时，要时刻留意每个版本直接作出的改动。这样，我们才能够避免出现类似的错误，愿与大家共同进步。
