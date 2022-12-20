# Visual Studio 未来:随时随地的智能生产力和协作

> 原文：<https://dev.to/lostintangent/visual-studio-futures-intelligent-productivity-and-collaboration-from-anywhere-19gd>

*注意:我是 Visual Studio 团队的项目经理，这篇文章解释了我们试图应对新兴开发趋势的方法。它最初发布在 [Visual Studio 博客](https://aka.ms/vsfutures)上。*

由于对上市时间的日益重视，以及使用的技术比以往任何时候都更广泛(例如多语言应用、微服务)，开发人员今天正面临着巨大的复杂性。此外，团队正在[变得更加地理分散](https://stripe.com/blog/remote-hub)，这增加了对高效协作的需求，以便在敏捷环境中保持知识转移。

在过去的几年里，我们与成千上万的开发人员交谈过，并了解到解决这些基本挑战需要您的开发工具和过程中的一组新功能。因此，我们的重点是显著提高个人生产力，改善团队协作，并从根本上拥抱工作场所的灵活性。到目前为止，我们已经取得了很大的进步(并且还在迭代中！)关于前两项功能，感谢开发人员社区的反馈:

*   [Visual Studio IntelliCode](https://visualstudio.microsoft.com/services/intellicode/) 通过向 IDE 中注入智能，帮助提高**个人生产力**。基于对 API 如何在数千个开源 GitHub 仓库中使用的理解，它通过让自动完成等事情变得更智能来做到这一点。

*   [Visual Studio Live Share](https://aka.ms/vsls-why) 通过让开发人员能够从他们最喜欢的工具中舒适地一起编辑和调试，促进了**实时协作**。

*   我们还拥有[丰富的代码导航体验](https://code.visualstudio.com/blogs/2018/12/04/rich-navigation)来改进**异步协作**，并使开发人员能够通过多回购、基于云的语言服务深入审查 PRs。

今天，我们很高兴地分享三个新功能的初步外观，它们在[私人预览](https://aka.ms/vsfutures-signup)中，将使开发人员能够在任何地方、任何设备上工作，同时几乎消除了开始高效编码所需的设置量。

## 远程驱动的开发者工具

在我们发布 Visual Studio Live Share 之后，我们立即听到了对相邻场景的兴趣:个人远程开发。事实上，这是一年多来 GitHub 对 Visual Studio Live Share 的第一个功能要求。能够在远程机器上进行开发有很多好处，比如在与应用程序的部署目标不同的操作系统上工作，能够利用更高端的硬件，以及具有多机器可移植性。今天，许多开发人员希望进行远程开发，但不一定满足于使用 SSH + Vim 或 RDP/VNC 的体验。

上周，Visual Studio 代码团队发布了[远程开发扩展](https://code.visualstudio.com/blogs/2019/05/02/remote-development)(面向 Visual Studio 代码内部人员)，以便将您的本地工具连接到 WSL、Docker 容器或 SSH 环境，同时**保留 Visual Studio 代码中的全保真编辑体验**(例如扩展、主题、调试)。今天，我们很兴奋地分享对 **Visual Studio 远程开发**的初步了解，这将使 Visual Studio 用户获得同样的好处，并超越他们本地开发机器的限制。我们从 C#和 C++开始，并期待与社区合作来定义体验。注册[私人预览](https://aka.ms/vsfutures-signup)以获取未来更新。

[https://www.youtube.com/embed/UXnDDsgbgtc](https://www.youtube.com/embed/UXnDDsgbgtc)
*开发一个没有安装任何本地工具的 C++应用*

## 云托管开发环境

具有远程功能的工具解除了大量开发人员场景的障碍，但就其本身而言，它们仍然需要您手动管理机器。我们清楚地听到，开发人员花费了太多的时间来设置他们的开发环境，这可能会妨碍新团队成员的加入或使您能够在任务之间快速移动。为了简化这一点，我们宣布了一项功能的[私有预览](https://aka.ms/vsfutures-signup)，该功能可以按需提供完全托管的云托管开发环境。

当你需要做一个新项目，接一个新任务，或者审查一个 PR 时，你可以简单地启动一个基于云的环境，让服务来正确地配置它。这允许您花更多的时间编写代码，而很少甚至没有时间安装依赖项。然后，您可以使用 Visual Studio 或 Visual Studio 代码(或两者)连接到这些环境。)确保您可以在工作中使用正确的工具，并最大限度地提高您的个人生产力，无论您身在何处。

[https://www.youtube.com/embed/PhJofz9c-3o](https://www.youtube.com/embed/PhJofz9c-3o)
*直接在 Visual Studio 代码中创建新的云托管开发环境*

[https://www.youtube.com/embed/icZk9SW8Ih0](https://www.youtube.com/embed/icZk9SW8Ih0)
*连接到现有环境并远程调试*

## 基于浏览器的网络伴侣

开发人员对他们的编辑器非常固执己见，通常会花费无数的时间来定制它们。因此，您会希望[远程开发和协作](https://code.visualstudio.com/blogs/2019/05/02/remote-development)功能直接包含在您现有的工具中，而您的大部分时间都花在这些工具上。然而，在某些情况下，在浏览器中执行任务实际上会更方便，例如在旅途中进行快速编辑、查看 PR 或加入队友的实时共享会话。为了解决这个问题，我们很高兴分享对 **Visual Studio Online** 的初步了解，这是一个新的基于网络的配套编辑器，补充了 Visual Studio 家族，并确保您可以从任何设备有效地工作。

将来，您将能够导航到[https://online.visualstudio.com](https://online.visualstudio.com)并访问您的任何远程环境。因为 Visual Studio Online 是基于 Visual Studio 代码的，所以它会让人感觉很熟悉，并受益于您已经知道并喜欢的丰富的扩展生态系统，同时支持 Visual Studio 代码工作区以及 Visual Studio 的项目和解决方案。此外，它将支持 IntelliCode 和即时共享，这确保了它提供开发人员所需的丰富协作和生产力功能

[https://www.youtube.com/embed/XRQR9hUTkjI](https://www.youtube.com/embed/XRQR9hUTkjI)
*通过 Visual Studio Online 在浏览器中编辑 web 应用*

## 我们需要您的反馈！

我们很高兴分享这一进展，收集反馈，并了解我们如何继续提高基于团队的生产力。我们相信，这些经验的结合能够满足现代团队的需求，并确保您能够实现高水平的生产力、协作和工作场所灵活性。如果你有兴趣接触早期的东西并与我们的团队聊天，你可以在这里注册私人预览。我们期待着在不久的将来收到您的来信并分享更多的更新！