# Android 上 expo 客户端的降级方法

> 原文：<https://dev.to/sasurau4/the-way-to-downgrade-expo-client-on-android-2mca>

## [T1】简介](#intro)

React Native 是世界上最活跃的开发项目之一。
根据[octo verse](https://octoverse.github.com/projects#repositories)的状态，React Native 在 2018 年贡献人数第二。

这意味着 React Native 经常被释放。
2018 年初最新版本是`0.52.0`。而最新版本(2019 年初)是`0.57.8`。Native 的发展速度有多快！

因此，Expo 是 React Native 应用程序的一套工具、库和服务，其发展速度与 React Native 一样快。

有一天我在世博客户端遇到了类似`No compatible manifest found. SDK Versions supported: 32.0.0, 31.0.0, 30.0.0, 29.0.0, 28.0.0, 27.0.0, 26.0.0 Provided manifestString: 25.0`的错误。

此错误意味着 expo 客户端尚未支持您应用程序中的 expo sdk 版本。
**遇到错误我强烈推荐升级你的 expo sdk 版本**！

我将解释在 Android 中继续开发您的应用程序的解决方法。
但是我强烈推荐`upgrade expo sdk`尽快。

## 步骤

### Step1。

从[https://expo.io/-/API/v2/versions](https://expo.io/--/api/v2/versions)中检查与您的 expo sdk 版本兼容的客户端版本

在我的情况下，我的应用程序使用的是`v25.0.0`的 expo sdk，因此兼容的 expo 客户端版本是`v2.3.0`。

### Step2。

进入[https://expo.io/tools#client](https://expo.io/tools#client)，点击`Download APK <Version>`的链接。你可以从 CDN 下载 apk 文件。

### Step3。

将 CDN 链接修改为您在步骤 1 中检查的兼容版本

例子:[https://d1ahtucjixef4r.cloudfront.net/Exponent-2.3.0.apk](https://d1ahtucjixef4r.cloudfront.net/Exponent-2.3.0.apk)

### Step4。

下载 apk，传输到 Android 设备上，直接从 apk 安装 app！仅此而已！

## 结论

尽管我解释了使用已弃用的 expo sdk 继续开发应用程序的解决方法，但尽快升级 sdk 还是很重要的。升级可能需要大量的时间和精力，但你同样可以从新的库和性能改进中受益。

我希望这篇文章能帮助你克服同样的错误。
感谢阅读！