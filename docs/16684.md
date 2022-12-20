# 如何使用 Android 密钥库验证用户的密码

> 原文：<https://dev.to/anadea/how-to-verify-users-passwords-using-the-android-keystore-p3k>

[![Android security](img/bf3b05b8dbf4717b3b03a8860e6f38ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YJT0QmHq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://anadea.info/uploads/image_attachment/image/1991/Security_android.jpg)

每个人都知道安全性对用户来说是一个非常重要的问题，因此开发者应该适当地关注它。互联网上有很多关于 Android 安全性的帖子——一些解释了加密算法之间的区别，另一些则致力于 Android 安全性的基础知识。在这篇文章中，我将重点介绍 Android 开发人员在工作中可能会遇到的一个用例。

假设我们开发了一个应用程序，我们需要识别用户——我说的是一个常规的登录屏幕。我们正在开发一个独立的应用程序，所以我们不能像往常一样依赖后端用户验证功能。当然，我们需要一个安全的授权流程。

<center>![Knock Knock](img/47d42ee455e0c081650d19bf63594e44.png)</center>

<center><sub>Image source: [Knock Knock](https://en.wikipedia.org/wiki/Knock_Knock_(play))</sub></center>

保存用户密码是一种糟糕的做法，没有一个开发人员愿意为泄露用户凭证负责。关键是许多用户很可能在许多服务中使用同一个密码。因此，我们的应用程序中泄露的密码可能是许多其他服务的关键。

## 解决 Android SDK 中的一个安全任务

让我们试着找出我们可以用什么来解决 Android SDK 中的安全任务。我们首先想到的工具是 Android 4.3 中引入的 KeyStore API。我们可以使用密钥库作为存储加密密钥的容器。密钥库最大的好处是它可以访问 Android 系统密钥库，并且由系统进程维护。通过这种方式，我们将保管加密密钥的任务委托给系统服务。

我们要做的是:

1.  为异步加密生成一个密钥对，并将其放入密钥库中。
2.  用数字签名签署用户密码。将使用步骤 1 中的密钥启动签名过程。
3.  使用步骤 2 中的签名验证另一个用户密码。

### 别说了，我们开始编码吧！

首先，我们需要用 RSA 特定的参数创建一个密钥对。要做到这一点，我们应该从为 RSA 密钥创建参数开始，但是我们应该为 Android M 和更高版本做一点不同的事情。

这就是我们如何获得 M 版本以下 Android SDK 的规范: