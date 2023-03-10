# 太棒了。公共 S3 存储桶以及如何找到它们

> 原文：<https://dev.to/auth0/fantastic-public-s3-buckets-and-how-to-find-them-339l>

不受欢迎的公共 S3 水桶是一个持续的威胁。他们已经(并且仍然)在网络上造成了巨大的破坏。有几个工具可以帮助你的公司找到公共 S3 桶。它们几乎都是独立的脚本或 lambda 函数，通过某种 SDK (Python、Node.js 等)查询 AWS APIs。).

但是，当实施集中式安全时，正如我们在 Auth0 中所做的那样，可以使用数据湖或任何类型的系统/服务来执行该任务，在这些系统/服务中，可以聚合、分析日志并对其采取行动。在这方面，AWS 事件的第一个来源是 CloudTrail。

在互联网上搜索，我们没有找到足够的资源向我们解释 S3 桶可以公开的不同方式，以及如何在原始 CloudTrail 日志中检测它，所以我们开始到处玩，运行测试和构建查询来找出答案。这篇博文将引导你了解我们的过程、我们的发现和我们的解决方案。

[读读☁️](https://auth0.com/blog/fantastic-public-s3-buckets-and-how-to-find-them/?utm_source=dev&utm_medium=sc&utm_campaign=aueng_s3buckes)

[![Fantastic! Public S3 Buckets and How to Find Them](img/919832b9a7c85f56d8b672c7ea37fd2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IGmuXP98--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8ytpmex1qyfboap1poz6.png)