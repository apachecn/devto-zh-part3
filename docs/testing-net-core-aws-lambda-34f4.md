# 测试。网络核心 AWS Lambda

> 原文：<https://dev.to/samueleresca/testing-net-core-aws-lambda-34f4>

*原贴于[https://samueleresca.net](https://samueleresca.net)T3】*

下面的帖子展示了一些关于测试的技巧。NET Core AWS Lambda，更具体的说，它侧重于测试。NET Core AWS Lambda 使用 [LambdaTestTool](https://github.com/aws/aws-lambda-dotnet/tree/master/Tools/LambdaTestTool) 。我已经谈到了无服务器。NET Core 和 AWS Lambda:[快速增长的无服务器和。网芯](http://samueleresca.net/2018/12/fast-growing-architectures-with-serverless-and-net-core/)。这篇文章将更多的关注测试方面。

## 无服务器计算中的测试问题

正如在[上一篇关于无服务器](http://samueleresca.net/2018/12/fast-growing-architectures-with-serverless-and-net-core/)的文章中所说，测试无服务器应用是很难的。特别是当它们的复杂性增加时，在云上运行它们之前很难发现问题。此外，从开发的角度来看，如果每次我们都需要重新部署我们的服务来验证结果，就很难处理问题。

## 现有的测试工具

一些现有的测试工具帮助我们处理无服务器系统。说到 AWS，像[https://github.com/localstack/localstack](https://github.com/localstack/localstack)这样的东西提供了一种在你的机器上通过安装或者运行 docker 镜像来模拟 AWS 栈的方法。这些框架的要点是它们消耗大量的资源，并且它们并不总是适合集成测试。

# 使用 LambdaTestTool 测试 lambda

[LambdaTestTool](https://github.com/aws/aws-lambda-dotnet/tree/master/Tools/LambdaTestTool) 是由 AWS 生产的实用程序。NET 团队，它提供了一种有用的、轻量级的 Lambda 测试方法。此外，它还提供了一种本地调试 lambda 的方法，方法是触发一些输入事件，并附加您喜欢的 IDE 或代码编辑器的调试器。

最近，AWS。NET 团队还发布了一个新版本的 [LambdaTestTool](https://github.com/aws/aws-lambda-dotnet/tree/master/Tools/LambdaTestTool) ，其 PR 如下:

[https://github.com/aws/aws-lambda-dotnet/pull/364](https://github.com/aws/aws-lambda-dotnet/pull/364)

这也使得能够对使用[无服务器框架](https://serverless.com)的所有 lambdas 进行调试。

## 安装

[LambdaTestTool](https://github.com/aws/aws-lambda-dotnet/tree/master/Tools/LambdaTestTool) 是作为 dotnet 工具套件的一部分实现的(我已经在下面的帖子中写过关于 dotnet 工具的内容:[使用。NET Core](http://samueleresca.net/2019/02/artless-http-server-using-net-core/) ，它是跨平台的，可以使用以下命令安装:

`dotnet tool install -g Amazon.Lambda.TestTool-2.1`

此外，可以使用以下说明将工具更新到最新版本:

`dotnet tool update -g Amazon.Lambda.TestTool-2.1`

上述代码在您的本地计算机上安装新的 dotnet 工具。因此，通过正确配置您的 IDE 或代码编辑器，可以使用它在本地运行 lambda。

## 调试λ

[LambdaTestTool](https://github.com/aws/aws-lambda-dotnet/tree/master/Tools/LambdaTestTool) 运行一个 ASP.NET 实例，该实例返回以下接口:

[![](img/ed7d9bccebce0735f82a064bd531abec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZwhS1VOp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://samueleresca.net/wp-content/uploads/2019/02/Screenshot-2019-02-19-at-22.22.40-960x761.png)

UI 提供了选择 lambda 的*配置文件*、项目中要执行的*函数*、凭证、 *AWS 区域*以及作为函数输入发送的消息的方法。

可以在多个编辑器上配置 [LambdaTestTool](https://github.com/aws/aws-lambda-dotnet/tree/master/Tools/LambdaTestTool) ，例如 Visual Studio、Visual Studio Code、Rider 和 Visual Studio for Mac。下一节将展示 Visual Studio 代码和 Rider 的配置。

### Visual studio 代码

通过在`launch.json`文件中添加以下部分，可以对 Visual Studio 代码采用相同的方法: