# Masonite - v2.1 的下一个版本发布了！现代 Python Web 框架

> 原文：<https://dev.to/masonite/next-version-of-masonite---v21-is-released--1f48>

# 简介

Masonite 2.1 已经发布！Masonite 是一个惊人的 MVC 和**真正的**电池包括网络框架。

对于那些熟悉 2.0 的人来说，2.1 改变了应用程序中几个类的继承方式，以帮助解决我们看到的一些挑战。它还改进了我们注意到的开发人员正在做的一些框架上的怪癖。如果您是从 2.0 升级过来的，那么请务必阅读[新功能- Masonite 2.1](https://docs.masoniteproject.com/v/v2.1/whats-new/masonite-2.1) 文档以及官方的[从 2.0 升级到 2.1](https://docs.masoniteproject.com/v/v2.1/upgrade-guide/masonite-2.0-to-2.1) 的指南。

## 褒奖和反馈

大多数使用该框架的开发人员对此都有令人惊叹的地方。在过去 6 个月中，我收到的一些反馈包括:

**renéMoser【11 月 21 日上午 8:03】**
“我真的很喜欢在 Masonite 做事情，终于我可以把事情变成现实了”

当选择 Masonite 而不是 Django 竞标开发合同时(他赢得了该合同):

**亚伯兰·伊索拉【9 月 20 日下午 1:55】**
我的决定因素:

1.  应用程序必须使用现有的模式连接到现有的数据库，并管理自己的数据库。这几乎肯定会淘汰 Django，因为它的模型。使用 masonite 允许我使用一个 ORM (Orator ),它足够灵活，我可以访问几个不同的数据库，同时只管理其中一个数据库的模式。节省大量时间。
2.  craft 命令允许我生成(或编写生成器)我将为他们做的大部分代码。节省大量时间。
3.  Masonite 的文件夹结构适合我为这个项目选择的架构。
4.  Masonite 的发布周期使其成为大公司的安全选择。升级通常可以通过一个简单的脚本自动完成。节省大量时间。(想法:我们应该以某种方式将它制作成工艺品。)
5.  Masonite 的灵活中间件使我能够以模块化的方式轻松管理他们想要的访问控制级别。
6.  Masonite 的上传驱动程序允许我非常容易地管理到他们各种上传服务的连接。
7.  我只是想这么做，因为我比其他人更喜欢 Masonite

当谈到 Masonite 与他们目前在 Django 的工作软件时:

**Dean Raemaekers【9 月 17 日上午 11 点 24 分】**
我目前在 Masonite 中构建的东西可以做一些实际官方软件做不到的事情。这是在几天内完成的。

这里还有一些其他的:

迪安·雷梅克斯[9 月 14 日下午 1:42]
我仍然喜欢姜戈，但梅森奈特对我来说更有吸引力...这是新的，但感觉很成熟

迪安·雷梅克斯【9 月 12 日上午 11 点 51 分】
哇。我已经完成了我想写的应用程序的第一部分。真的很快。是的，Masonite 使项目快速发展。

**Caleb Meyer【8 月 20 日下午 5:26】**
我从未写过任何 laravel (PHP 让我不寒而栗)，但我从事 ruby 和 rails 已经有 5 年了，这是第一个从这个角度来看有意义的 python 框架。

托尼·哈马克[8 月 16 日晚上 9:23]
我在播客上听到了你的演讲。 **init** 前两天。我昨天检查了 Masonite，并在 30 分钟内移植了我的 Flask 应用程序。我花了 6 个小时才弄明白如何设置我的 flask 并重构它以达到生产水平。

虽然我在 python 方面变得更好了，但我不得不说这个框架是我用过的最简单的。文档是如此的有用。有用的代码注释和文档字符串数量惊人。我希望其他软件包采用这种策略。干得好！

## 特性

开箱即用，它具有:

*   活动记录样式表单
*   配套的脚手架 CLI 工具
*   一个非常明确的 IOC 容器来管理应用程序的依赖性
*   内置对我们都需要的许多服务的支持，如 Amazon s3 上传和磁盘支持
*   内置 websockets / Pusher 支持
*   现成的 SMTP 和 Mailgun 电子邮件支持
*   设计模式的明确用例。大多数使用 Masonite 的开发人员学习这些设计模式以及它们是如何被使用的，这自然使他们在实际意义上并通过例子和真实世界场景来学习设计模式
*   内置数据库种子，可以非常快速地将数据播种到您的表中
*   内置队列支持，通过 RabbitMQ 和其他队列服务对作业进行排队
*   近 **600 页**的大量文档。
*   还有更多。

Masonite 在 6 月份发布了最新的 2.0 版本，并获得了一些追随者和一些优秀的社区成员。

以下是查看它的主要链接:

*   [主回购](https://github.com/masoniteframework/masonite)
*   [核心回购](https://github.com/masoniteframework/core)
*   [推特账号](https://twitter.com/masoniteproject)
*   [Podcast.init 第一集](https://www.podcastinit.com/masonite-with-joe-mancuso-episode-174/)
*   [文档](https://docs.masoniteproject.com/v/v2.1/)
*   [Django vs Masonite 差异](https://dev.to/masonite/masonite-and-django-differences-kni)