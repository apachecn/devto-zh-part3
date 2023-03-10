# 你应该在无服务器上运行你的整个网站吗？

> 原文：<https://dev.to/jsitapara/should-you-run-your-entire-website-on-serverless-3g7o>

**这可行吗？使用 AWS Lambda 这样的工具来完成这个任务是正确的选择吗？**

或者它更适合服务的较小的高使用率领域？

做这件事的理想方式是什么？

如果这些是你最近一直在思考的问题，我有一个答案给你。是的，这是可能的，是的，人们这样做了。它可能不太适合所有的项目，但是运行一个网站可能是你开始使用无服务器的最好例子之一。

所有的无服务器提供商都工作得很好，这主要归结于生态系统对功能的偏好。我上一篇关于如何选择合适的无服务器提供商的文章？可以帮你。此外，如果您计划运行大规模的 FaaS 基础设施，您可能不会对配置进行硬编码，而是使用类似 Serverless 的框架来为您处理部署和组合。

查看 [https://3factor.app](https://3factor.app) 获得一个通用的架构模式(hasura 是可选的)。TL；灾难恢复请求通过 API 网关，并将路径链接到各个功能。根据 API 网关，您可以将所有请求处理、负载平衡、身份验证和日志记录保持在该级别，并且所有功能都保持集中和隔离。

[![Should you Run your Entire Website on Serverless?](img/3798fdfc2827aca8d7cdac7b00578d78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IH1k1SYV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://3factor.app/3factor-migration.png)

有许多模式取决于个人偏好，但我建议浏览无服务器博客的代码模式帖子，了解如何构建无服务器架构。

这最终成为 API 设计中的一个很好的练习:如何保持功能的集中但可重用(如果有联系，在 FaaS 配置中“集中”倾向于优先)，需要什么领域模型和动作，谁是我的消费者。

我对如何构造你的函数的另一个建议是考虑它们是可组合的单元。如果你的评论函数中需要用户，那么就调用你的用户函数并获取必要的数据。

此外，出于安全性和可重用性的原因，将“消费者端点函数”与“记录函数声明”(那些涉及数据存储的函数)分开可能是有用的。对于较小的应用程序来说，这有点过了，但随着应用程序的增长，这被认为是一个很好的安全实践。

**想要一些例子吗？**

在最近举行的 ServerlessConf 上，[‘一位云专家’](https://acloud.guru/)给出了一个无服务器承诺的证明，称他们从未因性能原因而被要求改变其架构。他们运行着 **287 个 Lambda 函数，19 个微服务，数据量为 3.68 TB，每月成本仅为 580 美元**。再读一遍！

Bustle.com 完全在无服务器上运行。它管理着**每月 5200 万的访客，由使用 12 个 API**构建的无服务器架构提供支持。在任何一天，它们收集 1 亿个事件，这些事件都被发送到 API 网关端点。这些由 Lambda 函数处理，清理成规范化的事件(例如，服务器设置时间戳、用户代理信息和添加设备分段)，然后写入 Kinesis 流。然后，Kinesis 流的消费者可以在 Redis 中看到内容的实时原始计数和聚合。

[![Bustle](img/1347095c92a70bb803a01c2cc5997b56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iSUnODM7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://thenewstack.io/wp-content/uploads/2016/09/bustle-arch-map.png)

听起来足够有趣？是啊！然而，如果你运行一个有大量访问者的网站，那么在实施新的计划时就有一点猜测的空间。如果运营一个网站是你不确定的事情，这里有一些有趣的无服务器的例子。

如果你已经在研究这些例子，让我们在评论区讨论一下。