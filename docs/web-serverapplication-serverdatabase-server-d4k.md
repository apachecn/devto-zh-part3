# Web 服务器？/应用服务器？/数据库服务器？

> 原文：<https://dev.to/k_penguin_sato/web-serverapplication-serverdatabase-server-d4k>

在这篇文章中，我将分解三个主要的用于网络应用的`servers`，它们是`web server`、`application server`和`database server`。

## 什么是 web 服务器

> `Web server`是一个程序，它使用 HTTP(超文本传输协议)向用户提供静态文件，以响应他们的请求。
> 
> [什么是 Web 服务器？-来自 WhatIs.com 的定义](https://whatis.techtarget.com/definition/Web-server)

如果 web 服务器收到提供静态 HTML、CSS 或 Javascript 文件的请求，它只返回请求的文件。但是，如果 web 服务器收到一个需要动态处理的请求，会发生什么呢？

这时一个应用服务器进来了。

`Nginx`和`Apache`都是非常知名和广泛使用的`web server softwares`。

## 什么是应用服务器？

> 应用服务器是分布式网络中计算机中的服务器程序，为应用程序提供业务逻辑。
> 
> [什么是应用服务器？-来自 WhatIs.com 的定义](https://searchsqlserver.techtarget.com/definition/application-server)

它是您的应用程序实际运行的服务器。应用服务器并不局限于 HTTP，它还能做许多其他的事情。当一个应用程序服务器收到来自 web 服务器的请求时，它会告诉你的应用程序，而你的应用程序会执行它被告知的任务。

如果你熟悉 Ruby on Rails，你可能听说过`Unicorn`、`Rainbows`和`Puma`。它们是广泛用于 rails 应用程序的应用服务器软件。

## 什么是数据库服务器？

> 数据库服务器是一种计算机系统，它为其他计算机提供与从数据库中访问和检索数据相关的服务。
> 
> [什么是数据库服务器？](https://www.computerhope.com/jargon/d/database-server.htm)

基本上是非常自明的。
T3 一个数据库服务器保存着`Database Management System (DBMS)`和`databases`。它在数据库中搜索选定的记录，并通过网络将它们传回。

## 参考文献

*   [应用服务器、Web 服务器:有什么区别？| JavaWorld](https://www.javaworld.com/article/2077354/app-server-web-server-what-s-the-difference.html)
*   [web server——应用服务器和 web 服务器有什么区别？-堆栈溢出](https://stackoverflow.com/questions/936197/what-is-the-difference-between-application-server-and-web-server)