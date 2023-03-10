# 模块化旧的整体轨道引擎拯救！

> 原文：<https://dev.to/szaszolak/modularizing-old-monolith-rails-engines-to-the-rescue-4jm0>

## TLDR

这篇文章是对 Rails 惊人特性的一次呐喊，这些特性允许开发人员相当容易地将代码从 monolith 应用程序提取到独立的模块中。它旨在指出 Rails 引擎相对于微服务的优势，并承认引擎解决方案的缺点。

## 关于我

我是一名后端开发人员，我是在 4.5 年前迷上 Rails 的。从那以后，我花了将近三年的时间，作为一名专业人士，致力于最初于 2011 年开发的一款产品。这是我的第一个帖子，希望你喜欢。

## 简介

几周前，我开始研究如何改进我公司的主应用程序的架构，该应用程序相当旧且相当大(81，980 次提交，还在继续)。随着时间的推移，许多开发人员试图让它保持最佳状态。尽管超越了标准的 MVC 模型，并向应用程序添加了层，但这已经不够了。我们遇到过与大型整体系统相关的典型问题:维护系统心智模型所需的巨大认知负荷、缓慢的测试套件、死代码等。在这样的情况下，首先想到的是将这样一个系统转变成一个闪亮的微服务世界。虽然听起来很诱人，但过渡并不总是容易，甚至不可能，原因有很多:

*   它将应用程序代码库的复杂性转移到系统基础架构中
*   它引入了新的问题，主要是通信、同步和集成方面的问题。
*   在将单个业务模块移出一个整体之前，需要做大量的基础工作来为这种变化做好系统准备
*   这使得本地开发环境的设置更加复杂

选择微服务方法的团队应该有许多熟练的软件架构师和开发运营专家，以及相当多的时间来完成这样的目标。对于我来说，微服务是不可能的。我一直在寻找，我注意到了一条方便的中间道路，你会读到的。。。。

## 轨道引擎

发动机类似于标准的轨道应用，但在小型化的形式。它们的结构与典型的 Rails 项目非常相似；人们可以在 Rails 架构中找到所有熟悉的 MVC 部分，比如模型、视图、控制器、作业和通道。有一些小的区别，但最重要的，也是使它们成为如此有用的工具的是**Rails 引擎不是作为独立的应用程序运行，而是安装在更大的系统中**。此功能还实现了一种方便的机制，可以防止主机应用程序和引擎代码之间的名称冲突，同时还允许在需要时进行交叉引用。
考虑到这一点，引擎最酷的用例之一是能够将 monolith 主机应用程序中名称空间分组的代码提取到安装在父应用程序中的引擎中。这种解决方案实现了微服务的大部分承诺，例如系统模块化、减少在单个服务上工作所需的认知负荷以及减少测试套件的执行时间。
此外，Rails 引擎在几个特定领域大放异彩:

*   因为它们安装在主机应用程序内部，所以不会受到与独立微服务相关的通信和集成问题的影响。
*   有一个简单的设置；事实上，Rails 引擎可以用一个 Rails 命令启动。
*   作为一个引擎，提取代码的平稳性起初可能只是主机应用程序的一个子目录。文件可以通过拖放在它们之间移动。
*   以 gem 的形式提取到独立存储库中的能力。并被多个应用程序重用。
*   基于所使用的 gem 版本号，不同的版本可以包含在不同的应用程序中，这使得开发新功能和 A/B 测试更加简单。

因为在软件开发中没有灵丹妙药，所以这种方法也有它的缺点:

*   除了由许多独立的模块组成之外，宿主应用程序仍然是一个单一的系统，可能会受到多个引擎之间的依赖冲突的影响。
*   Rails 引擎不具备微服务的技术自由度。
*   主机应用程序仍然需要大量的硬件资源，就像 monolith 一样。
*   尽管很方便，交叉引用主机应用程序和安装的引擎的能力也使它们之间更容易产生不必要的耦合。

## 总结

基于上述分析，到目前为止，利大于弊，特别是对于没有太多资源来执行向微服务过渡的小型开发团队。即使就这种转变而言，Rails 引擎也可以作为中间步骤，允许一系列较小的变化，而不是一个大的飞跃。每个之前提取的引擎都可以安装在一个更小的 Rails 应用程序中，并作为微服务使用。还应该提到的是，引擎经过了很好的“战斗测试”，因为一些非常流行的解决方案采用了它们(例如，设计和狂欢宝石)，
我希望你喜欢这篇文章。在我的下一篇文章中，我将提供 Rails 引擎世界中“Hello World”的教程。