# c++ build 与 Gradle 简介

> 原文：<https://dev.to/ben1980/introduction-into-c-builds-with-gradle-52f6>

发布于[thoughts-on-cpp.com](https://thoughts-on-cpp.com)

欢迎回到关于 thoughts-on-cpp.com 的新帖子。在今天的帖子中，我想介绍一下构建系统 [Gradle](https://gradle.org) 以及我们如何使用它来构建原生应用程序和库。Gradle 最初来自 Java 世界，但它也支持本地构建工具链很长时间了。Gradle 在 Java 世界中越来越受欢迎，并且很有可能会淘汰老牛 [Maven](https://maven.apache.org/) 。这是因为在本机(C/C++、Objective-C/C++、汇编和 Windows 资源)构建环境中我们也可以从中受益的两个特性。这些特性是基于 [DSL](https://en.wikipedia.org/wiki/Domain-specific_language) 的 Gradle 的易于维护和非常有表现力的 Groovy(或 Kotlin，如果喜欢的话)，以及它通过在线和本地库提供者(如 maven-central、Artifactory、Bintray 等)解决依赖性的能力。)或本地存储库。

让我们从一个[多项目的例子](https://github.com/Ben1980/gradleNativ)开始，我们已经从我的文章[中了解到关于 Jenkins 和 CMake](https://dev.to/ben1980/introduction-into-an-automated-c-build-setup-with-jenkins-and-cmake-do-temp-slug-5798207) 的自动化 C++构建设置。我只是稍微修改了 hello world 应用程序的例子。这次的主要功能是打印出“Hello World！”使用一个名为 greeter 的共享库下载到控制台上。Greeter 类本身利用外部库 [{fmt}](http://fmtlib.net/latest/index.html) 将“Hello World”打印到屏幕上。如果你想知道 Gradle 目录和文件，这些是由 Gradle 包装器提供的，它方便我们构建项目，甚至不需要预先安装 Gradle。