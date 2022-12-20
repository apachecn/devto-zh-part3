# AWS DevOps Pro 认证博客文章系列:监控和日志记录

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-monitoring-and-logging-15b0>

Angie Muldowney 在 Unsplash 上拍摄的照片

## 考试指南怎么说？

要通过此域，您需要了解以下内容:

*   确定如何设置日志和指标的聚合、存储和分析
*   应用自动化环境监控和事件管理所需的概念
*   应用审核、记录和监控操作系统、基础架构和应用程序所需的概念
*   确定如何实现标记和其他元数据策略

该领域占考试总分数的 15% 。

## 有哪些相关的白皮书？

根据 DevOps 的 [AWS 白皮书](https://aws.amazon.com/whitepapers/#dev-ops),我们应该查看以下文档:

*   [AWS 上的 DevOps 简介(20 页)](https://d1.awsstatic.com/whitepapers/AWS_DevOps.pdf)

## 该领域涵盖哪些服务和产品？

*   [CloudWatch](https://aws.amazon.com/cloudwatch/) -全面了解您的云资源和应用。如果您提供一个资源(至少通过控制台)，它很可能会登录 CloudWatch。
*   [AWS X-Ray](https://aws.amazon.com/xray/) -分析和调试生产、分布式应用。类似的产品还有 Rollbar，Sentry，Azure Application Insights。

来源: [AWS DevOps -监控和记录页面](https://aws.amazon.com/devops/#monitoring)

## 其他类型的文档呢？

如果你有时间，一定要阅读用户指南，但是它们通常有几百页。或者，使用常见问题来熟悉这些服务:

*   [云观察](https://aws.amazon.com/cloudwatch/faqs/)
*   [AWS X 射线](https://aws.amazon.com/xray/faqs/)

你们都应该知道 API

*   [云观察](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/index.html)
*   [AWS X 射线](https://docs.aws.amazon.com/xray/latest/api/Welcome.html)。Java 有特定的 API。Net(和核心)、Ruby、Node 和 Python SDKs。

在恐慌之前，您将开始发现 API 动词的一种模式。

和 CLI 命令

*   [CloudWatch](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/index.html) 。这适用于 AWS CLI 中的命令。独立的 CloudWatch CLI 已被弃用。

X 射线没有命令行界面

与 API 一样，命令也有模式。

## 监控和记录(哈欠)

监控和日志记录是 DevOps 工程师的日常工作。我们使用日志中的数据来发现模式，并使用监控警报来对问题做出反应并减少停机。

这不是最令人兴奋的话题，但却是这个角色的必需品。我希望在未来版本的考试中，这个领域扩展到可观察性。

这个领域包含的服务最少，但毫无疑问，CloudWatch 集成了如此多的服务，因此很好地理解它提供的功能是有意义的。AWS X 射线如果你用过类似的产品，应该是一个更容易掌握的概念(著名的遗言)。

Unsplash 路径(我使用什么术语来获得封面图像):监控(meh)，日志记录

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*