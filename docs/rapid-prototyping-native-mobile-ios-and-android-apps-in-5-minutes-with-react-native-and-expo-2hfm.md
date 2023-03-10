# 使用 React Native 和 Expo 在 5 分钟内快速制作原生移动 iOS 和 Android 应用的原型

> 原文：<https://dev.to/olioapps/rapid-prototyping-native-mobile-ios-and-android-apps-in-5-minutes-with-react-native-and-expo-2hfm>

当你对一个应用程序有了新的想法，你想尽快从用户那里得到反馈，看看这个想法是否可行，然后开始完善它。一旦人们尝试了，最好的反馈就来了。

所以，我们的主要目标是尽快从使用这款应用的人那里获得真实的用户反馈。

在这篇文章中，我们将开始创建一个应用程序，并在 5 分钟内与他人分享，这样他们就可以在你创建它时给你反馈。

次要目标

*   原生应用程序-不是在浏览器中运行的响应性 web 应用程序，而是完全的原生应用程序
*   跳过应用程序商店——在应用程序商店获得批准是一个复杂、耗时的过程。

考虑到这些目标，现在最好的工具之一就是 React Native 和 Expo。

React 是一个声明式的、高效的、灵活的 JavaScript 库，用于构建用户界面。

[React Native](https://facebook.github.io/react-native/) 是一个使用 React 构建原生 iOS 和 Android 应用的框架。

Expo 是一个快速创建 React 本地应用的工具。使用 Expo，您可以启动一个新的应用程序，在浏览器中预览，然后在自己的设备上看到它，并几乎立即(5 分钟)与他人分享。

## 品尝小吃

[![Snack](img/ea9baed207e35a1ccb29a3050fb66095.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FbqXVM6t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.olioapps.com/asseimg/rapid-prototyping-react-native-expo/rapid-prototyping-react-native-expo-snack-640.gif)

小吃是一个轻量级的在线集成开发环境，允许你在几秒钟内在浏览器中运行代码。它非常适合快速实验和分享例子。

进入 [snack.expo.io](https://snack.expo.io/) 。你会看到一些代码，和一个 iPhone 的图像，有一个大的“点击播放”按钮。点击它，应用程序将在设备模拟器中启动。

更改代码中的文本，它就会更新。

## 安装 Expo 工具链

首先安装 [Node.js](https://nodejs.org/) 。建议使用最新版本。

其次，为节点安装 Expo 包。

```
npm install expo-cli --global 
```

第三，在你的设备上安装世博移动应用。不是 [iOS](https://itunes.apple.com/us/app/expo-client/id982107779?mt=8) 就是[安卓](https://play.google.com/store/apps/details?id=host.exp.exponent&hl=en_US)。

## 新建一个 app

一旦所有东西都安装好了，您就可以从您的终端运行下面的命令，让一个新的应用程序在开发中运行。

```
expo init my-new-project
cd my-new-project
expo start 
```

## 扫描 app 二维码，在设备上运行

您将在终端和开发工具中看到一个二维码。要在您的设备上打开您的项目，只需在 Android 上使用 Expo 客户端应用程序扫描二维码，或在 iOS 上使用相机应用程序的内置二维码扫描仪。

## 立即看到您的变化

*   编辑主屏幕`screens/HomeScreen.js`
*   观察您的设备更新

## 注意我们没有做的事情

*   没有苹果开发者计划注册(99 天以上)
*   不创建代码签名证书
*   没有从计算机到设备的电缆
*   Xcode 中没有编译
*   没有上传到试飞
*   不邀请用户试飞
*   没有 app store 审核流程

当然，我们也想做这些事情，但是只有当我们准备好发布的时候。到那时，我们将通过真实的用户反馈来验证和改进我们的想法，所以它真的很棒。

而且我们不需要重新开始使用不同的平台或框架来实现这一目标。React Native 和 Expo 支持从开发到应用商店部署的全生命周期。

你需要一个原型应用还是快速构建的应用？Olio 应用程序可以提供帮助。我们已经构建了多个 iOS 和 Android 应用程序，并将其发布到 app store。告诉我们你的项目吧！