---
layout: post
title: 解决php处理jpg格式图片的问题
date: 2014-10-23
categories:
- 技术
tags:
- PHP
- 图片格式
description: 这两天在使用wordpress的编辑图像功能时遇到一个问题，发现jpeg格式的图片在上传之后无法编辑，以为是代码的问题，后来重新安装wp后发现问题依旧，就猜想是不是环境的问题，于是就查看phpinfo的结果，发现在gd模块下ibjpeg version unknown，不能识别jpeg的版本，在网上搜索一番，终于找到了结果，拿来和大家分享，希望对有同样问题的同学有帮助...
---

## 1. 问题

这两天在使用wordpress的编辑图像功能时遇到一个问题，发现jpeg格式的图片在上传之后无法编辑，以为是代码的问题，后来重新安装wp后发现问题依旧，就猜想是不是环境的问题，于是就查看phpinfo的结果，发现在gd模块下ibjpeg version unknown，不能识别jpeg的版本，在网上搜索一番，终于找到了结果，拿来和大家分享，希望对有同样问题的同学有帮助。 

## 2. 解决办法

这个原因是由于gd库没有解析jpeg的版本，这就需要重新编译php和gd库扩展,首先需要编译安装 freetype，libpng，jpegv9，和gd2 安装过程就不在赘述。

然后在编译php的时候将--with-gd=/usr/local/gd2/ 替换成--enable-gd-native-ttf 这个选项，来进行编译，编译成功后进入源代码文件夹内，进入ext/gd/libgd文件夹下，修改gd_jpeg.c文件，大约在111行左右，将switch内的内容替换为如下格式

```php
	switch(JPEG_LIB_VERSION) {
        case 62:
            return "6b";
            break;
 
        case 70:
            return "7";
            break;
 
        case 80:
            return "8";
            break;
 
        case 81:
            return "8a";
            break;
 
        case 82:
            return "8b";
            break;
 
        case 83:
            return "8c";
            break;
 
        case 84:
            return "8d";
            break;
 
        case 90:
            return "9";
            break;
 
        case 91:
            return "9a";
            break;
 
        case 92:
            return "9b";
            break;
 
        case 93:
            return "9c";
            break;
 
        case 94:
            return "9d";
            break;
 
        default:
            return "unknown";
    }
```

然后进入到上级目录内，开始编译gd扩展
```
>/usr/local/php/bin/phpize 
```

然后 
```
>./configure --with-php-config=/usr/local/php/bin/php-config --with-jpeg-dir=/usr/local/jpeg --with-gd
```

编译完成

然后在php.ini中添加一行

```
>extension=gd.so
```

重启apache服务，就会看到在libjpeg version 后显示的不再是unknown了 ，问题解决了