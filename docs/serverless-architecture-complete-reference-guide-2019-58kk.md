# 无服务器架构–2019 年完整参考指南

> 原文：<https://dev.to/simon_sugob/serverless-architecture-complete-reference-guide-2019-58kk>

无服务器架构(也称为无服务器计算或功能即服务，FaaS)，让我们离独立于人类更近了一步，让我们离机器依赖更近了一步。对于许多人来说，无服务器是他们第一次接触公共云。

## 什么是无服务器架构？

尽管名字如此，无服务器架构并不意味着它在没有服务器的情况下运行代码。在此解决方案中，公司无需购买、租赁或供应服务器/虚拟机来运行后端代码。这是一种无需基础设施管理即可构建和运行应用和服务的方式。

由于术语“无服务器”令人困惑，而且对于什么是无服务器没有一个明确的观点，我鼓励您阅读 Mike Roberts 的文章。

## 无服务器架构的演进

*‘server less 是云平台向纯云-原生代码方向的进化’。*

它是云平台几次迭代的顶点。这一演变始于数据中心的物理机，并通过基础设施即服务(IaaS)、平台即服务(PaaS)和容器即服务(CaaS)发展为功能即服务(FaaS)。在 Jason McGee (IBM 的首席技术官)这段 4 分钟的短片中，你可以找到最好的解释:[https://www.youtube.com/embed/3vaqTIIcfvY](https://www.youtube.com/embed/3vaqTIIcfvY)

## 为什么选择无服务器架构？

无服务器架构极大地帮助开发人员更专注于他们的核心产品，而不是基础设施。否则，开发人员仍然会担心基础设施的实现、维护、调试和监控，无论是在云中还是在现场管理它们。使用 AWS Lambda、Google Cloud Functions、Firebase 或 Microsoft Azure Functions 等无服务器服务，他们将负责物理硬件、虚拟机操作系统以及 web 服务器，而您——作为开发人员——只需担心一件事——您的代码。

[![traditional vs serverless](img/c14e6a47dbca72201843df0c2e355df1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zvvk7HLG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.hiredevops.org/wp-content/uploads/2019/05/traditional-vs-serverless-architecture-800x596.jpg)

## 去无服务器便宜吗？

看情况。如果您有少量的功能需要托管，您应该考虑切换到无服务器。拥有一个更复杂的应用程序，无服务器架构仍然是有利可图的，但是你需要用不同的方式来设计你的应用程序。如果您有一个现有的应用程序，这可能是不切实际的。在这种情况下，最好的解决方案是随着时间的推移将应用程序的较小部分迁移到无服务器功能中。我遇到了一个聪明的汽车类比，由[Luciano Mammino([https://www . slide share . net/loige/building-a-server less-company-with-nodejs-react-and-the-server less-framework-js day-2017-verona](https://www.slideshare.net/loige/building-a-serverless-company-with-nodejs-react-and-the-serverless-framework-jsday-2017-verona))解释是否要无服务器。

汽车 95%的时间都是停着的(财富)。这辆车你用了多少时间？
[![is it cheaper to go serverless car analogy](img/88a43cdc66ecfbd5e70a3772d13263bc.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--89PzkAXB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.hiredevops.org/wp-content/uploads/2019/05/Is-It-Cheaper-To-Go-Serverless-car-analogy-1200x900.png)

## 什么是功能即服务(FaaS)？

FaaS 是无服务器架构的实现，工程师可以在其中部署单独的功能或业务逻辑。它允许开发人员“在运行中”编写和更新一段代码，然后执行这段代码来响应一个事件，例如用户在 web 应用程序中单击一个元素。这有助于扩展代码，是实现微服务的一种经济高效的方式。

换句话说，开发人员现在可以将服务器拆分成一堆可以自动独立扩展的功能，而不是扩展一个单一的 REST 服务器来处理潜在的负载。如果您熟悉微服务，这张图片可能会有所帮助:

[![what is Faas](img/ed646bfb1cb99f062427af777f7e2b61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0bBqxZMm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.hiredevops.org/wp-content/uploads/2019/05/What-is-function-as-a-service-FaaS-1200x900.png)

一些人认为，无服务器应该被称为功能即服务(FaaS)，虽然这不是一个坏名字，但无服务器是一个更广泛的概念，而不仅仅是运行在云中的短暂功能。了解原因？:[https://www.youtube.com/embed/RlzY81LLb3A](https://www.youtube.com/embed/RlzY81LLb3A)

**FaaS 的主要属性有:**

*   独立的服务器端逻辑功能
*   短暂的(可能只持续一次调用)
*   无国籍的
*   事件触发的
*   默认情况下可伸缩
*   完全由云供应商(第三方)管理

## FaaS 和 PaaS 有什么区别？

PaaS 使部署整个应用程序变得容易。PaaS 提供商负责供应服务器，并将您的应用程序部署到服务器上。

**功能即服务(FaaS)** 让您能够部署基本上是单一功能或应用程序的一部分。你可以将你的应用分解成独立的小程序或功能，而不是在平台即服务平台上运行的单一应用。

## 无服务器基础设施提供商&操作指南链接

在你的机器上安装了无服务器框架之后，下一件事就是选择你想要运行无服务器服务的地方。从以下列表中选择您的计算提供商，并遵循位于[https://serverless.com/framework/docs/getting-started/](https://serverless.com/framework/docs/getting-started/)的无服务器公司提供的精彩指南。

[![Serverless Infrastructure Providers](img/f8e083dbf5eecf744759440a56c3c7b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MmjJygA1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Aav7m2FyOxomYk1jECLgeuw.png)

## 无服务器设计的五大原则

如果您决定使用无服务器方法创建整个系统(后端和前端),以下原则适用。如果您正在构建其他类型的系统(用于转换文件的管道)，与前端相关的原则将不适用。

*   使用计算服务按需执行代码
*   编写单一用途的无状态函数
*   设计基于推送、事件驱动的管道
*   创建更厚、更强大的前端
*   接受第三方服务

请访问[https://techbeacon . com/enterprise-it/essential-guide-server less-technologies-architectures](https://techbeacon.com/enterprise-it/essential-guide-serverless-technologies-architectures)了解更多信息。

## 无服务器的最佳用例

根据无服务器公司的[调查](https://serverless.com/blog/2018-serverless-community-survey-huge-growth-usage/),这里列出了无服务器最适合的情况:

[![Serverless use cases](img/5beac398cfe12e6bacf4b1581999a162.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2tL8_P_m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.hiredevops.org/wp-content/uploads/2019/05/serverless-architecture-use-case-1200x960.jpg)

## 但是什么时候不用 Serverless 呢？

以下是 Quora 上的回答:[https://www . Quora . com/What-is-the-best-use-cases-for-server less/answer/Max-Katz](https://www.quora.com/What-is-the-best-use-cases-for-serverless/answer/Max-Katz)

## 无服务器架构的优缺点

[![Advantages And Disadvantages Of Serverless Architecture](img/a5dc0f79debd241458ec640d4cd77f40.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ktp6zLfR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.hiredevops.org/wp-content/uploads/2019/05/Serverless-architecture-Pros-and-Cons.png) 
*下载 PDF:[https://www . Dropbox . com/s/0 wumyqm 3 oadlww 6/server less-architecture-PROS-and-cons . PDF？dl = 0](https://www.dropbox.com/s/0wumyqm3oadlww6/Serverless-architecture-PROS-and-CONS.pdf?dl=0)T7】*

## 总结

无服务器正在快速发展。几个关键的采用指标是去年的两倍。不仅仅是小公司，企业也同样快速地采用无服务器技术来处理关键工作负载。无服务器公司的上述[调查](https://serverless.com/blog/2018-serverless-community-survey-huge-growth-usage/)中有明确的证据表明:

[![serverless architecture importance](img/a619f3c420e0ee8e99b74cae38dae24a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KbHwTECP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.hiredevops.org/wp-content/uploads/2019/05/serverless-architecture-importance-1200x960.png)

## 2019 年最佳云计算大会

与前几年相比，越来越多的人使用多家云提供商。这一事实增加了项目的重要性，如 [CloudEvents](https://cloudevents.io/) 和其他支持供应商选择的计划。至于 2019 年发生的最好的云计算会议，你可以在这里找到[的精选列表。](https://github.com/serverless/cloud-computing-conferences)

## 目前无服务器应用的最大障碍是什么？

操作化无服务器(调试、监控和测试)是目前最大的问题，这显然表明缺乏工具。

[![The Biggest Obstacle To Wider Serverless Adoption Right Now](img/a0d8694b75b55b222da4810c21da5b4a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LiUXZu8D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.hiredevops.org/wp-content/uploads/2019/05/challenges-serverless-architecture-1200x960.jpg)

## 推荐阅读

[https://martinfowler.com/articles/serverless.html](https://martinfowler.com/articles/serverless.html)
[http://Luke angel . co/cross-platform/docker-servless-FAAS-functions-as-a-service/](http://lukeangel.co/cross-platform/docker-servless-faas-functions-as-a-service/)
[https://hacker noon . com/what-is-server less-architecture-what-is-is-its-pros-cons-cc4b 804022 e9](https://hackernoon.com/what-is-serverless-architecture-what-are-its-pros-and-cons-cc4b804022e9)
[https://server less . com/blog/2018-server less-community-survey-huge-growth](https://serverless.com/blog/2018-serverless-community-survey-huge-growth-usage/)