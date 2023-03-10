# AWS DevOps Pro 认证博文系列:配置管理和基础设施代码简介

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-configuration-management-and-infrastructure-as-code-introduction-492j>

埃文·德沃金在 Unsplash 上拍摄的照片

## 考试指南怎么说？

要通过此域，您需要了解以下内容:

*   根据部署需求确定部署服务
*   根据业务需求确定应用程序和基础架构部署模式
*   在资源供应自动化中应用安全概念
*   确定如何在部署上实现生命周期挂钩
*   应用使用 AWS 配置管理工具和服务管理系统所需的概念

该领域占考试总分数的 19%。

## 有哪些相关的白皮书？

根据 DevOps 的 [AWS 白皮书](https://aws.amazon.com/whitepapers/#dev-ops),我们应该查看以下文档:

*   [基础设施作为代码(39 页)](https://d1.awsstatic.com/whitepapers/DevOps/infrastructure-as-code.pdf)
*   [AWS 上的 DevOps 简介(20 页)](https://d1.awsstatic.com/whitepapers/AWS_DevOps.pdf)
*   [实践持续集成和持续交付(32 页)](https://d1.awsstatic.com/whitepapers/DevOps/practicing-continuous-integration-continuous-delivery-on-AWS.pdf)
*   [詹金斯上 AWS (48 页)](https://d1.awsstatic.com/whitepapers/jenkins-on-aws.pdf)
*   [用 PowerShell 将 Windows Server 导入亚马逊 EC2(20 页)](https://d1.awsstatic.com/whitepapers/DevOps/import-windows-server-to-amazon-ec2.pdf)

## 该领域涵盖哪些服务和产品？

有用的[https://aws.amazon.com/devops/#infrastructureascode](https://aws.amazon.com/devops/#infrastructureascode)

*   这是一种模板语言，允许你编写你的基础设施。这是这个领域的“基础设施代码”部分。
*   OpsWorks -该服务提供 Chef 和 Puppet 的托管版本。这两个都是行业标准的配置管理系统。
*   [Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/) -是 AWS 的平台即服务(PaaS)产品。
*   [AWS Lambda](https://aws.amazon.com/lambda/)——运行微服务/无服务器功能/时髦词 bingo 的服务
*   AWS ECS -托管集装箱服务。IaC(已编纂)
*   [AWS 配置](https://aws.amazon.com/config/) -审计您的 AWS 服务。
*   [AWS 托管服务](https://aws.amazon.com/managed-services/) -让 AWS 来管理您的 AWS！

## 其他类型的文档呢？

如果你有时间，一定要阅读用户指南，但是它们通常有几百页。或者，使用常见问题来熟悉这些服务:

*   [云的形成](https://aws.amazon.com/cloudformation/faqs/)
*   OpsWorks 为他们的各种产品提供了多个常见问题解答[厨师自动化](https://aws.amazon.com/opsworks/chefautomate/faqs/?nc=sn&loc=5)、[木偶企业](https://aws.amazon.com/opsworks/puppetenterprise/faqs/?nc=sn&loc=5)和[栈](https://aws.amazon.com/opsworks/stacks/faqs/?nc=sn&loc=5)
*   [弹性豆茎](https://aws.amazon.com/elasticbeanstalk/faqs/)
*   [AWSλ](https://aws.amazon.com/lambda/faqs/)
*   [AWS ECS](https://aws.amazon.com/ecs/faqs/)
*   [AWS 配置](https://aws.amazon.com/config/faq/)
*   [AWS 托管服务](https://aws.amazon.com/managed-services/faqs/)

你们都应该知道 API

*   [云的形成](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/Welcome.html)
*   OpsWorks 有两个 API[栈](https://docs.aws.amazon.com/opsworks/latest/APIReference/Welcome.html)和[配置管理](https://docs.aws.amazon.com/opsworks-cm/latest/APIReference/Welcome.html)
*   [弹性豆茎](https://docs.aws.amazon.com/elasticbeanstalk/latest/api/Welcome.html)
*   [AWSλ](https://docs.aws.amazon.com/lambda/latest/dg/API_Reference.html)
*   [AWS ECS](https://docs.aws.amazon.com/AmazonECS/latest/APIReference/Welcome.html)

AWS 托管服务没有 API，因为这是专业或技术服务产品。

在恐慌之前，您将开始发现 API 动词的一种模式。

和 CLI 命令

*   [云的形成](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/index.html)
*   OpsWorks 有两个命令 [opswork](https://docs.aws.amazon.com/cli/latest/reference/opsworks/index.html) 和 [opswork-cm](https://docs.aws.amazon.com/cli/latest/reference/opsworks-cm/index.html)
*   [弹性豆茎](https://docs.aws.amazon.com/cli/latest/reference/elasticbeanstalk/index.html)
*   [AWSλ](https://docs.aws.amazon.com/cli/latest/reference/lambda/index.html)
*   [AWS ECS](https://docs.aws.amazon.com/cli/latest/reference/ecs/index.html)

AWS 托管服务没有 CLI，因为没有相应的 API。

与 API 一样，命令也有模式。

## Configu-什么？下一个是谁？

在之前的领域中，我们了解到 SDLC(特别是持续交付管道)确保我们的代码完整性以一致的方式反复测试。

现在，我们将了解如何实现类似于底层基础设施的东西，为我们构建和托管应用程序提供动力。

配置管理是一种处理服务器更改的系统化方法，可以随着时间的推移保持完整性。需要记住的关键点是，我们经常谈论维护大量服务器，即不止一台服务器。手动执行此操作会带来遗漏步骤和环境不一致的风险。

通过自动化服务器构建和维护流程，我们降低了这种风险。虽然您可以通过一系列 shell 脚本和 ssh 自己完成这项工作，但最好使用专用工具，一些流行的选择是 Puppet、Chef、and、Salt Stack 和 Ansible。

如果配置管理可确保我们的服务器安装了正确版本的操作系统，并包含正确的软件来运行，那么基础设施即代码可确保*鼓声*基础设施的供应作为可复制的步骤来完成。你可以在这个领域找到的工具有 Terraform、Azure Resource Manager，当然还有 CloudFormation。

作为代码的配置管理和基础设施通常被表达为模板或编程语言。这两者都是使用版本控制系统(如 Git)来跟踪变更的理想选择。

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*