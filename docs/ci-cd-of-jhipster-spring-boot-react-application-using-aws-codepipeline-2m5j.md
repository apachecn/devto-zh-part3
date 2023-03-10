# 使用 AWS 代码管道的 JHipster Spring Boot + React 应用程序的 CI/CD

> 原文：<https://dev.to/pravatk/ci-cd-of-jhipster-spring-boot-react-application-using-aws-codepipeline-2m5j>

最近，我致力于为 JHipster 应用程序创建一个 CI/CD。对于所有不熟悉 JHipster 的人来说，它是一个开发工具，可以生成一个 Spring Boot+React/Angular/Vue web 应用程序([https://www.jhipster.tech/](https://www.jhipster.tech/))。该工具还为不同的数据库自动生成一系列配置，以及用于静态分析的 CI/CD 脚本和 Sonar 配置。

现在，如果贵公司的所有基础架构都在 AWS 上，出于安全等显而易见的原因，您会希望 CI/CD 也是 AWS 基础架构的一部分！咄！。在 AWS 中，我们有 AWS CodePipeline，它可能不像 Jenkins 那样可扩展，但它解决了我们的需求，因为它与 AWS 服务集成得很好。

我们的应用程序需要将开发、QA 和生产环境放在同一个管道中，有多个阶段和批准，我将在一分钟左右的时间里介绍这些。现在，我将逐一介绍每个阶段，以提供更多信息。

Source
从 GitHub repo 中提取代码的第一阶段。您必须在创建这个 stage 时选择 webhook，以便在每次合并时从 GitHub 触发管道。

为了构建代码库，我使用了 AWS CodeBuild。这个阶段的工件是来自 JHipster 应用程序的 war 文件。war 需要部署到具有 tomcat 配置的弹性豆茎上。
beanstalk 的 Tomcat 配置有点棘手。为了成功部署，需要压缩 war 和配置文件。

一旦 war 在构建阶段被压缩，它就位于 S3 并启用服务器端加密。然后这个 ZIP 被推送到 Elastic Beanstalk 进行部署。

集成测试
这个阶段运行一系列为完整的 Web 应用程序配置的 Selenium 测试。

QA
这里我们有一个手动批准步骤，通过电子邮件通知人们进行批准。一旦获得批准，它将部署到 QA 环境中。

对于试运行或生产环境，也可以多次复制同一个 QA 阶段。