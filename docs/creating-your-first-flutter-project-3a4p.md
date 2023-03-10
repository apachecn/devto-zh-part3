# 创建您的第一个颤振项目

> 原文：<https://dev.to/rodydavis/creating-your-first-flutter-project-3a4p>

Flutter 是 Google 的一个 UI 工具包，允许你创建任何平台都无法比拟的富有表现力的独特体验。您可以编写一次 UI，然后在任何地方运行它。是的，到处都是！Web、iOS、Android、Windows、Linux、MacOS、Raspberry PI 等等…

如果你喜欢视频，你可以关注我正在做的 YouTube 系列，名为“Flutter Take 5 ”,在那里我探索了你在构建 Flutter 应用程序时遇到的话题。在我浏览这个系列的时候，我还会给你一些提示和技巧。

<center>

## 什么是旋舞 [#](#what-is-flutter)

Flutter 最近在 Github 上跨越了 React Native，现在有超过 200 万开发者使用 Flutter 来创建应用。仅 Google Play 上用 Flutter 发布的应用就超过 5 万个。

[**【Flutter】-漂亮的原生应用创纪录的时间**

*Flutter 是 Google 的 UI 工具包，用于制作漂亮的、原生编译的移动、网络和桌面应用…*flutter.dev](https://flutter.dev/)

## 入门 [#](#getting-started)

一旦你安装了 SDK，开始是非常容易的。安装后，创建新的应用程序，插件和软件包是照明快。按照这个指南安装 Flutter:

[**安装**

*如何设置你的代码编辑器。*flutter.dev](https://flutter.dev/docs/get-started/install)

关于 Flutter 的一个好处是，它是作为一个开源项目开放开发的，任何人都可以参与其中。如果缺少了什么，您可以轻松地进行回购，并为缺少的功能制作 PR。

## 创建项目 [#](#create-the-project)

现在你已经安装了 Flutter，是时候创建你的第一个了😉)颤振项目！打开您的终端，导航到您想要创建应用程序文件夹的位置。一旦你“光盘”进入目录，你可以输入以下:

```
$ flutter create my_awesome_project 
```

Enter fullscreen mode Exit fullscreen mode

您可以将“my_awesome_project”替换为您想要的项目名称。使用 snake_case 很重要，因为它是 dart 中项目名称的有效语法。

恭喜你刚刚创建了你的第一个项目！

## 打开项目 [#](#open-the-project)

因此，您可能想知道我们刚刚创建了什么，让我们深入了解一下细节。如果你已经安装了 VSCode，你可以在终端中输入以下命令来打开你的项目:

```
$ cd my_awesome_project && code . 
```

Enter fullscreen mode Exit fullscreen mode

如果您愿意，可以在您喜欢的 IDE 中打开该文件夹。需要注意的两个重要文件是 pubspec.yaml 和 lib/main.dart

您的 UI 和逻辑位于“lib/main.dart ”,您应该看到以下内容:

您可以在“pubspec.yaml”上定义应用程序所需的任何依赖项和插件，您应该会看到以下内容:

## 跑项目 [#](#running-the-project)

运行应用程序也非常容易。虽然所有 ide 中都有按钮，但是您也可以从命令行运行您的项目来进行快速测试。您还可以为桌面配置 [Flutter，而无需等待仿真器预热。打开你的项目，在终端中输入以下内容:](https://flutter.dev/desktop) 

```
$ flutter run -d macos 
```

Enter fullscreen mode Exit fullscreen mode

请注意“-d macos”，因为您可以定制想要在什么设备上运行。您应该在终端中看到以下内容:

```
Building macOS application... Syncing files to device macOS... 141msFlutter run key commands.r Hot reload. 🔥🔥🔥R Hot restart.h Repeat this help message.d Detach (terminate "flutter run" but leave application running).c Clear the screenq Quit (terminate the application on the device).An Observatory debugger and profiler on macOS is available at: [http://127.0.0.1:58932/f1Mspofty_k=/](http://127.0.0.1:58932/f1Mspofty_k=/)Application finished. 
```

Enter fullscreen mode Exit fullscreen mode

您也可以同时运行多个设备。你可以在这里找到更多关于[颤动章鱼的信息。如果一切顺利，您应该会看到以下应用程序启动:](https://github.com/flutter/flutter/wiki/Multi-device-debugging-in-VS-Code)

[![](img/65e4836df8eab0b90e81981c4bf60ae0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sI0WqvTT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3560/1%2AO3W-7Ge-qfMk7i4CgqN-oQ.png)

在这一点上，它是一个非常基本的应用程序，但是展示在应用程序中改变状态是多么容易是很重要的。只需调用“setState()”就可以重建 UI。

## 测试项目 [#](#testing-the-project)

测试是我如此热爱 Flutter 的原因之一，为项目运行和编写测试非常简单。如果您查看文件“test/widget_test.dart ”,您应该会看到以下内容:

您可以非常容易地运行这些测试。打开您的项目，在终端中键入以下内容:

```
$ flutter test00:07 +1: All tests passed! 
```

Enter fullscreen mode Exit fullscreen mode

就像这样，你所有的测试都会运行，你可以发现任何你遗漏的错误。

您还可以通过键入以下内容来轻松地为您的应用程序生成代码覆盖率:

```
$ flutter test --coverage 
```

Enter fullscreen mode Exit fullscreen mode

这将在“coverage/lcov.info”中生成一个新文件，并将读取以下内容:

现在，您可以使用 LCOV 数据轻松创建徽章和图表。这里有一个包可以让这变得更简单:

[**test _ coverage | Dart 包**

*命令行实用程序，用于在 Dart VM 中运行测试并收集覆盖率数据。*发布开发](https://pub.dev/packages/test_coverage)

## 结论 [#](#conclusion)

Flutter 使得快速构建不依赖于网络或移动技术的应用成为可能。编写一个游戏可能很熟悉，因为你必须设计你自己的用户界面。你可以在这里找到最终的源代码:

[**rodydavis/flutter _ take _ 5**

*一个新的 Flutter 项目。这个项目是颤振应用的起点。一些资源让你开始…*github.com](https://github.com/rodydavis/flutter_take_5/tree/master/01_your_first_project)

你也可以在这里找到 Flutter 的源代码:

[**Flutter/Flutter**

_ Flutter 让你轻松快速的为移动及更远的地方构建精美的 app。- flutter/flutter_github.com](https://github.com/flutter/flutter)

[![](img/a40e16b1f9b09c032cbc26e029adbaf6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dNhedDVM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rodydavis.com/.netlify/functions/ga%3Fv%3D1%26_v%3Dj83%26t%3Dpageview%26dr%3Dhttps%253A%252F%252Frss-feed-reader.com%26_s%3D1%26dh%3Drodydavis.com%26dp%3D%252Fposts%252Ffirst-flutter-project%252F%26ul%3Den-us%26de%3DUTF-8%26dt%3DCreating%2520Your%2520First%2520Flutter%2520Project%26tid%3DG-JQNPVBL9DR)

</center>