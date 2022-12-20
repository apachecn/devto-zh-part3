# AWS DevOps Pro 认证博客文章系列:AWS 配置和托管服务

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-aws-config-and-managed-services-2bbm>

大卫·科瓦连科在 Unsplash 上拍摄的照片

## 概不退换

使用 AWS 要花钱，其中一些服务可能不属于 AWS 免费层。你可以通过拆除你在学习时创造的任何东西来降低成本，但仍然有可能产生巨额账单，所以要注意你设置的实例！

我非常幸运能够使用我雇主的 AWS 帐户。你应该问问你工作的地方，是否可以做类似的安排作为你学习的一部分。

## 谁快窒息征服谁

当我试图完善我的每个领域的心智模型时，博客帖子的格式很容易改变，所以请确保定期重温博客帖子。

## 什么？

我决定将 AWS 配置和 AWS 托管服务放在一起讨论，因为它似乎没有两篇非常短的博文。

AWS Config 是一项允许您评估、审计和评价 AWS 资源配置的服务。在非常基本的层面上，这允许您审计和跟踪对您的环境所做的更改。

AWS 托管服务是 AWS 在 AWS 上管理和操作您的基础设施的服务。没错，AWS 将运行您的基础设施(下一节将讨论一些注意事项)。

其他资源:

*   [AWS 配置产品页面](https://aws.amazon.com/config/)
*   [AWS 配置常见问题解答](https://aws.amazon.com/config/faq/)
*   [AWS 配置开发者指南](https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.html)
*   [AWS 配置 API](https://docs.aws.amazon.com/config/latest/APIReference/Welcome.html)
*   [AWS 配置 CLI](https://docs.aws.amazon.com/cli/latest/reference/configservice/index.html)
*   [AWS 托管服务产品页面](https://aws.amazon.com/managed-services/)
*   [AWS 托管服务常见问题解答](https://aws.amazon.com/managed-services/faqs/)

## 为什么？

AWS 配置侧重于审计、变更控制和合规性。下一节中有一些用例，但这将有助于像金融机构那样受到严格监管的 AWS 用户。

AWS 托管服务通常面向希望以更安全的方式从本地迁移到基于云的服务的大型组织(企业)。虽然服务本身不处理迁移(这是由 AWS 合作伙伴处理的)，但您可能以前见过这种情况:

> 你在一家企业机构工作，权力机构希望你迁移到云。他们不想花钱请专家，所以他们希望你在工作中学习。
> 
> 几个月后，当这项服务灰飞烟灭的时候(因为使用一个 VPC 并让它在互联网上可见似乎是一种快速的工作方式)。当权者最终雇佣专家从头开始设计云架构。然后他开始斥责你不知道你的 EC2s 和 TF2s。

从这个发泄口(抱歉)得到的关键事实是，AWS 将提供一个基于他们自己的最佳实践的基础设施。您可以专注于让连续交付管道工作，并对您的应用程序进行必要的更改，以使它们在 AWS 上工作。

最终，您可以接管这个基础设施，但是这可以在团队有足够的信心这样做的时候完成。

## 什么时候？

AWS 配置的一些建议用法如下:

*   (资源的)发现
*   变更管理
*   持续审计和合规性
*   法规合规性
*   解决纷争
*   证券分析

AWS 托管服务旨在帮助企业迁移到云服务，但不需要亲身体验。

## 如何？

这两项服务没有实用部分。

提醒:AWS 配置将花费金钱，如果你决定玩它，因为它不包括在免费层。

## API 和 CLI 特性和动词

没有 AWS 托管服务部分，因为没有 API/CLI。

虽然有用于 AWS 配置的 API/CLI，但普遍的共识是您只需要知道服务。如果你在考试中遇到了一个需要了解 API 的问题，请告诉我。

Unsplash path(我用什么术语来获得封面图片):垃圾，随机

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*