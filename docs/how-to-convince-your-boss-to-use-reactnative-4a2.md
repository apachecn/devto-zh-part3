# 如何说服你的老板使用 React Native

> 原文：<https://dev.to/frantic/how-to-convince-your-boss-to-use-reactnative-4a2>

很可能你看到这篇文章是因为你对 React Native 很感兴趣。厉害！如果你有一个 web 工程师团队和一个全新的应用程序，很容易从 React Native 开始。

然而，大多数公司已经在商店里安装了应用程序。他们使用 native stack 来构建他们的应用程序:Xcode 或 Android Studio。React Native 也可以为这些公司增加价值，但制作混合应用程序会带来一些挑战。

在过去的 4 年里，我帮助将 React Native 集成到脸书和 Oculus 应用程序中。以下是我学到的一些经验。

[![](img/d46117680b9404060b5fc90088fa98ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hOKLl2ZE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://frantic.im/assets/gunnar-sigurdarson-1368301-unsplash.jpg)

# 从感同身受开始

*等等，从什么开始？*

了解参与你的项目的人——这是成功整合的第一步。

你的上司(项目经理、总监、CTO 等。)负责 app 上市风险小，时间线短，影响最大。公司已经在竞争激烈的环境中运营。对他们来说，任何新技术都是一种风险。

你的同事也有不同的观点。他们有使用“本地”技术构建应用的经验。他们知道如何在 IDE 中格式化字符串、构建 ui、访问网络、编写和调试代码。切换到一个完全不同的生态系统是非常难受的。对于走出舒适区有一种天然的抗拒。

[![](img/a3ab98cc10c2c2231f1cb13a46060dc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oAwV8m3Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://frantic.im/assets/maximilian-weisbecker-544039-unsplash.jpg)

【React Native 有布局充气机吗？如何更改此按钮的文本？

工程师可能会对 React、React Native 和 JavaScript 生态系统有一些看法。或者他们在某个地方看到了一个 React 原生应用，发现它的质量很差。有许多常见的误解，例如“JS 很慢”，或者“框架来来去去太快”。

这些神话并不是 JavaScript 特有的。作为人类，我们不可能体验每一个技术堆栈并有一个完整的观点。我们必须依靠从别人那里听到的东西。

这听起来可能不太令人鼓舞，但是愿意接受失败。在很多情况下，React Native 非常有用。但是也有在特定时间没有意义的有效情况。没关系。也许几个月后你可以重新考虑这个决定。你最不希望的事情就是分化你的团队，破坏建设性的工作关系。

为了消除现有的误解，用你应用程序中的一个真实功能证明它们是错误的(不要让其他人觉得愚蠢)。

# 求值

本指南假设您正在将 React Native 集成到现有应用程序中，并且已经使用平台相关代码编写了大量功能。全部重写是一个巨大的风险，对您的用户和业务几乎没有好处。这就是为什么很难说服你的团队和项目负责人这样做。

但是你不必重写你的应用程序！ **React Native 可以增量采用**。

在应用程序中找到一个可以立即从 React Native 实现中获益的区域。例如，它可能是一个有用但没有得到太多开发关注的屏幕，或者是一个作为 WebView 实现的表面。比如脸书以 Pokes 起家，Instagram 重建“喜欢的照片”等。

它必须有一个范围，你可以在几天内建立自己。这将是一个测试集成的不同方面的好地方:构建系统、会话共享、日志、崩溃报告等。

让新功能在 iOS 和 Android 上都能运行的加分。突然之间，一个人在很短的时间内就创造出了过去需要大量工程时间和协调的东西。

秀，不要说。

[![](img/8923888328d4951181cc2360e3aaad06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q4mXbl-d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://frantic.im/assets/hilthart-pedersen-602249-unsplash.jpg)

# 成为冠军

你不能只说“让我们用 React Native”然后躲在灌木丛里。

[TC39](https://github.com/tc39) ，一个发展 JavaScript 语言的委员会[有如下的过程](https://tc39.github.io/process-document/):对语言的改变被称为“提议”。领导提案的成员被称为“拥护者”。他们的任务是创建规范草案，与社区合作，推动规范向前发展。

成为贵公司的 React 本地冠军。承担起贯彻这一理念的责任。

成为冠军的第一步是更熟悉 React Native。创建一些原型(通过[重建你的应用](https://www.youtube.com/watch?v=I8b0v0uFXLs)的一些部分)，学习架构，准备好演示并帮助人们开始。

来自本土的工程师很可能会对新的生态系统感到不舒服和不知所措。创建一个环境，让 React Native 的入门变得超级简单:

*   有一个内部维基页面，上面有[关于如何开始的逐步说明](https://bitbucket.org/frantic/react-bnb)。可以将它视为 React Native 在您的组织中的“登录页面”。
*   构建一个自动化环境设置的脚本:安装 NodeJS 和其他依赖项，运行 yarn install 等。
*   设置好的 IDE 默认值。你不能要求 IntelliJ 用户配置和使用 VIM。记录或提交使 React Native 更容易上手的配置。比如:编辑器推荐、插件、语法方案、键盘快捷键等。
*   组织一次技术讲座或小型黑客马拉松，让您的团队了解 React Native，并在一个小项目中一起尝试。
*   创造一个提问、讨论和闲逛的地方。可能是 Slack 上的一个`#react_native`频道、群组、邮件列表等。在那里回答问题。

超级重要:如果有人不想使用 React Native，**确保他们的开发体验不会受到影响**。他们应该能够像以前一样工作，而不必了解或做任何关于 React Native 的事情。

# 关闭思绪

如果你想说服某人使用 React Native，在你的应用中加入一个没有它就不可能实现的功能。

我希望这个小指南能给你一些关于如何开始的想法。这只是开始。

许多大公司和小公司都在他们的混合应用中使用 React Native。不幸的是，[非常](https://medium.com/airbnb-engineering/react-native-at-airbnb-f95aa460be1c) [他们中很少](https://eng.uber.com/ubereats-react-native/)人分享细节。我认为这主要是因为集成的某些方面是特定于他们的基础架构的，谈论这些没有什么意义。