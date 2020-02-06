---
layout: post
title: vue在非项目根目录上部署的问题和解决方案
date: 2018-09-18
categories:
- 技术
tags:
- vue
- 项目部署
description: vue-router有两种模式，默认是hash模式，通过URL的hash来模拟URL。hash模式生成的URL有点丑，还有另一种history模式，它是利用history.pushState API来模拟URL。这两种模式在开发环境下都没有什么太大的问题，但是当部署到生产环境中后，两者有所不同...
---

## 环境

`vue-router` 有两种模式，默认是hash模式，通过URL的hash来模拟URL。

hash模式生成的URL有点丑，还有另一种history模式，它是利用`history.pushState`API来模拟URL。

这两种模式在开发环境下都没有什么太大的问题，但是当部署到生产环境中后，两者有所不同。

hash模式部署没有什么问题，只要访问到服务器上的`index.html`，就可以访问网站了。
重点是history模式。

## 根目录下
当项目部署在根目录下时，vue的配置文件默认不需要修改，我们要修改的是服务器的配置，这里我们使用nginx。
官方提供了解决方案[router.vuejs.org/zh...](https://router.vuejs.org/zh/guide/essentials/history-mode.html)

```
location / {
  try_files $uri $uri/ /index.html;
}
```
稍微解释一下

`$uri`就是访问的url，不包含域名和querystring，例如`/test/hello`
当访问`$uri`时，如果存在，则访问`$uri/`, 不存在就访问`/index.html`
这样配置好，访问`http://example.com/`时就可以访问到网站了，进入多级目录后刷新页面也不会存在问题。

## 非根目录下

当然，有时候我们并不想单独申请一个域名或者设置一个子域名来部署网站，这时，就需要修改一下vue的配置文件了

### 修改配置文件

这里我们定义几个环境
我们的域名：`http://example.com/`

nignx的root目录：`/usr/local/nginx/html/` 

vue网站目录：`/usr/local/nginx/html/h5/main/`

vue网站访问地址：`http://example.com/h5/main/`

vue网站主页访问地址：`http://example.com/h5/main/index.html`

vue网站静态文件目录：`http://example.com/h5/main/static/`

__1. 打包后静态资源的路径需要修改__

修改`config/index.js`文件的build节点下的`assetsPublicPath`
```
build: {
    ...
    assetsSubDirectory: 'static',
    assetsPublicPath: './'  // 未修改前的配置为 '/'，
}
```
很多人直接修改成`./`这样的相对路径是有问题的。

这种当我们访问`http://example.com/h5/main/test`这样的目录时，`./static/`就是表示`static`目录在这一级`http://example.com/h5/main/static`，这样没有问题。

当我们访问多级目录时，例如`http://example.com/h5/main/test/test`，`./static/`就是表示`static`目录在这一级`http://example.com/h5/main/test/static`，这路径明显是不正确，访问不到。


这里我们需要改成绝对路径。
```
build: {
    ...
    assetsSubDirectory: 'static',
    assetsPublicPath: '/h5/main/'  // 未修改前的配置为 '/'，
}
```
这样静态文件目录就始终在`http://example.com/h5/main/static/`这个路径了。

__2. 路由文件需要修改__

在路由的history模式下，所有的路由都是基于根路径的，如/xxxx，现在我们时部署到`/h5/main/`下，我们就要基于这个来修改路由。

`vue-router`有一个`base`属性 [https://router.vuejs.org/zh/...](https://router.vuejs.org/zh/api/#base)

```
base
类型: string
默认值: "/"
应用的基路径。例如，如果整个单页应用服务在 /app/ 下，然后 base 就应该设为 "/app/"
```

修改路由代码

```
const router = new Router({
  mode: 'history',
  base: '/h5/main/', // 这是新加的配置
  ...
})
```
这样打包部署时就可以了，但是开发环境下这个就不对了，所以我们还要根据环境修改一下文件。

`config/prod.env.js`生产环境变量
```
module.exports = {
  NODE_ENV: '"production"',
  ROUTER_BASE: '"/h5/main/"'
}
```

`config/dev.env.js`生产环境变量
```
module.exports = {
  NODE_ENV: '"production"',
  ROUTER_BASE: '"/"'
}
```

路由代码
```
const router = new Router({
  mode: 'history',
  base: process.env.ROUTER_BASE, // 这是新加的配置
  ...
})
```

至此，打包配置的相关修改已全部完成，项目也能够正常访问。

但还是会有一个问题，跳转到某个路由后，刷新页面，就gg了，页面为空白，此时就要修改nginx的配置了。

### nginx配置修改

官方给的nginx配置是根目录下的，需要修改一下
```
location ~ /h5/main {
    try_files $uri $uri/ /h5/main/index.html;
}
```

`/h5/main/`就是部署的网站目录。

这样几项配置后，就可以在子目录下访问网站，刷新也没有问题。
