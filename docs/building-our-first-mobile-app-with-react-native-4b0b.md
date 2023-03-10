# 使用 React Native 构建我们的第一款移动应用

> 原文：<https://dev.to/samdelacruz/building-our-first-mobile-app-with-react-native-4b0b>

嘶！这是从[兰德科技博客](https://tech-blog.landinsight.io)交叉发布的。哦，还有[我们正在招人！](https://landinsight.io/careers)

[React native](https://facebook.github.io/react-native/) 是一个跨平台的应用开发框架，主要用于构建 iOS 和 Android 的应用，几乎完全使用 Javascript / React。它允许 Javascript 开发人员使用他们最熟悉的工具和技术来创建功能全面的原生应用程序，这些应用程序看起来和感觉上与 Swift / Java 类似。

几个月前，我们使用 React Native 推出了我们的第一款移动产品。 [LandInsight GO](https://pages.landinsight.io/li-go) 是主 LandInsight web 应用程序的跨平台配套应用程序，允许用户在办公室外探索和保存潜在的开发网站，以及访问他们现有的 LandInsight 保存的网站。

在这篇文章中，我将谈论一些用于构建应用程序的关键技术，围绕它们做出的决策，以及在此过程中遇到的一些棘手问题。

## 世博 vs 本土

Expo 是一个框架、工具集和集合库，旨在使 React 本地开发比其基本形式更流畅。主要的吸引力在于，你可以非常快速地构建一个应用，而不需要安装任何原生的 SDK 工具(即 Xcode、Android Studio)。这对于快速原型化和验证一个应用的想法来说是非常棒的。他们甚至支持构建和发布到 iOS 和 Android 应用商店。然而，这也带来了一个缺点，即您不能安装额外的本机依赖项(没有额外的复杂性)。

我们很早就知道我们将需要使用 [Mapbox 的本地地图库](https://github.com/mapbox/react-native-mapbox-gl)，所以不幸的是 Expo 是不可行的，我们完全本地化。

## JavaScript vs 流 vs 类型脚本

这个决定本身可能值得写一篇博文，静态类型的利弊可能会被详细讨论。当我们开始构建我们的应用程序时，React Native 的现状是纯 JavaScript(和 JSX)， [Flow](https://flow.org/) 是唯一受支持的静态类型检查器，这要感谢 Babel， [TypeScript](https://www.typescriptlang.org/) 需要一个相当复杂的两部分构建过程。由于其相对简单的设置，当时的决定是随流运行。我们刚刚开始在后端 Node.js 代码中试验 Flow，所以这是一个很容易做出的决定。

通过使用 Flow，我们可以在调用参数类型不正确的函数时获得即时反馈，重构也更加容易(也更加安全！).然而，Flow 中的类型检查过程可能会变得非常慢，因为后台进程使用了大量的内存。这不是 TypeScript 经常遇到的问题，一般来说该工具似乎工作得更好(特别是 Visual Studio 代码)，所以事后看来，我们使用 TypeScript 会更好。

今天，React Native 几乎可以直接支持 TypeScript。一般来说，这种语言有着比 Flow 更广泛的应用和更活跃的社区。此后，我们已经将后端流代码迁移到 TypeScript，并将很快将 React 本机流代码迁移到 TypeScript。如果我现在开始一个新的 React Native 项目，我会选择 TypeScript 而不是 Flow(或者普通 JavaScript)。

## 构建流程

因为我们选择了本地构建而不是 Expo，所以我们负责整个构建/测试/部署过程。要手动完成这项工作，需要使用 React 本地命令行工具进行本地构建，并将生成的本地二进制文件上传到 App Store 和谷歌 Play 商店。但是我们可以做得更好！没有人喜欢做这些耗时的手工任务。我们的整个 web 应用程序堆栈都是持续部署的，为什么要降低要求呢？我读过 Codepush 的魔力，它允许开发者绕过臭名昭著的应用商店审查过程，通过无线方式将更新推送到他们的 React 原生应用。Codepush 与 [Visual Studio 应用中心](https://appcenter.ms/)无缝集成，后者是微软的 CI 平台。通过使用 App Center，每次提交给 master 都会为 Android 和 iOS 启动一个新的构建，如果构建成功，就会被推送到各自的 app store 平台。App Center 几乎很容易安装，并且极大地简化了为 React 本地项目设置 CI/CD 的过程。

构建本身并不是很快。如果您习惯了典型的 Node.js 或 Webpack 构建时间，那么准备好失望吧。React Native 并不是特别轻量级，一旦您将一些本机模块混合在一起，预计构建时间会超过 15 分钟。这并不是对 App Center 的批评，但它确实意味着拥有一个本地测试过程比简单地依赖 CI 平台来告诉你构建是否被破坏要重要得多。为了测试，我们使用了脸书的开源框架 [Jest](https://jestjs.io/) 。单元测试作为构建过程的一部分运行，以防止一个坏的应用程序落入我们的客户手中！

## iOS vs Android

React Native 是一个跨平台的框架。一个代码库可以用来输出许多不同平台的原生应用程序。这是我们选择 React Native 而不是纯本机代码的主要原因之一，但是它也有自己的问题。

公平地说，虽然用 React Native 构建的 iOS 和 Android 应用程序很相似，但它们并不完全相同。运行在 iOS 上的相同代码将会有不同于 Android 的外观和感觉。本机模块的行为也可能不同，因为它们必须为每个平台单独编写。这些微小的差异累积起来，导致了不和谐的开发体验。

我给任何想要构建跨平台 React 原生应用的人的主要建议是尽早并经常在两个平台上进行测试。为 iOS 构建 100%的应用程序，并希望它能为 Android 工作是没有用的(因为它不会)。至少，为两个平台设置 CI。确保你能同时为 iOS 和 Android 开发，因为这将为你以后省去很多麻烦(和大量的咖啡)。

## 结论

React Native 是 JavaScript 开发人员直接进入移动开发的绝佳工具。生态系统在不断发展，采用率也在不断提高。因此，围绕构建生产应用的工具和服务也变得越来越成熟。花一点时间预先为 Android 和 iOS 建立一个 CI 渠道，你可以确保在构建自己的跨平台应用程序时走上成功之路。

在以后的帖子中，我会讨论一些我在构建应用程序时问自己的其他问题。如果你对 React Native 这样的前沿技术感兴趣，那就加入我们吧！我们在招人！。