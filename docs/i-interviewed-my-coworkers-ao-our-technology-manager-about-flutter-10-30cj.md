# 我就 Flutter 1.0 采访了我的同事(我们的技术经理)

> 原文：<https://dev.to/ljosmyndun/i-interviewed-my-coworkers-ao-our-technology-manager-about-flutter-10-30cj>

上周二，谷歌推出了 Flutter 的第一个稳定版本，它的开源、跨平台 UI 工具包和 SDK。在口袋里，我们带着啤酒、披萨和巨大的热情观看了发布会的直播。在演示前后，我(Thomas，Flutter 菜鸟)会见了 Bram(我们的技术经理)和 Yves(我们的 Xamarin 负责人和狂热的 Flutter 专家)。

**如果我理解正确的话，Flutter 将确保我们不再需要单独开发一个 Android 和 iOS 应用程序，而只需构建一个？还是我太天真了？**

这当然是每个跨平台框架的目标。Xamarin 和 React Native 都希望实现相同的目标，但重要的是要记住，这些框架都不是灵丹妙药。你不能指望只选择一个框架就能创建所有可能的应用。我们仍然单独查看每个应用程序的需求，并决定哪种方法(本机或跨平台框架之一)最适合解决其特定需求。我们敢说，Flutter 为我们提供了另一种非常强大的手段，使用单一代码库创建漂亮的应用程序。

**谷歌为什么要推出这个工具包？我首先想到的是，有几个应用程序首先在 iOS 上发布，几个月后才在 Android 上发布——这是谷歌希望避免的情况？**

这可能确实是原因之一，但正如我们在主题演讲中看到的那样，实际上 Flutter 并不仅限于 Android 和 iOS。通过一些工作，开发人员将能够创建桌面应用程序，以及使用 Flutter 的 web 应用程序，甚至很可能在未来创建 Fuchsia 应用程序。我们认为，通过 Flutter，谷歌希望创造一种通用的方式来开发各种平台的应用程序，并提供一个易于使用、易于扩展的工具包。当然，Flutter 是谷歌的产品，所以这是另一种将开发者绑定到谷歌生态系统的方式，让他们一路使用更多的谷歌产品(这不一定是坏事)。

这对我们有什么改变吗？

我们认为可能会。自今年早些时候以来，Flutter 一直处于测试阶段，我们一直在对它进行实验，但其他开发者总是有些犹豫，因为“测试”标签被认为不适合生产。正如我们在过去所看到的，谷歌可能开始开发一个产品，但在正式版本发布之前停止开发。现在这个第一个版本已经正式发布，更多的人很可能会开始尝试。

随着 1.0 版本的发布，现在对客户端应用程序使用 Flutter 也是“安全的”。虽然好处是显而易见的，但基于测试框架创建长期产品仍然是有风险的。有了这个稳定的版本，我们可以积累更多的经验，让我们的客户从中获益。

正如你所提到的，Flutter 并不是第一个尝试跨平台的产品，有 React Native、Xamarin 和其他产品，为什么 Flutter 会与众不同呢？

为此，我们需要看看每个框架的不同方面。在 C#的原生 Xamarin 中，重点是共享移动应用程序的业务逻辑。Xamarin Forms 在此基础上增加了一层，因此 UI 也可以共享，但它仍然呈现本机元素。Xamarin 是一个拥有跨平台应用程序的好方法，你可以选择通过使用原生 Xamarin 来拥有非常定制的布局，或者如果代码共享更重要的话，使用 Xamarin 表单获得更基本的 UI。

React Native 允许您共享业务逻辑和 UI。业务逻辑为每个平台编写一次，并通过使用手机上运行的浏览器所使用的相同 javascript 引擎在不同的线程上执行。React 通过 JavaScript 桥呈现本机 UI 元素。最终结果将是一个具有原生外观和感觉的应用程序。因为语言是来自 web 的，并且因为业务逻辑(全局状态管理、API 调用等。)也可以很容易地与附带的 web 应用程序共享，React Native 对 web 开发人员很有吸引力，肯定非常适合某些产品。

当然，这些框架都有一些缺点，比如在某些平台上的性能，令人失望的工具，或者难以定制。颤振小组已经试图确定和改善所有这些缺点。他们的主要关注点是让开发人员以高速度交付漂亮、流畅的应用程序，并提供令人敬畏的开发体验。Flutter 和 React Native(或 Xamarin Forms)之间的主要区别在于，UI 不是由原生 UI 元素呈现，而是由 Skia 呈现引擎呈现。这意味着无论平台如何，你的用户界面看起来都是一样的。这导致特性的开发周期更快，因为您只需要创建一个布局。如果需要的话，Flutter 仍然允许在同一个环境中，从同一个代码库中获得看起来像是每个平台固有的视图。

要更深入地了解 Flutter 架构是什么样子，请查看 Flutter 团队在这里提供的架构概述。

让 Flutter 脱颖而出的还有它的基本元素，叫做 widgets。事实上，Flutter 中的所有东西都是一个小部件，而不仅仅是你的 UI 元素。最终，您的整个应用程序只是一个部件树。有些部件负责布局、动画、定位，但也负责构建其他部件。当然，虽然您可以创建自己的小部件，但 Flutter 已经提供了大量预构建的小部件供您使用。通过这种方式，您可以快速开发一个好看的应用程序，目标是具有材料设计小部件的 android 设备或具有 Cupertino 风格小部件的 iOS 设备。

**在主题演讲期间，有你没有预料到的公告吗？**

最意想不到的部分，是团队将 Flutter 呈现为一个可移植的 UI 工具包的方式。此外，社区已经采取了一些措施，使 Flutter 不仅可以在 Android 和 iOS 上工作，但看到在活动的主要阶段提出的倡议是一个游戏改变者，他们甚至更进一步，包括 web 应用程序。

除此之外，还有一些有趣的公告。Flare 工具将是一项受欢迎的资产。它允许设计者创建复杂的动画，可以很容易地在 Flutter 应用程序中导入并在代码中操作。这让我们想到了很多洛蒂动画，所以这是一个非常受欢迎的补充。

我们还惊喜地宣布与 Square 合作。他们已经为其他语言提供了一些非常有用的库，所以对 Flutter 来说，他们肯定是一笔巨大的财富。与 Nevercode 的合作并不令人惊讶。虽然我们自己还没有使用过它，但是有相当多的文章是关于 Nevercode 和 Flutter 的，所以这是一个合乎逻辑的发展。

我们看到的 Flutter 最大的缺点之一是还不支持集成网络视图或谷歌地图。随着几天前发布的插件，他们也摆脱了这个障碍，所以对我们来说，真的没有理由不开始使用 Flutter。

为什么他们选择 Dart 作为编程语言？

这是一个经常被问到的问题。在他们的 FAQ 页面上也有一个关于这个的部分。简而言之，Dart 正好满足了他们所有的需求。实际上，Dart 与其他现代面向对象语言并没有什么不同。如果您了解 Swift、Kotlin 或 Javascript，您很快就会了解 Dart。Dart 还允许以不同的方式编译代码。这就是为什么 Flutter 既能支持有状态热重装所需的 JIT(实时)编译，又能支持更高性能发布版本所需的 AOT(提前)编译。

不，Flutter 可能不会像一些人声称的那样是圣杯，但它绝对是一个值得关注的框架。现在 Flutter 不再处于测试阶段，它可以成熟了，我们可以用它积累更多的经验。正如 Bram 和 Yves 所说:真的没有理由不开始使用 Flutter！