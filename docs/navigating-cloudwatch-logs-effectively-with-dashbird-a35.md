# 使用 Dashbird 有效导航 CloudWatch 日志

> 原文：<https://dev.to/johndemian/navigating-cloudwatch-logs-effectively-with-dashbird-a35>

为了完成一些严肃的工作，我们通常需要为此做准备。“婴儿第一步，”他们说。在我们所处的环境中，这些“小步骤”是在我们开始主要项目之前需要做的无数小工作。适当的准备(试着快速说 5 遍)是成功的关键，但是在我们达到我们的主要目标后，总会有一些事情要做来保持稳定和流畅。

保持您的应用程序完美运行需要什么？嗯，在许多其他事情中，导航 CloudWatch 日志无疑是其中之一，今天我们将更多地讨论如何使用 Dashbird 有效地导航 CloudWatch 日志。

## 什么是 CloudWatch？

[CloudWatch](https://dashbird.io/free-cloudwatch-alternative/) 本质上是 AWS 云资源的 AWS 监控服务，适用于在 AWS 云上运行的所有应用程序。因此，如果出现问题，CloudWatch 会向您发出警报。CloudWatch 允许您收集和跟踪指标，以便您可以获得系统范围的可见性、资源使用情况、应用程序性能以及整体运行状况。这些见解将使您能够领先于潜在的问题，并保持您的应用程序平稳运行。

借助 CloudWatch，您可以从单一平台以日志和指标的形式收集并访问所有运营数据和性能。这进一步意味着您可以克服监控服务器、网络、数据库等中的单个应用和系统的挑战。CloudWatch 使您能够一起跟踪所有信息(应用程序、服务、基础架构)，并设置警报、日志和事件数据以自动采取行动。

## 日志

Amazon CloudWatch 服务中有三种主要的日志类型:

**1。出售日志** -是由 AWS 服务代表客户发布的日志。亚马逊 VPC 流量日志和亚马逊 Route 53 日志是两种受支持的类型。
2**。AWS 服务发布的日志**——有超过 30 个 AWS 服务向 CloudWatch 发布日志，比如 AWS CloudTrail、AWS Lambda、Amazon API Gateway 等等。
**3。定制日志**——通常来自您的应用程序以及内部资源。
您可以利用 AWS 系统管理器来安装 CloudWatch 代理，甚至使用 PutLogData API 操作来发布安全日志。

## 达什伯德的角色

Dashbird 是一个定制的服务，它为所有 Lambda 函数在每次调用后发出的预先格式化的 CloudWatch 日志提供语法分析。来自这些日志的每一条数据都被格式化成不同的 Lambda 调用事件，并进一步编译成通用的鸟瞰仪表板。将一个模块或额外的代码附加到被监控的函数(这将进一步导致执行延迟)是**而不是**需要的，就像在一些替代服务中一样。

Dashibrd 的有趣之处在于它以一种非常容易导航的方式组织日志。Dashbird 提供的其他额外服务包括 CloudWatch 的实时跟踪日志以及全文搜索。直接来说，Dashbird 收集重要的 CloudWatch 日志，并从那些不重要的指标中挑选出可操作的和有意义的指标，这对于让您的服务成为优秀的服务非常重要。

所有重要的内容都已经在 dashboard 视图中捕捉到了，这对开发人员很有帮助，因为它为他们提供了总体健康状况以及他们的功能和功能利用率的极好概述。

Dashbird 为您提供了一种全新的方式，在一个易于查找的视图中编译各个调用，这本身就是一种体验。如果你每天都在使用 Lambda 函数，你就会知道在整个日志流中搜索，发现在大多数情况下只有一行代码导致了服务中的问题，这是多么令人恼火。

Dashbird 服务的一个重要部分是，它还提供每日更新，以及从过去 24 小时的调用日志中收集的关键兴趣点。所有这些都意味着，即使一切进展顺利，万一发生什么事情，Dashbird 的警报系统也会通过电子邮件或 Slack 让你立即知道。

[![](img/d96ac5c43a570b9b679900c0cfc7a828.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gpFI86zl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/359rey2zxuatbjmkzogs.png)

当提到 CloudWatch 日志时，我们大多数人都会想到视觉噪声，但是让我们总结一下

从我们已经说过的，我们知道 Dashbird 的易用和友好的用户界面肯定会让你每天的日志导航比你现在可能已经习惯的要容易得多。使用 Dashbird 的服务有各种好处，虽然计费是其中之一，因为 Dashbird 几乎不会增加你每月的 AWS 账单，但你应该自己检查其余的费用。看看我们的 Dashbird 调查结果，了解更多关于 Dashbird 可以提供的服务。如果你已经花了无数的时间寻找某人或某事来帮助你浪费时间搜索日志，试试 Dashbird，看看它是如何为你工作的。在下面的评论区告诉我们和我们的读者你的经历。