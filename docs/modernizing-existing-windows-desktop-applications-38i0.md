# 更新现有的 Windows 桌面应用程序

> 原文：<https://dev.to/claudiobernasconi/modernizing-existing-windows-desktop-applications-38i0>

在我上周的文章中，我写了一篇关于。NET 桌面应用。我的文章一发表，微软就宣布了针对 Windows 桌面开发者的重大消息。

随着最近[微软宣布。NET 5](https://devblogs.microsoft.com/dotnet/introducing-net-5/) 将于 2020 年秋季发布，微软将统一所有支持平台的开发者体验。

向前看。NET 5 将支持面向 Windows、Linux、macOS、iOS、Android、WebAssembly 等所有相同的操作系统。NET 5 APIs。

[![.NET – A unified platform (Image: Microsoft)](img/f06e9a1f45eb0bf5c7a02e62a7222e38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ie_Drfqg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.claudiobernasconi.ch/wp-content/uploads/2019/05/dotnet5_platform.png)

这张从公告中截取的图像显示了统一的。NET 平台，以桌面开发、web 开发、云、移动、游戏、物联网、人工智能为特色。

在本文中，我们将重点讨论这对现有的 Windows 桌面应用程序有什么影响，以及作为开发人员，我们可以做些什么来为迁移到 Windows 桌面做好准备。NET 5 以及为什么我们应该首先考虑这样做。

# 关于什么。网芯 3.0？

微软已经宣布发布。2019 年 9 月网芯 3.0 。。NET Core 3.0 允许我们通过添加 WinForms 和 WPF 支持来迁移现有的桌面应用程序。网芯。

> ".NET Framework 4.8 将是。NET 框架。项目管理总监斯科特·亨特。净

另一方面，他也提到:

> “如果已经存在的话。NET Framework 应用程序，没有必要将这些应用程序移动到。网芯。我们将继续提供服务和支持。NET 框架，其中包括错误——可靠性——和安全修补程序。”项目管理总监斯科特·亨特。净

因此，我们不需要迁移我们的应用程序，但如果我们这样做了，我们可以利用随之而来的所有好处。NET 核心，如并行部署、新功能、性能改进和跨平台支持。

在我看来，迁移到。NET Core 3.0 是准备新的应用程序的第一步。NET 5 世界。对于任何将投入生产几年以上并处于积极开发中的应用程序，我建议进行迁移，以获得全新的好处。NET 核心实现。

微软还让我们在迁移到之后进一步开发 WinForms 应用程序。网芯 3.0。由于 WinForms 支持，我们可以开发我们的 WinForms 应用程序，只要我们不需要像 Microsoft Store 这样的新平台功能，适当的多显示器支持或新 Windows 10 APIs 的其他功能。

在宣布之前。NET Core 3.0，唯一的选择是将 WinForms 应用程序迁移到 UWP 应用程序，如果你想运行它们的话。NET 核心并利用新的 API。的发布。NET Core 3.0 让我们可以选择继续使用 WinForms 作为 UI 框架。

对于不再处于活动开发状态，但更可能处于维护模式的应用程序，不需要将它们迁移到较新版本的. NET。安全修补程序仍将对(完整版)可用。NET 框架。

# 有什么好处。NET 5？

[公告](https://devblogs.microsoft.com/dotnet/introducing-net-5/)陈述了中的三项关键改进。网络 5:

*   适用于所有平台的单一运行时和框架
*   取…之精华。网芯，。NET 框架、Xamarin 和 Mono
*   在单一开源代码库中提供一切

**简而言之:**所有项目都应该基于相同的项目文件，拥有相同的可用 API，并受益于相同的性能改进。

如果你想深入了解。NET 5，一定要查看一下[微软官方公告](https://devblogs.microsoft.com/dotnet/introducing-net-5/)。

在我看来，**。NET 5 拉近了开发者的距离。尽管 Xamarin 或 UWP 的开发人员以及 ASP.NET 的核心开发人员喜欢新的工具、框架版本和 API，但 WinForms 和 WPF 的桌面开发人员经常感到被排斥在外。。NET 5 将改变这一点。**

今天，当处理不同的项目类型时，您需要学习稍微不同的项目文件结构、不同的 API 和不同的工具。

随着所有目标和平台的统一，每个开发人员都将拥有相同的工具和相同的 API。这是一个巨大的进步。

。NET 5 缩小了这个差距，并允许在这些目标和项目类型之间进行更简单的知识转移。

同样，对我来说，将所有仍在开发中的现有应用程序迁移到。NET 5 在 2020 年发布的时候。

# XAML 岛

XAML 岛允许我们在 Win32、WinForms 和 WPF 应用程序中使用 UWP 控件。它还允许我们从现有的应用程序中访问与 UI 相关的 Windows 10 APIs。如果没有 XAML 岛，这些 API 只能用于 UWP 应用程序。

XAML 岛提供了一种现代化应用程序的方式，这些应用程序不能作为一个整体重写，但可以受益于对基于 Windows 10 APIs 的控件的访问。

Thomas Claudius Huber 写了一篇关于[如何在现有的 WPF 应用程序中使用 UWP 控件的深度博文](https://www.thomasclaudiushuber.com/2019/04/23/net-core-3-use-uwp-controls-in-wpf-with-xaml-islands/)。

如果您有较新的应用程序和较旧的应用程序，将较旧应用程序中的一些控件迁移到 UWP 控件而不是一次性重写整个应用程序是一个可行的用例。

值得一提的是，XAML 岛 1.0 是作为 **Windows 10 版本 1903** 的一部分发布的。之前的任何版本都不支持 XAML 岛。

# 结论

桌面开发者经常感觉被所有闪亮的新事物所遗忘。NET Core 不得不提供。只有几个新功能。基于. NET 框架的应用程序。

随着……的宣布。NET Core 3.0 及其对 WinForms 和 WPF 应用程序的支持，微软展示了现有 Windows 桌面应用程序的迁移路径，它允许这些应用程序从所有。净核心改进。

借助 XAML 岛，微软提供了另一种方式来逐步实现现有系统的现代化。NET 桌面应用程序，允许它们使用 UWP 控件。

在我看来，有一个光明的未来。NET 和桌面开发。NET Core 3.0 我们有机会从。网芯。而且随着。NET 5 Windows 桌面开发人员终于与所有其他人站在了同一战线上。NET(核心)开发者。

*本文原载于 2019 年 5 月 15 日 [claudiobernasconi.ch](https://www.claudiobernasconi.ch/2019/05/15/modernizing-existing-windows-desktop-applications/) 。*