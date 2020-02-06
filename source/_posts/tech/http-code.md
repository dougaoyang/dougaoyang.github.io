---
layout: post
title: HTTP协议和状态码
date: 2014-11-14
categories:
- 技术
tags:
- http
- 状态码
description: 计算机中的协议和现实中的协议是一样的，一式双份/多份。双方/多方都遵从共同的一个规范，这个规范就可以称为协议。计算机只所以能全世界互通，协议是功不可没，如果没有协议，计算机各说各话，根本谁都听不懂谁。有ftp，http，stmp，pop，tcp/ip协议...
---

## 1. http协议介绍

计算机中的协议和现实中的协议是一样的，一式双份/多份。双方/多方都遵从共同的一个规范，这个规范就可以称为协议。计算机只所以能全世界互通，协议是功不可没，如果没有协议，计算机各说各话，根本谁都听不懂谁。有ftp，http，stmp，pop，tcp/ip协议.....

http协议的重要性:无论是以后用webserverice,还是用rest做大型架构,都离不开对HTTP协议的认识.

甚至可以简化的说:

webservice = http+XML

rest = http+ json

各种API,也一般是用http+XML/json来实现的.

## 2. http协议内容

### 2.1. http 请求头

```
    请求行(请求方法路径协议)
    头信息(格式为key: value)
    空行
    主体信息(可选) (发送内容)
    例:
    POST /http.php HTTP/1.1
    Host: localhost
    Content-type: application/x-www-form-urlencode
    Cotent-length: 15

    name=jack&age=3
```

### 2.2. http 响应头

```
    响应行(协议状态码状态文字)
    响应头信息(格式为key:value)
    空行
    主体信息(也可能没有)
    例:
    HTTP/1.1 200 OK
    Content-type: text/html
    Content-length: 5

    hello
```

## 3. 状态码,状态文字

状态码是用来反应服务器响应情况的，最常见的如200 OK, 404 NOT FOUND。状态文字是用来描述状态码的,便于人观察.

| 状态码  | 定义       |  说明 |
| :-----  | :--------  | :---- |
| 1XX     | 信息       | 接受到信息，请处理 |
| 2XX     | 成功       | 操作成功的收到，理解和接受 |
| 3XX     | 重定向     | 为了完成请求，必须采取进一步措施 |
| 4XX     | 客户端错误 | 请求的语法有错误或不能完全被满足 |
| 5XX     | 服务端错误 | 服务器无法完成明显有效的请求 |

### 3.1. 常见状态码
200 – 服务器成功返回网页  

204 - no content

301/2- 永久/临时重定向

304 Not Modified– 未修改  

307 重定向中保持原有的请求数据
 
404 – 请求的网页不存在 

503 – 服务器暂时不可用 

500 – 服务器内部错误
