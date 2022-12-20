# Jenkins 和 CMake 的自动化 C++构建设置简介

> 原文：<https://dev.to/ben1980/introduction-into-an-automated-c-build-setup-with-jenkins-and-cmake-4ob2>

thoughts-on-cpp.com 的海报

欢迎回到关于 thoughts-on-cpp.com 的新帖子。这一次我想介绍一个自动化构建设置，它基于 [Jenkins](https://jenkins.io/) 和 [CMake](https://cmake.org/) ，满足了以下需求:

*   在每次提交时构建一个准备部署的版本
*   所有测试的执行
*   运行静态代码分析来跟踪代码质量
*   并且易于通过自动化部署(CD)进行扩展

对于所有必要的资源，我准备了一个 [GitHub](https://github.com/Ben1980/jenkinsexample) 库。我们在这里只关注自动化构建过程的技术部分，这是 CI/CD(持续集成/持续部署)过程的先决条件。对于一个公司来说，完全接受 CI/CD 过程背后的思想比仅仅使用工具要必要得多，但是使用自动化的构建和测试设置是一种很好的方式。

基于 Qt 和 C++的示例桌面应用程序的构建将由 [Jenkins 声明性管道](https://jenkins.io/doc/book/pipeline/syntax/)协调。示例应用程序基于一个简单的 CMake 项目，该项目是用 [CLion](https://www.jetbrains.com/clion/) 生成的。作为静态代码分析工具，我们使用的是 [cppcheck](http://cppcheck.sourceforge.net) 。测试是用我最喜欢的测试框架 [Catch2](https://github.com/catchorg/Catch2) 完成的。

在这篇文章中，我假设你已经熟悉了一个基本的 Jenkins 设置。如果你不熟悉 Jenkins， [jenkins.io](https://jenkins.io) 是一个很好的信息来源。

Jenkins 由一个名为 Jenkinsfile 的文件中定义的声明性管道组成。该文件必须位于项目的根文件夹中。Jenkins 的声明性管道是基于 Groovy 作为 DSL 的 T2，并提供了一种非常有表现力的方式来定义构建过程。尽管 DSL 非常强大，因为它是基于 Groovy 的，您实际上可以编写小脚本，但不幸的是，它的文档与它的前身基于脚本的管道有点混淆。对于我们的示例设置，它看起来如下。