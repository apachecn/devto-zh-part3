# Loguru 日志记录简介

> 原文：<https://dev.to/ben1980/introduction-into-logging-with-loguru-2bak>

thoughts-on-cpp.com 的海报

这次我想简单介绍一下我刚刚遇到的一个不错的小图书馆。它叫做 [loguru](https://github.com/emilk/loguru/blob/master/README.md) ，是一个轻量级的、线程安全的日志库，拥有令人印象深刻的好的书面[文档](https://emilk.github.io/loguru/index.html)和人类可读的输出。

要使用它，你需要做的就是添加一个头文件和源文件来编译。不幸的是，在我看来，这也是一个缺点。是的，它在培训中更容易安装和使用，但部署后无论是谁在操作系统都不能更换它。在这种情况下，当操作者想要定义如何以及使用什么来进行日志记录时，一个通用的日志记录接口(facade 模式)将是更好的选择，比如[SLF 4 XXX](https://archive.codeplex.com/?p=slf4cxx)，它类似于它的 java 挂件 [slf4j](https://www.slf4j.org) 。

Loguru 支持各种各样的特性，比如日志和致命错误的回调，冗长级别，断言和中止，以及中止情况下的堆栈跟踪。甚至支持 [{fmt}](https://github.com/fmtlib/fmt) 。每个习惯 java/spring 日志输出的人都会认识到它的相似之处。