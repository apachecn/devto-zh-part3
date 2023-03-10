# 自动化。NET 核心服务和面向方面的代码

> 原文：<https://dev.to/dealeron/automating-net-core-services-with-postsharp-and-aspect-oriented-code-2j8i>

如果你在写软件定位。那么你很有可能在工作中使用面向对象的原则。在这个过程中，你可能会碰到你发现自己经常重复的设计模式。其中一些模式是从抽象需求开始的，比如“我想记录我的代码在做什么”。在实践中，这很容易解决，但是随着您的解决方案范围的扩大，您可能会发现自己要花费大量的时间来决定在哪里以及如何记录程序的状态。最终，您可能想要更改日志记录模式，但是这将需要对源进行大量的更改。

虽然许多人已经用面向对象的方式解决了这个问题，但是这些横切关注点最终会给任何开发团队增加大量的时间。当您需要进行重大更改时，它们还会在以后提出架构挑战。当您想要添加新的日志接收器、监视器或内部日志异常处理程序时，您将不得不回顾所有的样板代码。这个问题再次出现在许多常见的问题上，例如安全性、缓存、线程、速率限制，甚至警告 GUI 某些可视对象已经更新。

面向方面的设计通过在一个地方处理问题，然后在代码中任何需要的地方应用该解决方案，来关注这些问题。我在这里试图展示的是一种在投入时间完全模拟出来之后，有目的地隐藏那些顾虑的方法。通过花一些额外的时间来考虑我们的日志或线程方法，我们可以编写更像是可以分配给代码区域的质量的方面。一旦你写了一个[Log]属性，你就可以决定一个类或者一个方法将被记录，只要用这个属性来标记它。稍后，编译器通过返回并在声明这些方面的地方添加缺少的代码来完成剩下的工作。

在我进入主示例之前，先做一个简短的说明，微软发布了[。NET Core 3 Preview 1](https://blogs.msdn.microsoft.com/dotnet/2018/12/04/announcing-net-core-3-preview-1-and-open-sourcing-windows-desktop-frameworks/) 上个月发布，它还支持原生的 Windows 桌面命名空间。而本文讨论的是。NET Core 2.1，你可以在任何时候在 netcoreapp3.0 下作为项目目标重新编译它，它将支持更广泛的类型表面，包括 UWP 库，而无需任何额外的配置。

### 备注

这篇文章试图涵盖某些困难的主题，这些主题在设计上因企业而异。我将引用的代码写得尽可能简短，而不是快速或高效。这是为了展示每个特性并创建一个可用的演示。

这里的目标是探索的跨平台能力。NET 泛型主机，结合模式识别编译器的强大功能。通过围绕通用主机包装平台和运行时问题，无缝链接。适用于任何平台的. NET 库。这并不意味着你可以在 Linux 上运行 UWP 库，但如果你想在它检测到 Windows 10 是操作系统时运行该库，你不必重写任何代码。

PostSharp 是一个面向 Visual Studio 的模式感知编译器扩展，将在整篇文章中重点介绍。如果您没有许可证，并且想要遵循代码，那么仍然有一个解决方案。这个框架的免费 [Essentials](https://www.postsharp.net/essentials) 版本覆盖了多达 10 个类，这足以实现本文中的全部程序。你还需要[下载 Visual Studio 2017 的扩展](https://marketplace.visualstudio.com/items?itemName=PostSharpTechnologies.PostSharp)。

### 托管服务

我花了几个月的时间来熟悉新的[。NET Core 通用主机](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-2.2)，这是任何在 ASP.NET Core web applications 工作过的人都熟悉的。主机也可以在控制台应用程序中使用，以处理内置的应用程序配置、依赖注入和日志记录。让我们花一点时间来看看服务托管如何适应这种设计。

目标是创建一个小型应用程序，它可以:

*   基于 OSVersion 托管通用服务和加载原生类。
*   自动将日志记录包含在整个解决方案中。
*   为多核计算机自动线程化服务。
*   验证架构，以便 bug 以确定的方式抛出异常。

现在，因为我已经在 ETL 解决方案上花了很多时间，我倾向于绘制出我的程序流，并从高层次上看我的目标是什么。下面我只画了一些基本的形状来说明它的结构，并试着看看边界在哪里。这些边界很重要，因为它们代表了关注点交叉并切入单个对象的关注点的区域。当在一个项目中使用 AOP 时，我总是发现清楚地定义那些边界是有帮助的，因为目标是隐藏它们是如何工作的。你必须原谅我的绘画美学:

[![](img/815d3eaecf922154e2ae2f2684812097.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nZu7PPa8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVBWR2Bo6CGvt6AYYHS6CaA.png) 

<figcaption>不可变的(冻结的)运行时和平台区域，这些区域托管封装在一个演员线程模型中的服务。</figcaption>

**程序:**在这个例子中是一个带有`Main()`的控制台应用程序。

运行时:这将是所有 PostSharp 包装器的顶层，也是程序其余部分的构造器。这应该会启动服务主机。

平台:它包含加载本地类所需的任何方法，并在需要实现任何服务间通信的情况下向主机提供线程安全的引用。

**冻结:**前面的两层都将被冻结，这提供了一些与不可变对象相同的保证。这将在后面讨论。

**托管:**托管。NET 泛型宿主生成器。

**服务:**服务将被包装在一个 Actor 模型中，确保它们可以在多核计算机上运行。

### 日志输出

在整个练习中，您将看到几个日志输出示例，这些示例描述了特定模板中程序的状态。我在这里标记了其中的一些，作为这些值代表什么的例子。

[![](img/7f37af08288c900e7991bf30271ee5cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dbRjChSr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/68iweed02si31faunxq5.png) 

<figcaption>控制台日志输出由 PostSharp</figcaption>

自动生成

### 程序

首先，让我们从新的开始。NET Core 2.1 控制台应用程序。