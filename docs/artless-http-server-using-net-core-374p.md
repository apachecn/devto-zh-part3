# 使用。网络核心

> 原文：<https://dev.to/samueleresca/artless-http-server-using-net-core-374p>

_ 最初发布于[https://samueleresca.net](https://samueleresca.net)

下面的文章展示了如何使用？网芯。我已经说过了。NET Core 和 ASP.NET Core 在以下岗位:

[。网芯 2.1 亮点:站在巨人的肩膀上](http://samueleresca.net/2018/03/net-core-2-1-highlights-standing-on-the-shoulders-of-giants/)

[在 ASP.NET 核心中使用 Actor 模型开发 APIs】](http://samueleresca.net/2018/07/developing-apis-using-actor-model-in-asp-net/)

[快速发展的无服务器架构。网络核心](http://samueleresca.net/2018/12/fast-growing-architectures-with-serverless-and-net-core/)

。NET Core 是微软和社区共同开发的全新框架。它遵循类似于 NPM 的方法，此外，可以安装由社区实施的第三方全球工具。实际上。NET Core Global Tools 是包含一个简单控制台应用程序的 NuGet 包，它们可以在本地机器上运行。下面的示例演示如何使用？NET 全局工具来运行一个简单的 HTTP 服务器。

ASP.NET 核心团队的成员 Nate McMaster 发布了一个简单的工具，它提供了一种在你的机器上运行 HTTP 服务器的简单方法。唯一的要求是。开发计算机上安装的. NET Core 2.1 或更高版本。

## 运行 HTTP 服务器

我们可以运行以下命令来安装该工具:

`dotnet tool install --global dotnet-serve`

之后，可以执行命令来运行 HTTP 服务器:

`dotnet serve -o -S`

`-o`参数触发默认浏览器，`-S`参数提供 HTTPS 连接。

此外，控制台应用程序还接受以下参数: