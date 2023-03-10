# 。NET Core 在 IIS 上运行后台工作程序

> 原文：<https://dev.to/ankitvijay/net-core--running-background-worker-on-iis-54ho>

我正在开发的新解决方案的关键部分之一是 [RabbitMQ](https://www.rabbitmq.com/) 。对于那些从未听说过 RabbitMQ 的人来说，它是使用最广泛的开源消息代理之一。由于我们的解决方案是内部托管的，RabbitMQ 是最适合我们的解决方案之一。我们的整个解决方案架构建立在。NET Core 2.1，最近迁移到。网芯 2.2。要运行 RabbitMQ，我们有 3 个选项:

*   以传统方式运行 RabbitMQ。NET Framework“Windows 服务”
*   将 RabbitMQ 作为. NET 核心“Windows 服务”运行
*   将 RabbitMQ 作为. NET 核心应用程序宿主在 IIS 上

创建一个基于. NET 框架的 Windows 服务是一件众所周知的事情，因为我们已经做了上百次了。然而，由于我们的整个解决方案是基于。NET Core，这是一种倒退。因此，我们开始探索选项 2，即托管基于. NET 核心的 Windows 服务。我们的解决方案很大程度上受到了史蒂夫·戈丹关于“[将. NET 核心通用主机应用作为 Windows 服务运行](https://www.stevejgordon.co.uk/running-net-core-generic-host-applications-as-a-windows-service)”的帖子的启发

根据他的帖子，我们将解决方案配置为在开发期间作为控制台应用程序运行，然后在部署时作为 windows 服务运行。但是，我们没有发现在上部署 windows 服务。NET Core 和我们最初想的一样简单。我们必须修改项目文件来添加`RunTimeIdentifier`或 [RID](https://docs.microsoft.com/en-us/dotnet/core/rid-catalog) 。RID 是特定于操作系统和架构的。它是基于操作系统、版本和体系结构的不同字符串。我们不希望我们的解决方案依赖于特定的操作系统。在我们的解决方案中，我们没有特定的操作系统依赖性。除此之外，Windows 服务也有自己的复杂性。与托管服务相比，它很难调试和监控。

在这里，我们研究了第三个选项:在 IIS 上托管我们的后台工作人员。在 IIS 上托管后台工作程序意味着我们能够像部署 web 应用程序一样部署我们的后台工作程序。我们可以使用默认的“Web 主机”，而不是使用“通用主机”。自定义代码较少。更容易调试和监控。除此之外，我们还能够利用[。网络核心健康检查](https://al-hardy.blog/2017/04/17/asp-net-core-health-checking/)。

为了在 IIS 上运行后台工作器，我们必须调整 IIS 设置，以保持它始终启动和运行。下面是我们在 Octopus 上运行来更新 IIS 设置的 PowerShell 脚本。