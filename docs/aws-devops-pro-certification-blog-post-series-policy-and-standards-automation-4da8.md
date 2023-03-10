# AWS DevOps Pro 认证博客文章系列:政策和标准自动化

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-policy-and-standards-automation-4da8>

## 考试指南怎么说？

要通过此域，您需要了解以下内容:

*   应用实施日志记录、指标、监控、测试和安全性标准所需的概念
*   确定如何通过自动化优化成本
*   应用实现治理策略所需的概念

该领域占考试总分数的 10% 。

## 该领域涵盖哪些服务和产品？

*   [AWS 服务目录](https://aws.amazon.com/servicecatalog/)
*   [AWS 值得信赖的顾问](https://aws.amazon.com/premiumsupport/technology/trusted-advisor/)
*   [AWS 系统经理](https://aws.amazon.com/systems-manager/)
*   [AWS 组织产品页面](https://aws.amazon.com/organizations/)
*   [AWS Secrets Manager 产品页面](https://aws.amazon.com/secrets-manager/)
*   [AWS Macie 产品页面](https://aws.amazon.com/macie/)
*   [AWS 证书管理器产品页面](https://aws.amazon.com/certificate-manager/)

## 其他类型的文档呢？

如果你有时间，一定要阅读用户指南，但是它们通常有几百页。或者，使用常见问题来熟悉这些服务:

*   AWS 服务目录[常见问题解答](https://aws.amazon.com/servicecatalog/faqs/?nc=sn&loc=6)
*   AWS 可信顾问[常见问题解答](https://aws.amazon.com/premiumsupport/technology/trusted-advisor/faqs/)
*   AWS 系统经理[指南](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html) / [常见问题解答](https://aws.amazon.com/systems-manager/faq/)
*   AWS 组织[指南](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html) / [常见问题解答](https://aws.amazon.com/organizations/faqs/)
*   AWS 秘密管理器[指南](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) / [常见问题解答](https://aws.amazon.com/secrets-manager/faqs/)
*   AWS Macie [指南](https://docs.aws.amazon.com/macie/latest/userguide/what-is-macie.html) / [常见问题解答](https://aws.amazon.com/macie/faq/)
*   AWS 证书管理器[指南](https://aws.amazon.com/certificate-manager/getting-started/) / [常见问题解答](https://aws.amazon.com/certificate-manager/faqs/)

你们都应该知道 API

*   [AWS 服务目录 API](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_Reference.html)
*   [AWS 值得信赖的顾问](https://docs.aws.amazon.com/awssupport/latest/user/trustedadvisor.html?nc2=type_a)
*   [AWS 系统管理器 API](https://docs.aws.amazon.com/systems-manager/latest/APIReference/Welcome.html)
*   [AWS 组织 API](https://docs.aws.amazon.com/organizations/latest/APIReference/Welcome.html)
*   [AWS 秘密管理器 API](https://docs.aws.amazon.com/secretsmanager/latest/apireference/Welcome.html)
*   [你们有 API](https://docs.aws.amazon.com/macie/1.0/APIReference/Welcome.html) 吗

在恐慌之前，您将开始发现 API 动词的一种模式。

和 CLI 命令

*   [AWS 系统管理器 CLI](https://docs.aws.amazon.com/cli/latest/reference/ssm/index.html)
*   [AWS 组织 CLI](https://docs.aws.amazon.com/cli/latest/reference/organizations/index.html)
*   [AWS Secrets Manager CLI](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/index.html#cli-aws-secretsmanager)
*   [你们有 CLI](https://docs.aws.amazon.com/cli/latest/reference/macie/index.html) 吗

与 API 一样，命令也有模式。

## 百分之十

我决定将围绕该领域的所有服务都放在本简介中，希望您了解这些服务、它们的用途和使用情形，而不是深入了解它们的实现。

虽然这个领域只占 10 %,但了解这些服务可能不会有什么坏处，它可能会在你参加考试时产生及格与不及格的差别。

### 什么？

**AWS 服务目录**...

*   允许您建立用户可以使用的产品(AWS 资源)目录。
*   通过使用 API，您可以通过自己的前端而不是 AWS 控制台来提供这些服务
*   使用以下术语
    *   产品——是由管理员创建的基于 AWS 资源的应用程序堆栈吗
    *   投资组合——是产品的集合
    *   用户-无需访问 AWS 或控制台即可启动这些产品

**AWS Trusted Advisor** 是一项服务，它提供一个仪表板来报告 AWS 资源是否符合当前最佳实践。报告分为以下几类:

*   成本优化-将报告任何低或未充分利用的资源，以节省您的钱
*   性能-将报告对资源性能有影响的配置的任何方面
*   安全性-将报告任何与安全性相关的问题，即根帐户上未启用 MFA
*   容错-将报告任何不容错的资源
*   服务限制-将报告接近达到服务限制的资源

您将获得哪些报告取决于您的支持计划。

基本/开发人员-获得七个核心检查(六个来自安全和所有服务限制报告)
业务/企业-获得所有报告和检查

**AWS Systems Manager** 是一项基础设施管理服务，可以执行以下任务:

*   清单-允许您收集有关实例和安装在实例上的软件的信息。
*   配置符合性-扫描您的实例，检查补丁程序符合性和配置不一致性
*   自动化-自动化日常或重复的任务或报告
*   运行命令-允许您对单个或一组资源执行命令
*   会话管理器简化了与资源的连接，无需打开入站端口、设置堡垒服务器或管理 SSH 密钥
*   补丁管理器——允许您立即或通过维护窗口向实例部署补丁和更新
*   维护窗口-允许您安排预先安排的停机
*   分发服务器是一种软件包分发机制
*   状态管理器，用于存储当配置发生变化时可以重新应用于资源的策略
*   参数存储(机密和配置数据)

**AWS 组织**允许您根据自己的层级或职能角色(安全、合规、运营、开发人员和财务)来组织您的环境。

AWS Secrets Manager 使用 KMS 的密钥加密静态秘密。秘密可以是数据库凭证、密码和第三方 API 密钥。您可以通过控制台服务、CLI、API 和 SDK 存储和控制访问。

机密可以通过时间表自动轮换

您可以存储的机密类型有:

*   RDS 数据库的凭据
*   其他数据库的凭据
*   其他类型的秘密(API 密钥、密码等)

**亚马逊 Macie** 利用机器学习对 AWS 中的敏感数据进行自动识别、分类和保护。它承认个人身份信息(PII)或知识产权。它目前保护 S3，但数据存储相关的服务正在规划的未来。

**AWS 证书管理器**AWS 中集中管理的证书。它与 AWS 服务有很好的集成，允许它代表 AWS 服务提供 TLS/SSL 证书。当不需要使用可信的互联网根 ca 时，您还可以使用它来建立一个私人证书颁发机构，该机构可以在组织内部。

### 为什么？

**AWS 服务目录**允许您隐藏云基础设施的具体实施。通过为您的用户提供单独的前端，您可以删除与 AWS 帐户的直接关联。

**AWS Trusted Advisor** 是一款自动化工具，用于收集有关您的 AWS 环境的信息，并针对未按照最佳实践进行配置的环境进行报告。它也是一个节省成本的工具，建议删除未使用的资源或将其降级。完整的检查列表可以在 [AWS 支持](https://aws.amazon.com/premiumsupport/technology/trusted-advisor/best-practice-checklist/)页面上找到。

**AWS Systems Manager** 是一款工具，用于集中您可能希望针对基础设施执行的各种任务和活动。还集成了 CloudWatch 仪表盘和可信顾问报告，使其成为系统管理的一站式商店。

**AWS 组织**允许您根据自己的业务职能映射对 AWS 资源的访问。最简单的例子就是只允许开发人员访问开发环境，或者只读访问生产环境。

**亚马逊 Macie** 利用机器学习进行个人识别

### 什么时候？

**AWS 服务目录**应该在您希望您的用户拥有一组基于 AWS 服务的特定产品，但不想让他们访问 AWS 控制台或特定 IAM 帐户时使用。

**当您需要确定您是否遵循最佳实践时，应该使用 AWS 可信顾问**。

**AWS Systems Manager** 是一款企业工具，允许您通过属性(AMI 映像 id、操作系统类型、实例类型)识别资源，而不是导航到特定资源。

*AWS 组织*当您需要根据 AWS 资源和 IAM 用户映射您的业务功能时，组和角色是不够的。

**Amazon Macie** 应由需要谨慎处理客户数据的组织使用，如医疗保健或政府。数据量不容易由数据管理团队管理或由安全/法规遵从性业务职能部门监控。

**AWS 机密管理器**当您不想对机密进行硬编码或在服务器上存储敏感数据，或者有法规要求定期循环机密时。

**AWS 证书管理器**当您需要提供 SSL/TLS 证书或需要私人证书颁发机构时。

### 如何？

如果没有具体的说明，则假定该服务可以通过 AWS 控制台获得。

**AWS 服务目录**需要开发，因此应参考[开发者指南](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_Reference.html)。

**AWS 可信顾问**可通过 AWS 控制台获得。除了仪表板之外，您还可以根据角色(计费、运营和安全)向收据发送每周报告。

**AWS 系统管理器**可通过 AWS 控制台获得，您可以通过基于标签(资源类型、操作系统等)搜索资源来创建要管理的组。要成为受管实例，需要在资源上安装代理(以及 IAM 实例配置文件)。

Unsplash path(我用什么术语来获得封面图片):无聊

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*