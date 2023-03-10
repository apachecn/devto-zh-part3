# 跨浏览器兼容性和 React JS Web 应用程序

> 原文：<https://dev.to/lambdatest/cross-browser-compatibility-and-react-js-web-apps-4gjf>

有时我觉得生活中的事件按顺序发生，你经常会遇到令人畏缩的时刻。就像当你嘴里含着沙子或者粉笔在黑板上发出痛苦的声音。对我个人来说，最糟糕的时刻是当我遇到一个非常糟糕的网络应用程序或网站。我说的坏，是指 ie 浏览器坏(开个玩笑，这是个不错的浏览器)。没有方向的导航，功能不工作，糟糕的设计和痛苦的 UX。最大的问题是缺少跨浏览器兼容性。

它是如此糟糕以至于我喜欢它。在花了 30 分钟的时间后，我探索了导致它失控的问题。

## 为什么反应土人？

React 只是一个基于 JavaScript 的应用程序的 UI 和功能库。我们最近在上一篇文章《在 2018 年选择正确的 JavaScript 框架》中谈到了 ReactJS，所以在这篇文章中，我们将更详细地了解 ReactJS 如何帮助开发浏览器兼容的 web 应用程序。

所以回到手头的问题，是什么让 React 如此伟大？

它伴随着大量的社区支持。最大的好处是可以在 React 中编写 JavaScript 代码，transpiler 会自动处理因版本或语言差异而产生的兼容性问题。它还提供了大量的社区支持，并与 babeljs 等有助于浏览器兼容性的工具进行了本机集成。不完全是 JS，但它是 JSX，它甚至比 JavaScript 更强大。

“写一次用遍”。

***嘿！你知道吗 [CSS 美化](https://www.lambdatest.com/free-online-tools/css-prettify?utm_source=devto&utm_medium=organic&utm_campaign=jul25_sd&utm_term=sd&utm_content=free_tools)使用这个 CSS 美化在线工具美化你的 CSS 代码。输入你的 CSS 代码来美化，格式化，美化，使其更具可读性。***

## 跨平台进场

使用 React native 开发应用程序的一个显著特点是它提供了跨平台能力。让我们想象一下，你必须为 iOS 和 Android 开发一个应用程序。你所要做的就是为应用程序编写源代码。React Native 重用了您编写的 JavaScript，并为其添加了跨平台的维度。您所要做的就是指定您心目中的特定平台，剩下的事情就交给它了。在自身特性内对本地报价做出反应。

## 跨浏览器兼容 React Native

虽然跨平台方法有助于我们构建健壮的应用程序，但跨浏览器兼容性问题在某种程度上仍然是一个难题。我们转向它的父生态系统，使用 React native 的最大好处是它类似于 React，甚至 Preact，所以如果你愿意，你可以利用这一点。构建 Web 应用程序时，您可以利用预定义的设置，将自己从繁重的工作中解救出来。它也带有 PWA 设置，使用服务人员没有任何额外的负担。

它甚至提供了像 live reload 这样的出色功能，允许您随时更新或更改源代码。这真的令人印象深刻，因为它带来了某种自由感，这是其他框架/库无法提供的。

此外，React 实现了一个完全不同的独立于浏览器的 DOM 系统。实现是为了使应用程序跨浏览器兼容。使用 React native 时，内存泄漏更少，这直接提高了性能。

***你知道吗？ [XML 美化工具](https://www.lambdatest.com/free-online-tools/xml-prettify?utm_source=devto&utm_medium=organic&utm_campaign=jul25_sd&utm_term=sd&utm_content=free_tools)让你漂亮地打印可扩展标记语言(XML)数据文件。您还可以自定义缩进以及每个缩进级别使用的空格数。***

## 合成事件

在事件处理方面，Native 与 React 有类似的方法。事件处理类似于 DOM 操作，语法变化很小。合成事件由 React 根据 W3C 规范定义。代替 sting，你可以传递一个函数事件处理程序(JSX 真是太神奇了！).

## 反应过来的武器库

重复大量的编码功能没有意义。感谢无限的社区支持(脸书已经投入了大量资源)，开发者可以更容易地找到各种工具。如果你读了以前关于 Babel 和 Webpack 的博客，你就会知道得到他们的支持是多么有帮助。React framework 确保了这两个工具以及其他一些工具的正常运行。底线是你可以疯狂地使用库函数，它们会处理更小的包大小和跨浏览器兼容性。从免费实用的 [React Redux 测试开始。](https://www.lambdatest.com/testing-cloud/react-redux-testing?utm_source=devto&utm_medium=organic&utm_campaign=jul25_sd&utm_term=sd&utm_content=testing_cloud)

***看看这个: [Android 模拟器](https://www.lambdatest.com/android-emulator-online?utm_source=devto&utm_medium=organic&utm_campaign=jul25_sd&utm_term=sd&utm_content=webpage) -确保您的应用程序兼容最新和传统的 Android 操作系统、设备和浏览器。***

## 包装完毕

React 显然是所有框架/库的赢家，Angular 需要想出一个新的策略。随着 Vue 越来越受欢迎，谷歌宣布 Flutter，React 将面临两个不同方面的威胁。互联网最大的好处就是“不确定性”。

谁知道…

我为克罗斯感到难过。🙁