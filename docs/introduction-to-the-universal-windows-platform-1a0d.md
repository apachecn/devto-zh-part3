# 通用 Windows 平台简介

> 原文：<https://dev.to/claudiobernasconi/introduction-to-the-universal-windows-platform-1a0d>

什么是 Windows 通用平台(UWP)？作为一名. NET 开发人员，你可能听说过 UWP，但是你不知道它是什么以及它是如何工作的？太好了，我也是！我们一起来了解一下吧。

## 什么是 Windows 通用平台(UWP)？

在关于[通用 Windows 平台(UWP)](https://docs.microsoft.com/en-us/windows/uwp/index) 的官方文档中，微软将 UWP 定义为一个让你为任何 Windows 设备构建应用的平台。他们提到了 PC、手机、Xbox One、HoloLens，“以及更多。”

UWP 取代了随着 Windows 8 的发布而引入的 [Windows 运行时(WinRT)](https://en.wikipedia.org/wiki/Windows_Runtime) 。WinRT 是作为 Win32 APIs 的现代替代品而设计的。

## 发布到微软商店

创建 UWP 应用的一个主要好处是可以访问微软商店。你可能会认为这家商店是一个侏儒，苹果应用商店或谷歌 Play 商店(Android)要大得多，但我猜你错了，因为我在研究这些数字之前。

据 ZDNet 年 10 月的一篇文章称，这些产品背后的公司发布了官方数据，有 20 亿台活跃的 Android 设备(谷歌)，13 亿台活跃的 iOS 设备(苹果)和 7 亿台运行 Windows 10 的 PC(微软)。

是的，访问微软商店的人数只有访问苹果应用商店人数的一半左右。但是如果你考虑一下数字，我认为 7 亿仍然是一个相当大的数字，最重要的是这个数字正在迅速增长。

如果你创建了一个 UWP 应用程序，你就可以利用微软商店向全世界数百万人出售你的产品。Windows 桌面应用程序的分发从未像现在这样简单。

## UWP 应用在微软商店的收入份额

苹果对其应用程序采用了著名的 70:30 收入分成，这意味着应用程序的开发者获得 70%的收入，而平台则获得每笔销售额的 30%。谷歌在谷歌 Play 商店也有同样的收入分成。

微软还从所有预先付费的应用程序和游戏中抽取 30%的费用，而对于非游戏的应用程序，微软只抽取 15%的费用，这些应用程序是以订阅为基础向客户收费的。更多细节请看[应用开发者协议](https://docs.microsoft.com/en-us/legal/windows/agreements/app-developer-agreement)(第 6 章，字母 b)。

这意味着，如果你销售基于订阅的应用程序(不包括游戏)，你可以获得 85%的收入份额，而微软只能获得 15%，这只是苹果或谷歌的一半。

[微软最近宣布](https://developer.microsoft.com/en-us/windows/campaigns/microsoft-store-new-revenue-share)他们将把一部分应用的收入份额进一步提高到 95%。如果你把它与苹果和谷歌的 70%相比，这是一个巨大的差异！然而，这是一个公告，现在没有更多的细节。

## 其他设备呢？

我们已经提到了运行 Windows 10 的 7 亿台电脑，这是你的 UWP 应用的潜在目标。让我们来看看赋予该平台“通用”名称的其他设备。

微软提到，除了个人电脑，手机、Xbox One 和 HoloLens 也是 UWP 应用的目标。实话实说吧。如今，大多数手机运行的是安卓系统(74%)或 iOS 系统(23%)。微软的市场份额为 0.3%。我要声明，目前智能手机上没有 UWP 应用程序的市场。

Xbox One 是一个有趣的目标，因为大约售出了 4000 万台设备。这些数字因来源而异。微软不公布官方数字。

除了 7 亿台个人电脑外，Xbox 的销量约为 4000 万台。太好了。但是…

## 你会为 Windows 桌面和 Xbox One 编写哪种类型的应用？

我脑子里想的是，我会写哪个应用程序让人们想安装在他们的台式电脑和 Xbox One 上？也许是游戏？我想不出我会同时考虑这两种设备来编写应用程序。

如果你有一台 Xbox One，或者如果你知道有人在他们的 Xbox One 和台式电脑上运行应用程序，请在这篇文章下面写下评论。

还有物联网设备，Surface Hub，HoloLens。我不知道这些设备，因此我不做任何关于这些设备是否是建立 UWP 应用程序的论据的声明。

## UWP 和 Xamarin 有什么区别？

Xamarin 是微软用于移动应用程序开发的跨平台技术。使用 Xamarin 编写的应用程序可以针对 Android、iOS(和 Windows Phone)进行编译。你也可以瞄准 UWP。

要更详细地了解 Xamarin 和通用 Windows 平台之间的[差异，请查看 Tyler Whitney 的文章。](https://msdn.microsoft.com/en-us/magazine/mt790185.aspx)

UWP 不是跨平台的，但是(正如它的名字所表明的)对任何 Windows 设备都是通用的。这意味着 UWP 应用程序不能在除 Windows 之外的其他平台上运行，但它们可以在本文介绍中提到的所有不同的 Windows 设备上运行。

## UWP 和 WPF 有什么不同？

WPF 于 2006 年作为。NET Framework 3.0，它运行在。NET Framework 是从 Windows XP 到 Windows 10 的几个 Windows 版本的一部分。WPF 使用 XAML 作为 Silverlight 引入的用户界面设计语言。

它的目的是成为 Windows 桌面应用程序的 WinForms 或 Win32 开发的现代替代方案。如果你想了解更多关于 WPF 的现状，请阅读我最近写的一篇关于 WPF 的文章。

UWP 是在 2015 年随着 Windows 10 推出的，因此 UWP 应用程序不能在旧版本的 Windows 上运行。UWP 在设计时就考虑到了触摸屏，并使用 XAML 作为其用户界面设计语言。

WPF 应用程序和 UWP 应用程序之间最显著的区别是，WPF 应用程序是使用安装程序安装在本地计算机上的，没有任何安全检查。他们还可以写入 Windows 注册表。

UWP 应用程序必须通过微软的认证过程，可以使用微软商店安装，并且在安装过程中对应用程序的功能有限制。

如果你想深入了解 [UWP 对 WPF，请查看这篇文章](https://github.com/jbe2277/waf/wiki/UWP-vs.-WPF)。

## 如何开发 UWP 应用程序？

我相信作为一名开发者，你想知道如何开发一个 UWP 应用程序。可以用 C#、C++、Visual Basic 和 JavaScript 编写它们。用户界面技术包括 XAML、HTML 和 DirectX。

微软希望通过支持开发人员已经知道并喜爱的语言和工具(Visual Studio ),使每个开发人员构建 UWP 变得尽可能简单。

## UWP、安卓、iOS 的 app 有什么区别？

微软写了一篇大文章，非常详细地解释了主要应用平台之间的[差异。](https://docs.microsoft.com/en-us/windows/uwp/porting/android-ios-uwp-map)

它涵盖了用户界面设计、控件、数据绑定、UI 自动化、视觉样式、导航、应用程序结构、数据存储、web 套接字、HTTP 客户端、通用工具和应用程序生命周期管理。

它还提供有关性能、盈利、通知管理、媒体捕捉和渲染、传感器、位置和地图、应用程序到应用程序设计和软件设计的信息。

如果你熟悉 Android 或 iOS 开发，并想开始使用 UWP 应用程序，我建议查看这个资源。我以前开发过 Android 应用程序，对我来说，这是一个非常有用的资源。

## 如何向微软商店提交 UWP 应用？

如果您决定编写一款 UWP 应用程序，并且想要将该应用程序上传到微软商店，您需要访问[微软合作伙伴中心](https://partner.microsoft.com/dashboard)。

你必须先注册成为应用开发者，然后才能上传你的第一个应用。这种注册只需要一次。您未来的应用程序可以更简单地发布。

## 想写一个 UWP app 从哪里开始？

微软提供了关于如何为各种编程语言编写第一个 UWP 应用程序的教程，包括:

*   C#使用 XAML
*   使用 XAML 的 C++
*   JavaScript 和 HTML

这是一个开始 UWP 开发和[编写你的第一个 UWP 应用](https://docs.microsoft.com/en-us/windows/uwp/get-started/your-first-app)的好地方。

## Windows 模板工作室

Windows Template Studio 是一个 Visual Studio 扩展，旨在使用基于向导的体验来加速新 UWP 应用的创建。

在我看来，这个扩展非常有用，因为它为您生成了样板代码。它可以加速开发过程，尽管您仍然需要理解该工具为您做了什么。我建议从基础开始，当你已经熟悉基础的时候使用 Windows Template Studio。

它支持不同的导航风格，不同的应用程序设计模式，如代码隐藏，基本 MVVM，MVVMLight，Caliburn。微型和棱镜。它还允许您创建不同的页面，如 web 视图或主/详细页面，以及许多小的生产力助手。你可以仔细看看它的 [GitHub 页面](https://github.com/microsoft/windowsTemplateStudio)。

## Windows 社区工具包

[Windows 社区工具包(WCT)](https://github.com/windows-toolkit/WindowsCommunityToolkit) 是助手功能、自定义控件和应用服务的集合。它为平台本身提供的 UWP 基础提供了许多不同的补充。

WCT 是美国的一部分。并且是开源的。如果你用 C#开发你的 UWP 应用程序，这可能是一个有用的库。

## UWP App 提示

[UWP 应用提示](http://www.uwpapp.tips/)是由 Alvin Ashcraft 用 C#写的博客。网络社区。Alvin 是微软 MVP，已经写了 35 篇以上关于通用 Windows 平台应用程序开发的博文。

这是一个很好的入门资源，也是阅读更多高级主题和其他社区资源的好资源。

## 个人见解

对于那些更喜欢在微软堆栈上编写应用程序或希望向 Windows 桌面系统提供软件的应用程序开发人员来说，UWP 是一个极具吸引力的新机会。

与传统软件安装相比，使用微软商店的部署为用户提供了更高的安全性。作为一名开发者，你可以利用微软商店来提高你的应用程序的曝光率。如果你的应用很优秀，它会像其他应用商店一样被找到并安装。

你需要记住，这项服务的成本高达你向用户收取的价格的 30%。作为交换，你可以使用微软商店方便地处理支付，而在传统世界里，你必须自己建立一个支付流程。

在我看来，这取决于应用程序的用例。是面向最终用户还是业务用户？是内部 app 还是你们公司的客户用的 app？根据不同的情况，UWP 或 WPF(甚至是 Xamarin)可能是正确的选择。好消息是我们作为开发者有一个选择。

我质疑通用 Windows 平台的通用部分。我看到了部署到台式机以外的其他设备上的选项，但我认为大多数应用程序只能在运行 Windows 10 的计算机上运行。但我也不认为这有什么不好。也许微软再次涉足智能手机市场？

你如何看待 Windows 桌面开发的现状，尤其是 UWP？请在评论中告诉我。我很想收到你的来信。

*本文原载于 2019 年 2 月 28 日 [claudiobernasconi.ch](https://www.claudiobernasconi.ch/2019/02/28/introduction-to-the-universal-windows-platform-uwp/) 。*