# Android JS 与其他框架/库有何不同？

> 原文：<https://dev.to/chhekur/how-android-js-is-different-from-other-frameworks-libraries-5f3i>

# 什么是 Android JS？

Android JS 是在 GitHub 上开发和维护的开源框架。Android JS 允许使用最初为 web 应用程序开发的前端和后端组件来开发 Android 应用程序:后端的 Node.js 运行时和前端的 Android Webview。Android JS 框架可以用于采用 JavaScript、HTML 和 CSS 等前端技术的 Android 应用程序。

# 什么是科尔多瓦？

Apache Cordova，以前称为 Adobe PhoneGap，是一个开源框架，用于使用 HTML5、CSS3 和 JavaScript 等 web 标准技术创建跨平台的混合移动应用程序。科尔多瓦已经存在很多年了，在互联网上有很多可用的资源。此外，还有很多插件可以用来增强你的应用程序。

那么我们来说说 Android JS 有什么不同？
我们来讨论一下 Android JS 的 app 架构

### Android JS 的 App 架构

这是 Android JS 的架构

在这里，您可以简单地看到有一个由

*   Android JS，这意味着你可以在你的应用程序中使用任何 NPM 软件包。
*   Android JS 提供了两个进程后台进程和前台进程——前台进程只是你的应用程序的视图，你可以在 HTML/CSS、Angular、Phonon Framework、Onsen UI、Vue 等中创建它们… —后台进程是你的应用程序与节点环境通信的主要进程。
*   为了实现前后进程之间的通信，Android JS 提供了 IPC(进程间通信)。Android JS 提供各种原生 Android 库，如:通知、吐司、呼叫、短信等
*   Android JS 还提供了浏览器库，比如:摄像头 API，麦克风 API。现在我们来讨论一下 Cordova 的应用架构

### 科尔多瓦的 App 架构

这是科尔多瓦的建筑

*   科尔多瓦提供了前端程序，它只是你的应用程序的视图，你可以在 HTML/CSS，Angular，Phonon Framework，Onsen UI，Vue 等中制作它们
*   科尔多瓦提供了各种浏览器库，如:通知，相机，麦克风等…
*   Cordova 不提供本地库。
*   Cordova 不提供任何后端支持，但你可以建立基于 API 的应用程序，它使用 API 从服务器获取数据。科尔多瓦只是浏览器 API 的前端。

# 结论

不管怎样，每个框架都有它的优点和缺点。这实际上取决于您喜欢什么，以及您的应用程序需要什么。最后，两者都是创建和调试跨平台应用程序的绝佳选择。
Android JS 提供后台和前台两种流程，而 Cordova 只提供前台流程。

*   Android JS 在你的应用程序中提供了 Node JS 运行时环境，而 Cordova 根本没有提供这样的功能。
*   Android JS 提供原生 Android 库以及浏览器 API，而- - - Cordova 只提供浏览器 API。
*   这两个框架都是开源的。
*   两者都有各种前端框架可以使用。

干杯！