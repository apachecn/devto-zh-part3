# AWS DevOps Pro 认证博文系列:AWS X 射线

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-aws-x-ray-1e30>

乌曼诺德在 [Unsplash](https://unsplash.com/photos/KeVKEs1_RDU) 拍摄的照片

## 概不退换

使用 AWS 要花钱，其中一些服务可能不属于 AWS 免费层。你可以通过拆除你在学习时创造的任何东西来降低成本，但仍然有可能产生巨额账单，所以要注意你设置的实例！

我非常幸运能够使用我雇主的 AWS 帐户。你应该问问你工作的地方，是否可以做类似的安排作为你学习的一部分。

## 谁快窒息征服谁

当我试图完善我的每个领域的心智模型时，博客帖子的格式很容易改变，所以请确保定期重温博客帖子。

## 什么？

AWS X-Ray 是一种应用程序性能监控(APM)工具，类似于 New Relic、Solarwind TraceView 或微软应用程序洞察。APM 工具超越了通常的捕获指标级别，而不是执行跟踪，跟踪中包含大量关于应用程序请求、代码块响应时间和延迟的信息。简而言之，它们通常可以让您了解在给定的时间内应用程序是如何处理请求的。

除了围绕应用程序的完整数据捕获之外，X-Ray 还可以构建服务地图，显示应用程序与之交互的各种组件。[入门指南](https://eu-west-2.console.aws.amazon.com/xray/home?region=eu-west-2#/getting-started.html)中的示例应用程序展示了 Java 应用程序、DynamoDB、SNS 和外部 API 之间的交互。

以下 AWS 服务(在本次考试范围内)提供与 X 射线的集成:

*   自动气象站λ
*   英国文学学士（bachelor of english literature）
*   AWS 弹性豆茎

X 射线集成服务的完整列表可在[开发者指南](https://docs.aws.amazon.com/xray/latest/devguide/xray-usage.html#xray-usage-services)中找到

其他资源:

*   [AWS X 射线产品页面](https://aws.amazon.com/xray/)
*   [AWS X 射线常见问题解答](https://aws.amazon.com/xray/faqs/)
*   [AWS X 射线导轨](http://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html)
*   [AWS X 射线 API](https://docs.aws.amazon.com/xray/latest/api/Welcome.html)
*   [AWS X 射线 CLI](https://docs.aws.amazon.com/cli/latest/reference/xray/index.html)

## 为什么？

以前，应用程序的调试可以通过事后分析转储、堆栈跟踪来完成。这通常是离线完成的。随着 APMs 的出现，您现在可以在运行中查看应用程序的健康状况。

## 什么时候？

x 射线仅在生产环境中有意义，因为它被用作故障排除工具。在其他环境中作为学习辅助手段可能会有一些价值。

## 如何？

在[入门](https://eu-west-2.console.aws.amazon.com/xray/home?region=eu-west-2#/getting-started.html)指南中有一个很好的教程。强烈建议您熟悉 X 射线的功能集。

和往常一样，请记住您可能会为使用本指南开始的资源支付费用。

## API 和 CLI 特性和动词

*特性* *

*   组
*   抽样规则

**动词(CRUD)**

*   创建分组/抽样规则
*   获取组/采样规则
*   更新组/抽样规则
*   删除组采样规则

**离群值**

*   批量获取跟踪
*   获取-加密-配置
*   获取-抽样-统计-汇总
*   获取采样目标
*   获取服务图
*   获取时间序列服务统计数据
*   获取跟踪图
*   获取跟踪摘要
*   上传-加密-配置
*   上传-遥测-记录
*   放置跟踪段

记住，Java 有特定的[API](https://docs.aws.amazon.com/xray/latest/api/Welcome.html)。Net(和核心)、Ruby、Node 和 Python SDKs。

Unsplash 路径(我使用什么术语来获得封面图像):X 射线

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*