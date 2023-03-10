# 最近发布的一些我觉得有趣的东西

> 原文：<https://dev.to/rhymes/a-few-things-released-recently-that-i-find-interesting-4c2m>

你可能知道，最近亚马逊举办了他们的 [AWS re:Invent 2018](https://aws.amazon.com/blogs/aws/aws-previews-and-pre-announcements-at-reinvent-2018-andy-jassy-keynote/) 发布会，微软举办了他们的[Connect()；2018](https://www.microsoft.com/en-us/connectevent) 事件与谷歌举办[颤振直播](https://developers.google.com/events/flutter-live/)。

在这篇文章中，我将列出一些我觉得有趣的事情。如果我要谈论一切，我可能会在明年的会议上结束。

## AWS 重新发明

AWS 一如既往地发布了许多功能和改进。他们仪表板被过度包装是出了名的，我不知道其中的一半是干什么用的:

[![](img/278baea655404d7c11ecbcde8460afb0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--effPoJjV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/br8m4snhee2qceui4gx5.png)

[新增功能](https://aws.amazon.com/blogs/aws/aws-previews-and-pre-announcements-at-reinvent-2018-andy-jassy-keynote/)中间几笔一个和弦:

*   AWS Lambda(才 4 岁！)获得了对共享代码和运行时 API 的支持，允许您用任何语言编写函数。Ruby，C++，Rust，Erlang 等都在。这是巨大的，尤其是对于那些不想在分布式应用中维护多个环境的开发人员来说。你可以在这里阅读全部内容:[使用任何编程语言，共享通用组件](https://aws.amazon.com/blogs/aws/new-for-aws-lambda-use-any-programming-language-and-share-common-components/)

*   AWS 已经推出了基于 ARM 的处理器，提供无限的实例类型。用他们的话说:

> 它们非常适合横向扩展工作负载，在这种情况下，您可以在一组较小的实例之间共享负载。这包括容器化的微服务、web 服务器、开发环境和缓存车队。

对于 EC2 上的 web 应用来说，这似乎是一种经济高效的横向扩展方式

*   AWS 鞭炮(命名部门需要治疗:D)是一种技术，用于内置安全和隔离的微型虚拟机。出于显而易见的原因(节省资金和能源，提高安全性)，所有的云公司都在朝着同一个方向前进，尽管有所不同，例如，见 [Cloudfare 的隔离](https://blog.cloudflare.com/cloud-computing-without-containers/)。Firecraker 使您能够在 125 毫秒内启动 VM，只占用 5MB，Lambda 已经使用了它，您可以在同一个实例上运行多个。啊，而且是[开源用 Rust 写的](https://github.com/firecracker-microvm/firecracker)。你可以在这里了解更多:[https://AWS . Amazon . com/blogs/AWS/cracker-lightweight-virtual ization-for-server less-computing/](https://aws.amazon.com/blogs/aws/firecracker-lightweight-virtualization-for-serverless-computing/)-在我看来这是一个巨大的进步

AWS 显然是要统治世界了([卫星即服务](https://techcrunch.com/2018/11/27/aws-launches-a-base-station-for-satellites-as-a-service/)？[区块链即服务](https://techcrunch.com/2018/11/28/amazon-gets-into-the-blockchain-with-quantum-ledger-database-managed-blockchain/)？)但我想知道他们每年发布的数百个功能中，有多少能真正保持、改进和支持。“让我们同时进入尽可能多的市场”的感觉是显而易见的，同样的原则也在驱动着实际的亚马逊公司。

## 微软连接()；

*   Azure Functions 获得 [Python 3.6 支持](https://azure.microsoft.com/en-us/updates/azure-functions-python-support-public-preview-2/)(预览版中)。AWS (Java，Go，PowerShell，Node.js，C#，Python 和 Ruby，以及其他所有东西的新运行时)和 Azure (C#，JavaScript，F#，Java，Python，PHP，Bash，PowerShell)显然都在努力支持尽可能多的运行时。谷歌云只支持 JavaScript，最近还支持 Python 3.7。

*   Azure 无服务器社区库:一个现成可用的[开源库](https://azure.microsoft.com/en-us/updates/serverless-community-library-is-in-preview/)。

*   CORS 支持 Azure 宇宙数据库。在我看来，Cosmos DB 是数据库技术中最有趣的部分之一。这是一个全球分布式数据库，具有“无限”可扩展性、最小延迟，并且可以通过许多不同类型的 API 进行访问(作为传统的 SQL 数据库、graph 数据库、Mongo 数据库等)。好了，现在我们可以直接从 JavaScript 前端使用它:[Azure Cosmos DB 中的 CORS 支持](https://azure.microsoft.com/en-us/updates/cors-support-in-azure-cosmos-db/)

我觉得 Azure 应该得到更多的关注:-)

## Google 颤振直播

*   Flutter ( [现在 1.0](https://developers.googleblog.com/2018/12/flutter-10-googles-portable-ui-toolkit.html) )可以[添加到现有的原生应用](https://github.com/flutter/flutter/wiki/Add-Flutter-to-existing-apps)

*   最后 Flutter 可以[使用 WebView(在 Android 和 iOS 上都可以)](https://github.com/flutter/plugins/tree/master/packages/webview_flutter)

*   谷歌正在招聘 Flutter 开发人员。我知道这附近有几家，所以可能有人会感兴趣:D:

[![](img/a65814a0ef70705add015957b76579c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cCRHpZXN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uimrg7qh8zfa5qs6gqu8.png)

(截图来自[https://flutter.io/](https://flutter.io/))

你呢？你觉得什么有趣？