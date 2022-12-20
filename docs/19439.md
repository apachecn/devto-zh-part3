# duplo Live:AWS 托管的 Heroku++体验

> 原文：<https://dev.to/duplocloud/duplo-live-a-heroku-experience-for-aws-hosting-2igi>

我们很高兴地宣布 Duplo live 的公开预览，Duplo live 是 DuploCloud 的一个版本，它使您能够在 AWS 上部署应用程序，甚至不需要 AWS 帐户，更不用说设置基础设施了。你所需要的只是你的电子邮件地址(和付费订阅的信用卡)。用户仍然可以访问 AWS 控制台以及与其应用程序相关的所有方面。实际上，这是一个点击和部署的解决方案。每个帐户价值 100 美元的免费试用是可用的。参观[https://www.duplocloud.com](https://www.duplocloud.com/)开始吧。

对于不了解 [DuploCloud](https://www.duplocloud.com/) 的读者来说，它是 AWS 之上的平台即服务抽象，这样开发人员和开发者可以使用简单的声明性接口在 AWS 上部署应用程序，该接口抽象出诸如 VPC、子网 IAM 策略等底层基础架构细节。它还提供 CI/CD、日志编排、监控、审计等功能，作为 E2E 应用程序托管平台。DuploCloud 的核心技术是它能够接受应用程序需求，并自动生成所需的 AWS 配置。其基础是**【租户】**的概念，这是一个网络和 IAM 安全边界，是资源的逻辑容器和计费单位。一个或多个用户可以访问每个租户。在该租户内创建的所有 AWS 资源都只能由该租户内的 EC2 实例访问。ELB 是个例外，因为它可以从租户外部访问。如果删除了一个租户，该租户内的所有资源都会自动清理。租户不是预烘焙模板。在创建时，租户没有资源或策略。用户登录租户环境并在后台部署应用程序。基础架构策略由系统根据应用程序自动制定。

在 Duplo live 中，我们为每个用户分配一个独特的独立租户。所有租户都是在我们管理的同一个 AWS 帐户中创建的。每个用户为他的资源使用份额付费。

# **与英雄**比较

Heroku 给我们和世界上许多开发者留下了深刻的印象。许多人可能想知道，是否会有一种解决方案，通过使用 S3、DynamoDB、ELB、SQS、SNS、Kinesis 等原生 AWS 服务，提供在 AWS 上部署和管理应用程序的相同体验。如今，在 Heroku 中，如果需要 AWS，他必须在自己的 AWS 帐户中设置自己的 AWS 资源，并将访问密钥作为通用环境变量导入 Heroku 应用程序。Heroku 本身并不知道。它们实际上归 salesforce 所有。

## Heroku 极限和 DuploCloud 解

Heroku 是一个纯粹的 PAAS，其中用户应用程序与同一主机中的其他应用程序协同运行，因此用户对底层主机没有控制权。这有时会有限制，我们经常可以看到人们从 Heroku 开始，但最终由于可伸缩性的原因而转向 AWS。DuploCloud 只是一个抽象和策略翻译器，没有这些限制，同时保留了 Heroku 的易用性。在 DuploCloud 中，每个租户都有自己的主机(EC2 实例),因此用户可以像在本地 AWS 中一样控制主机，即启动、停止、实例类型、根访问、操作系统等。此外，AWS 服务，如 S3，社交网络，SQS，DynamoDB，Kinesis 等都是本地管理的，用户可以访问 AWS 控制台，进一步方便。Duplo 的 CI/CD 组件与 GitHub 集成。

因此，我们相信，通过 Duplo live，我们已经在 AWS 上创建了无缝的开发人员体验，而没有纯 PAAS 平台的限制。

帖子[Duplo Live:AWS 主机的 Heroku++体验](https://duplocloud.com/blog/duplo-live-a-heroku-experience-for-aws-hosting/)首先出现在 [DuploCloud](https://duplocloud.com) 上。