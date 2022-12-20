# AWS DevOps Pro 认证博文系列:SLDC 自动化简介

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-sldc-automation-introduction-1loa>

摇滚猴子在 [Unsplash](https://unsplash.com/photos/R4WCbazrD1g) 上的照片

## 考试指南怎么说？

要通过此域，您需要了解以下内容:

*   应用自动化 CI/CD 渠道所需的概念
*   确定源代码控制策略以及如何实现它们
*   应用自动化和集成测试所需的概念
*   应用安全地构建和管理工件所需的概念
*   确定部署/交付策略(例如，A/B、蓝/绿、淡黄色、红/黑)以及如何使用 AWS 服务实施这些策略

该领域占考试总分数的 21% 。

## 有哪些相关的白皮书？

根据 DevOps 的 [AWS 白皮书](https://aws.amazon.com/whitepapers/#dev-ops),我们应该查看以下文档:

*   [AWS 上的 DevOps 简介(20 页)](https://d1.awsstatic.com/whitepapers/AWS_DevOps.pdf)
*   [AWS 的开发和测试(17 页)](https://d1.awsstatic.com/whitepapers/aws-development-test-environments.pdf)
*   [实践持续集成和持续交付(32 页)](https://d1.awsstatic.com/whitepapers/DevOps/practicing-continuous-integration-continuous-delivery-on-AWS.pdf)
*   [AWS 上的蓝/绿部署(35 页)](https://d1.awsstatic.com/whitepapers/AWS_Blue_Green_Deployments.pdf)
*   [詹金斯上 AWS (48 页)](https://d1.awsstatic.com/whitepapers/jenkins-on-aws.pdf)

## 该领域涵盖哪些服务和产品？

*   [代码提交](https://docs.aws.amazon.com/codecommit/index.html?id=docs_gateway#lang/en_us)——该服务提供源代码控制管理(SCM)。这是您存储源代码并跟踪所做更改(提交)的地方。类似服务的例子:GitHub、Bitbucket、GitLab 和 Azure DevOps。你只有一个版本控制服务(VCS)的选择，那就是 Git。
*   代码构建(Code Build)——这项服务构建代码并生产产品。这通常是持续集成(CI)管道的一部分，因此这是可以运行测试以确保代码稳定的地方。类似服务的例子:特拉维斯 CI，圈 CI，Azure DevOps 和詹金斯。
*   代码部署(Code Deploy)——这项服务可以自动部署你的应用程序，或者将文件发布到网站上。这通常是持续部署(CD)管道的一部分。类似服务的例子:Travis CI、Circle CI、Azure DevOps 和 Octopus。
*   [代码管道](https://docs.aws.amazon.com/codepipeline/index.html?id=docs_gateway#lang/en_us) -该服务提供了一个工作流程，允许更复杂发布步骤。这通常是持续部署(CD)管道的一部分。类似服务的例子:Circle CI、Azure DevOps 和 Jenkins。

精明的读者会发现这些是 AWS 文档页面中开发工具套件的一部分。那么为什么我排除了 CodeStar、X-Ray 和 Tools&SDK 呢？它们对 SDLC 自动化并不重要。

## 其他类型的文档呢？

如果你有时间，一定要阅读用户指南，但是它们通常有几百页。或者，使用常见问题来熟悉这些服务:

*   [代码提交](https://aws.amazon.com/codecommit/faqs/)
*   [代码构建](https://aws.amazon.com/codebuild/faqs/)
*   [代码部署](https://aws.amazon.com/codedeploy/faqs/)
*   [代码流水线](https://aws.amazon.com/codepipeline/faqs/)

你们都应该知道 API

*   [代码提交](https://docs.aws.amazon.com/codecommit/latest/APIReference/index.html)
*   [代码构建](https://docs.aws.amazon.com/codebuild/latest/APIReference/Welcome.html)
*   [代码部署](https://docs.aws.amazon.com/codedeploy/latest/APIReference/index.html)
*   [代码流水线](https://docs.aws.amazon.com/codepipeline/latest/APIReference/index.html)

在恐慌之前，您将开始发现 API 动词的一种模式。

和 CLI 命令

*   [代码提交](https://docs.aws.amazon.com/cli/latest/reference/codecommit/index.html)
*   [代码构建](https://docs.aws.amazon.com/cli/latest/reference/codebuild/index.html)
*   [代码部署](https://docs.aws.amazon.com/cli/latest/reference/deploy/index.html)
*   [代码流水线](https://docs.aws.amazon.com/cli/latest/reference/codepipeline/index.html)

与 API 一样，命令也有模式。

## 词，看什么？

如果您不熟悉 SDLC 自动化，这涉及以下自动化:

*   编译代码或优化静态站点内容的构建过程，docker 映像
*   运行测试来验证构建是否成功
*   将新版本部署到目标，例如应用服务器、应用商店、web 服务器等。这也可能涉及到将构建过程中的工件发布到存储库的 GitHub 发布页面。

通常，启动这些步骤的触发器是对代码的`master`分支的提交或合并。

构建和测试阶段通常由持续集成服务提供，如 Travis CI、Circle CI 或 Jenkins。

部署阶段由 Octopus、Azure DevOps(发布管道)等持续交付服务处理，通常提供 CI 的服务也会处理部署。

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*