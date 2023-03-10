# 什么是无服务器？

> 原文：<https://dev.to/codetips/what-is-serverless-lo>

[![What is Serverless?](img/e3785feed9435acab4fadad79a901f98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---sxXVXb3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/06/todd-quackenbush-701-unsplash.jpg)

如果你错过了过去几年的新闻，无服务器是技术领域的“新新”，除了对开发人员的一些主要好处之外，它还引入了开发范式的重大变化，需要一些时间来适应。

## 什么是无服务器？

让我们先来解决这个流行词及其带来的困惑。当有人提到“无服务器”时，人们会想到两件事。无服务器平台，用于在许多服务提供商上部署和管理应用程序，以及我们今天讨论的无服务器架构。

无服务器架构是一种云计算，用户编写代码，由服务提供商运行，无需用户管理基础设施。

基本上，无服务器为您提供了一种创建应用程序的方式，而无需构建、维护、更新、升级或提供任何类型的基础设施；耗费大量时间和金钱的事情。

有很多服务提供商提供 FaaS(功能即服务)，但是为了让这篇文章简洁明了，我只提到前几个。

*   [AWSλ](https://aws.amazon.com/lambda/)
*   [微软 Azure](https://azure.microsoft.com/en-us/)
*   [谷歌云功能](https://cloud.google.com/functions/)
*   [IBM 云功能](https://www.ibm.com/cloud/functions)——基于 Apache OpenWisk

## 它是如何工作的？

为了每个人的理智，我从现在开始只参考 AWS Lambda，完全承认还有其他 FaaS 提供者，但是因为他们大多数实际上以类似的方式工作，并且因为 AWS Lambda 拥有大约 75%的市场，所以只讨论一个是有意义的。

冒着把事情过于简化的风险，我将尝试描述 AWS Lambda 是如何工作的，而不会深入细节。我已经提到过，你编写你的函数，并上传到你的 AWS 帐户，然后你就完成了，但当它真正得到“启动”时，会发生很多事情。

当一个函数被调用时，会有一个容器自动启动。它的调用和它实际执行函数的时刻之间的时间被称为“冷启动”。

容器将在最后一次调用后保持“温暖”大约 45 分钟，之后将被删除。

服务提供商删除未使用的容器，以允许负载较重的 Lambdas 能够扩展。

[![What is Serverless?](img/cdd494742725ee30bcf1ce5a012f651d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8i376uX0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rvsm59em8ijtn9wt2h4h.png)

*Lambda 函数的剖析- AWS re:Invent 2017*

## 无服务器有什么了不起的？

无论如何，无服务器技术并不新鲜，只是最近随着 Lambda 和 Azure 等 FaaS 提供商的发布，它才被大众所接受。

这项技术最大的吸引力在于成本、可扩展性和开发新功能的速度。

### 成本

[成本](https://dashbird.io/blog/saving-money-switching-serverless/)是该架构的一个巨大优势，因为您只需为调用和执行的持续时间付费。想象一下，有一个像在线商店一样的应用程序，只在白天使用。对于传统的服务器，你必须为它在夜间无人光顾你的商店时处于休眠状态的时间支付相同的费用。

你可以免费获得 100 万个请求，之后，你几乎不用为 AWS Lambda 支付任何费用。

### 可扩展性

也许无服务器的最大好处之一是它能够按需无缝扩展。一旦容器旋转起来，你就可以根据需要调用任意多的 lambdas 假设你没有遇到[并发限制](https://www.bluematador.com/blog/why-aws-lambda-throttles-functions)。

### 发展速度

由于您不管理任何基础架构，部署新功能将变得轻而易举。这就是为什么像网飞和 T2 可口可乐这样需要敏捷的公司严重依赖无服务器的原因。

有成千上万的公司在使用无服务器，这个数字似乎还在不断增加，因为无服务器的流行似乎只在开发者中增长；同比采用率达到 75%以上。

* * *

我希望我已经能够为所有希望开始使用无服务器的新手提供一些帮助。

请务必尽快查看[代码提示](https://www.codetips.co.uk/serverless/what-is-serverless/www.codetips.co.uk)，了解与无服务器相关的下一篇文章。我们将探索无服务器、行业工具和 API 示例的不同用例。