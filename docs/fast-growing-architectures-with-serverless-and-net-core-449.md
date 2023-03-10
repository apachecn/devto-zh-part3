# 快速增长的无服务器架构。网络核心

> 原文：<https://dev.to/samueleresca/fast-growing-architectures-with-serverless-and-net-core-449>

*原贴于[https://samueleresca.net](https://samueleresca.net)T3】*

无服务器技术为开发人员将实现投入生产提供了一种快速且独立的方式。这项技术在企业中日益流行，自 2017 年以来，它一直是 ThoughtWorks 技术雷达中的一项试验技术。

下一篇文章的第一部分涵盖了关于无服务器计算的一些一般概念。第二个展示了如何使用无服务器框架在 AWS 上构建一个. NET 核心 Lambda。

## 无服务器计算的优势

无服务器技术是 *FaaS(功能即服务)*技术家族的一部分。随着云系统的采用，这种技术变得越来越流行。如今，无服务器实现被提升为使用涉及私有和公共云提供商的解决方案的首选技术。

此外，典型的软件服务和系统通过在内存中保存大量数据以及在复杂的数据源中写入批量数据来执行操作。

无服务器，一般来说，FaaS 技术通过尽可能快地为大量小请求和事件提供服务，来保持我们的系统快速反应。无服务器组件通常与运行它们的云提供商提供的事件强耦合:通知、队列调度的事件或来自 API 网关的传入请求被视为无服务器组件中包含的小计算单元的触发器。因此，这就是为什么**云提供商定价系统基于请求数量**，而不是计算时间的主要原因。

此外，无服务器组件通常在执行时间上有一些限制。就像每种技术一样，无服务器并不适用于每种解决方案和系统。的确，它简化了软件工程师的生活。事实上，λ部署周期通常是 T2 快 T3，作为开发人员，我们可以通过少量的工作快速将新特性投入生产。此外，使用无服务器技术构建组件意味着**开发者不需要关心扩展问题或失败**，因为云提供商关心这些问题。

最后，我们还应该考虑无服务器功能是无状态的。因此，基于这种技术构建的每个系统更加**模块化** r 和**松耦合**。

## 无服务器痛点

这种力量和敏捷不是免费的。首先，无服务器功能是在云上执行的，它们通常由与云提供商强耦合的事件触发，因此，调试它们并不容易。这是保持它们的范围尽可能小的合理理由，并且总是将您的功能的核心逻辑与外部组件和事件分开。此外，用单元测试和集成测试覆盖无服务器代码非常重要。

其次，就像微服务架构一样，它有很多服务，但关注点很小，无服务器组件也很难监控，某些问题很难检测到。最后，很难全面了解体系结构以及不同无服务器组件之间的依赖关系。出于这个原因，云提供商和第三方公司都在一体化工具上投入大量资金，这些工具既提供监控功能，又提供系统分析功能。

## 实验使用无服务器计算

如今，根据传入的业务请求快速发展服务和架构比以往任何时候都更加重要。数据驱动的实验是这个过程的一部分。此外，在发布一个新特性之前，我们应该实现一个 MVP，并在有限的客户群中进行测试。如果实验结果是肯定的，那么就值得在 MVP 上投资，以便将它转化为我们产品的一个特征。

嗯，无服务器计算提供了一种快速发展我们的架构的方式，而不需要关心基础设施。无服务器轻型开销提供了一种实现一次性 MVP 的方法，用于试验新的特性和功能。此外，它们可以很容易地插入和拔出。因此，

## 实现 AWS Lambda 使用。网络核心

下一节将介绍一些 AWS Lambdas 的简单实现。网芯。该示例涉及三项关键技术:

*   *AWS* 是托管我们无服务器功能的云提供商；
*   *无服务器框架*，这是让我们的 lambdas 进入 AWS 的一个非常有用的工具。作为一个通用框架，它兼容所有主要的云提供商；
*   *。NET Core* 是微软支持的开源跨平台框架；

我们要讨论的例子也存在于无服务器 GitHub repository @以下 URL:[server less/examples/](https://github.com/serverless/examples/tree/master/aws-dotnet-rest-api-with-dynamodb)[AWS](https://github.com/serverless/examples/tree/master/aws-dotnet-rest-api-with-dynamodb)[-dot net-rest-API-with-dynamo db](https://github.com/serverless/examples/tree/master/aws-dotnet-rest-api-with-dynamodb)。该示例是无服务器框架提供的一些模板项目的一部分。

AWS Lambda 项目遵循以下功能模式:

<figure>

[![](img/7c86bbed48bce155fe825d454938106f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8-Bef724--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://samueleresca.net/wp-content/uploads/2018/11/Screenshot-2018-11-25-at-00.15.33.png)

<figcaption></figcaption>

</figure>

总之，该功能实现了一些数据读/写操作。一个 HTTP 请求通过 *API 网关*来自客户端，lambda 项目定义了三个函数:`GetItem`、`InsertItem`和`UpdateItem`。它们中的每一个都对一个 *DynamoDB* 表执行操作。

### 项目结构

我们将要实施的解决方案具有以下项目结构:

*   *src/DotNetServerless。应用程序*项目包含由无服务器逻辑执行的核心逻辑；
*   *src/DotNetServerless。Lambda* 项目包含无服务器功能的入口点和所有与 AWS 紧密耦合的组件；
*   *测试/DotNetServerless。Tests* 项目包含无服务器特性的单元测试和集成测试；

### 域项目

让我们从分析应用层开始。该项目的核心实体是代表 dynamo 数据库表中存储实体的`Item`类: