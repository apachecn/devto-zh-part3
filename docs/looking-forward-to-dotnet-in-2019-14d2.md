# 期待 2019 年的 dotnet

> 原文：<https://dev.to/csmacnz/looking-forward-to-dotnet-in-2019-14d2>

[![Looking forward to dotnet in 2019](img/e6cca10538499834f1c3013edd4d21db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c6lVPEFW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1537470284582-8da71000386c%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjE2ODI3fQ)

这是新的一年，我们已经有了一批值得期待的 dotnet 版本，这要感谢微软这些天在 dotnet 上使用的开放开发过程。

我期待的三个大版本是 C# 8 和。Net Core 3.0 和 NetStandard2.1。(更不用说还有. Net Framework 4.8。但我已经向前看了。)

## C# 8

感觉我们谈论 C# 8 已经有一段时间了，7.1、7.2 和 7.3 版本正在向它靠拢。(那是 2 年前他们在讨论 C# 7.0 的新特性的时候！).我们已经在 Build、NDC 和网上演示了即将到来的事情，去年年底[发布了](https://blogs.msdn.microsoft.com/dotnet/2018/12/05/take-c-8-0-for-a-spin/)预览版。我们应该期待看到 VS 2019 在本季度发布(希望如此)，届时可以更好地了解 C# 8 的最终版本。

我们所知道的是将会有一系列很酷的功能出现:

*   可为空的引用类型
*   异步流
*   范围和指数
*   接口成员的默认实现
*   递归模式
*   切换表达式
*   目标类型的新表达式

关于这些是什么以及它们看起来如何的更详细的注释和例子发表在？网博文章[构建 C# 8.0](https://blogs.msdn.microsoft.com/dotnet/2018/11/12/building-c-8-0/) 。

我非常期待可空的引用类型(我还有一些库需要更新以支持它)，切换表达式和递归模式，并找到其他特性开始为我所用的地方。

## [T1。网络核心 3.0](#net-core-30)

我们再次发布了一个合适的主版本。这个和那个一样重要。Net Core 2.0 里程碑是，但希望，跳跃更稳定。这是新的 dotnet CLI 工具更新、MSBuild 更新、新的 3.0 运行时以及对 Visual Studio 中所有内容的支持的总称。

大标题是 Windows 桌面即将到来。网芯。是的，这是特定于操作系统的，不是跨平台的，但这是将那些 GUI 应用程序转移到 dotnet CLI 和。Net 核心运行时。作为其中的一部分，他们甚至开源了所有的 GUI 框架 WPF、WinForms 和 WinUI。

搬到时需要注意的事项。网络核心 3.0:

*   桌面应用支持
*   默认情况下，应用程序现在有可执行文件
*   dotnet 构建现在复制依赖项
*   本地网络工具
*   ARM64 和更新的平台支持
*   许多语言和运行时改进
    *   快速内置 JSON 阅读器
    *   C# 8 语言功能支持
    *   系统。缓冲区.序列阅读器
    *   物联网改进
    *   linux 上的 TLS 改进
    *   加密技术的改进
    *   使用优化的跨度、内存
    *   接口成员的默认实现
    *   默认情况下打开分层编译
    *   使用 MetadataLoadContext 读取程序集元数据

要查看更多详细的变化，尤其是语言和运行时的改进，您可以在 12 月发布的公告中亲自阅读更多内容[。](https://blogs.msdn.microsoft.com/dotnet/2018/12/04/announcing-net-core-3-preview-1-and-open-sourcing-windows-desktop-frameworks/)

## .NetStandard2.1

的发布。Net Core 2.1 和 2.2 过去了，不需要更新兼容性的基线。在错误的开始之后，NetStandard2.0 目标已经很好地作为一个基线公分母。

去年年底，微软宣布。NET 标准 2.1 ，这是一个新的必需标准，将首先在。dotnet 3.0 和。Net Framework 4.8 是允许使用一些需要运行时支持的新语言功能的基础，例如异步流、索引器和范围。

## 结论

如前所述，你今天就可以在 Visual Studio 2019 的[预览中开始玩这些东西了。](https://blogs.msdn.microsoft.com/dotnet/2018/12/05/take-c-8-0-for-a-spin/)

在接下来的一年里有很多新东西要学，我等不及发布日了，这样我就可以更新我所有的东西了。第一站将是可空的引用类型。

你最期待用的是什么？