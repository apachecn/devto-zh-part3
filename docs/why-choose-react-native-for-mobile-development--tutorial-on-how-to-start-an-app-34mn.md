# 为什么选择 React Native for Mobile Development+如何启动应用程序的教程

> 原文：<https://dev.to/flatlogic/why-choose-react-native-for-mobile-development--tutorial-on-how-to-start-an-app-34mn>

# 如何搭建 React 原生 App

很明显，在 2019 年，拥有一款移动应用对你的业务来说是必要的。我不会在介绍中提到这个。

让我们直接进入文章的本质。在我们深入 React 原生教程之前，让我们快速概述一下 2019 年仍然存在的移动开发框架和技术。

以下是现代移动应用中使用的技术列表:

*   Android 和 iOS 的本地语言(Java/Kotlin，Objective–C，Swift)
*   反应自然
*   Apache Cordova 和 Cordova 工具(PhoneGap，Ionic 等。)
*   Xamarin
*   摆动

简而言之，2018 年移动 app 开发有三大类型:原生 App 开发、混合开发、跨平台开发。

如果我们简单分析一下允许几个平台使用一个代码库的技术的流行程度，我们可以看到 React Native 排在第一位。

[![React Native](img/b7265c0d3bd8f509d92573edc375d2a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bzRITRdN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://flatlogic.com/blog/wp-content/uploads/2019/01/image1-1024x480.png)

在本文中，我们将帮助您为您的想法选择正确的技术，并向您展示如何开始开发一个简单的 React 本机应用程序。

## 其他技术的利弊

在这里，我将描述一些其他技术，并指出它们的优缺点:

## 原生移动开发

原生移动应用是用特定编程语言编写的智能手机应用，如 iOS 的 Objective C/Swift 或 Android 操作系统的 Java/Kotlin。原生移动应用提供快速性能和高可靠性。然而，这种应用程序类型的开发成本很高，因为它依赖于单一的操作系统类型。

#### **优点:**

*   支持所有 API
*   表演
*   大型社区
*   错误更容易被发现(因为严格的语言)

#### **缺点:**

*   耗时的
*   需要雇用人才
*   独立代码库
*   没有可重用的代码

## 科尔多瓦

Apache Cordova 使软件程序员能够使用 CSS3、HTML5 和 JavaScript 为移动设备构建应用程序，而不是依赖于特定于平台的 API，如 Android、iOS 或 Windows Phones 中的 API。它能够根据设备的平台包装 CSS、HTML 和 JavaScript 代码。由此产生的应用程序是混合的，这意味着它们既不是真正的本地移动应用程序，也不是纯粹基于 web 的应用程序。

#### **优点:**

*   简单快速地制作原型
*   让 web 开发人员顺利过渡到移动开发
*   离线使用
*   更容易创建矢量图形来设计规格

#### **缺点:**

*   缓慢的数据处理
*   有限的 API 支持
*   支持不同的设备可能成为一场噩梦

## Xamarin

Xamarin 是一个使用 C#开发跨平台移动应用程序的框架。Xamarin 提供了一种单一语言 C#和运行时，可以在三种移动平台(Android、iOS 和 Windows)上工作。利用 Xamarin，我们可以制作一个外观和感觉都完全自然的移动应用程序。您可以编写一个 C#代码库，该代码库可以访问本机 SDK 的所有可用功能。

#### **优点:**

*   更快的开发周期
*   接近原生应用性能
*   原生用户体验

#### **缺点:**

*   庞大的操作软件开销
*   核心用户界面创建是不可移植的
*   在 Xamarin 应用程序开发中开发的代码不能重用或转移到 HTML5 或 iOS 或 Android 的原生应用程序

## 飘起

Flutter 是一个开源 SDK，用于为 iOS 和 Android 创建高性能、高保真的移动应用程序。凭借丰富的材料设计和 Cupertino (iOS)小部件和行为，Flutter 可以轻松开始构建漂亮的应用程序。您将使用 Dart 编写您的 Flutter 应用程序。如果您已经了解 Java、JavaScript、C#或 Swift，那么 Dart 语法应该看起来很熟悉。Dart 是使用标准的 Android 和 iOS 工具链为您的应用程序需要运行的移动平台编译的。

#### **优点:**

*   跨平台
*   热重装
*   由于 Dart(强类型语言)，代码更少
*   谷歌官方支持

#### **缺点:**

*   相对较新的框架
*   仅限手机
*   Dart 不受欢迎，所以社区很小
*   谷歌在建设新项目方面有着糟糕的历史

## 反应原生

React Native 是一个构建 UI 组件层次结构的框架，用于构建 JavaScript 代码。它拥有一套适用于 iOS 和 Android 平台的组件，可以构建具有原生外观和感觉的移动应用程序。

#### **优点:**

*   单一代码库
*   让 web 开发人员顺利过渡到移动开发
*   可重用组件
*   低开发成本
*   更少的时间支出

#### **缺点:**

*   每个平台都有特定的设计指南
*   性能问题
*   本地 API 支持(需要本地语言知识)
*   对操作系统功能的访问受限(需要了解本地语言)

## 为什么选择 React Native？

让我们展示为什么使用原生 React 移动应用程序开发是当今一个伟大的想法——有时甚至比原生开发更好。

移动开发经历了前所未有的增长。根据统计数据，到 2020 年，移动应用程序将通过应用程序商店、广告和应用内购买产生约 1880 亿美元的收入。

[![mobile app revenue](img/b22cd187a095091cd947a63abb60d390.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vxp6ZokT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://flatlogic.com/blog/wp-content/uploads/2019/01/image2.png)

单一用户和企业用户需要高标准的应用程序，具有完美的性能、多屏幕、易于导航和良好的设计。

另一方面，与跨平台应用程序相比，高性能、高质量的原生应用程序的开发非常耗时，而跨平台应用程序的开发速度更快，但在性能和支持方面有所妥协。

React Native 似乎是一个可行的解决方案，可以在短时间内构建高质量的应用程序，并提供与原生应用程序相同的性能和用户体验标准。

[![react native](img/85b21ace1c50ff0d5ff906358e941628.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C_vtJkIw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://flatlogic.com/blog/wp-content/uploads/2019/01/image3-1024x356.png)

过去开发应用程序的公司必须集合和管理两个团队和两个不同的代码库。这导致了分离的团队和更慢的构建时间。有了 React Native 这样的移动 UI 框架，工程师可以使用一种语言和一个代码库为两个平台编写代码。

React Native 应用程序被编译成本机编写的代码，这使得 React Native 不仅可以在两个操作系统上工作，而且在两个平台上具有相同的功能，没有延迟。

使用 React Native 的另一个显著优势是实时更新。随着 JavaScript 的广泛使用，开发人员能够将更新直接推送到用户的手机上，而不是通过 app store 更新周期。

React Native 还将增加你的人才库:开发者能够使用 Javascript 构建移动应用。Javascript 在前端工程师中相当普遍，后端工程师也相对容易掌握。这样，更多的前端工程师第一次有机会尝试移动开发。

美中不足的是:开发人员仍然需要编写本地代码。对于简单的应用程序，React Native 是一个很好的选择，在这些应用程序中，API 在两个平台之间有一个清晰的桥梁。

最终，API 将不可避免地停止按照您想要的方式运行，并且无论如何，要进行必要的调整，您必须深入到本地库。

如果你雇佣一个 JavaScript 开发人员来开发你的 React 原生项目，你可以很容易地期望他编写原生代码并弥补现有的功能缺陷。

## 如何在 React Native 上启动项目的教程

### 先决条件

为了能够遵循本教程，您必须熟悉 React 并具备扎实的 JavaScript 知识。即使你以前没有使用 React 的经验，你也能够理解它。在本教程中，我们将解释一些基本的反应概念。

选择您的开发工具。在 React Native 上快速开始开发的开发工具的最佳配置是:

带有 React 本机工具的 Visual Studio 代码–一个 Visual Studio 代码扩展，为 React 本机项目提供了一个开发环境。使用该扩展，您可以调试代码，从命令面板快速运行 react-native 命令，并使用 IntelliSense 浏览 React Native APIs 的对象、函数和参数。

Expo 是一个围绕 React Native 构建的工具链，帮助您使用 JavaScript 和 React 构建原生 iOS 和 Android 项目。Expo 使您能够仅使用 JavaScript 构建跨平台的原生应用。使用您最喜欢的文本编辑器编写强大的 React 原生组件，而无需打开 Xcode 或 Android Studio。

使用 Sketch 设计屏幕，使用 Instabug 调试应用和错误监控。

### 项目的开始

要为 React Native 设置环境，您需要安装一些东西。我把 OSX 作为一个建筑平台。

1.  安装[节点](https://nodejs.org/en/)和 [NPM](https://www.npmjs.com)
2.  开始构建 React 原生应用有两种方式:使用 Expo 或者从 CLI 构建 React 原生 App。世博会是最简单的方式:它消除了一些预先的障碍。

`npm install -g expo-cli`

然后运行以下命令创建一个新的 React 原生项目:
`expo init NotAnAwesomeProject
cd NotAnAwesomeProject
npm start`

1.  在您的 iOS 或 Android 手机上安装 [Expo](https://expo.io) 客户端应用程序，并连接到与您的电脑相同的无线网络。
2.  在您选择的文本编辑器中打开 App.js 并编辑一些行。保存更改后，应用程序应该会自动重新加载。

就是这样！

## 回顾和总结

如果您的应用已经可以处理复杂的业务逻辑，并且许多屏幕都是基于 Swift/Java/Kotlin 构建的，并且一个本地移动开发人员团队在支持和开发应用方面做得很好，我不建议您转向混合或跨平台技术。这就是 Airbnb 停止使用 React Native 开发他们的移动应用程序的原因。

如果你计划快速原型化你未来的应用或者从头开始构建应用，那么跨平台技术正适合你——尤其是如果你熟悉 Javascript 及其 UI 构建框架的话。React Native 由于其易于采用、人力资源和开发速度，将非常适合这些目标。

## 关于作者和原生发酵剂的反应

我们是 [React 原生启动器](https://reactnativestarter.com)的创造者。React Native Starter 是一个移动初学者工具包，允许您的团队完全专注于开发使您的产品从竞争对手中脱颖而出的功能，而不是从头开始构建您的应用程序。你有 16 个现成的组件，先进的图表库，聊天应用加上更多的屏幕和组件。
[Flatlogic](http://flatlogic.com/) 正在开发[管理仪表板模板](https://flatlogic.com/admin-dashboards)和 [react 原生模板](https://reactnativestarter.com/)。我们是白俄罗斯排名前 20 位的网络开发公司之一。在过去的 5 年里，我们已经为小型创业公司和大型企业完成了大约 20 个大项目。作为一个团队，我们总是有帮助客户的强烈愿望。

*最初发布于*[https://flat logic . com/blog/why-to-choose-react-native-for-mobile-development-tutorial-on-how-to-start-an-app/](https://flatlogic.com/blog/why-to-choose-react-native-for-mobile-development-tutorial-on-how-to-start-an-app/)