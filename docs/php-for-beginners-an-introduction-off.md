# PHP 初学者入门

> 原文：<https://dev.to/kluxury/php-for-beginners-an-introduction-off>

## 概述

PHP(超文本预处理器的首字母缩写)可能是网络上最流行的脚本语言。它用于增强网页。

## Web 架构

[![](img/158b292ec18f4baa84cc76b5278eb0cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--29TOcKku--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4mhnsg0dpid57yka1tnn.jpg)

1.  用户通过浏览器访问网站。
2.  浏览器从 web 服务器请求该页面。
3.  网络服务器将收集请求的页面(html，图像和所有等。)从它的文档根。
4.  如果是静态元素，如 html、css 或 JavaScript，响应将直接返回给浏览器，浏览器会将其显示在用户屏幕上。
5.  否则，如果它是一个 PHP 文件，web 服务器将它的内容发送给 PHP 解释器，后者解释并执行文件中的代码。如果需要，它还执行数据库操作。
6.  PHP 解释器然后生成输出(如果需要的话)并发送给 web 服务器。
7.  然后，Web 服务器将其发送到浏览器，浏览器将其呈现在用户屏幕上。

## PHP 的由来

它是由拉斯马斯·勒德尔夫在 1944 年创建的，目的是跟踪对他的在线简历的访问，他将这些收藏命名为个人主页工具。它的第一个版本是用 C 编程语言编写的，随着时间的推移，需要更多的功能，这将我们带到了现在的阶段。

## 为什么要用 PHP

开源
免费下载和使用
更广泛的用户群体
在服务器上高效运行

## PHP 强项

与同时代的人相比，PHP 比其他语言更容易学习，更受欢迎，拥有更广泛的受众。

## PHP 同时代

PHP 并不是访问数据库或 web 服务器的唯一脚本语言，其他语言包括 Perl、Java Server Page 和微软 Asp.net 等等，每一种都有自己的优缺点。

## 可用性

PHP 可以在多种平台上使用，如 Windows、Linux、Unix、Mac OS 等。它还兼容几乎所有现在使用的网络服务器，例如 Apache 和 IIS。

## 安装 PHP 作为 Apache 的模块

有很多软件都有 PHP 包，比如 Xampp 服务器和 Wamp。明智的做法是，在做出选择之前，把它们都检查一遍，看看每一个都有什么可以提供。