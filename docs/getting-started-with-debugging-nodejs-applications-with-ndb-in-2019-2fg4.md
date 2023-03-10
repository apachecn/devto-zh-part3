# 2019 年开始使用 ndb 调试 nodeJs 应用程序

> 原文：<https://dev.to/geshan/getting-started-with-debugging-nodejs-applications-with-ndb-in-2019-2fg4>

NodeJs 差不多是 9 年前发布的。NodeJs 的默认调试过程(read Node.js)相当笨拙。您已经意识到需要使用节点检查器向节点脚本添加- inspect。它也依赖于 chrome。然后查看正确的 web 套接字连接，这是硬连接，并使用 chrome 节点调试器进行调试。老实说，这是一件令人讨厌的事。

> # Finally, Google chromelabs released ndb, which they said was "an improved debugging experience for Node.js, enabled by Chrome DevTools". Nd is helpful for debugging nodejs applications.

我将展示用 [ndb](https://github.com/GoogleChromeLabs/ndb) 调试 nodejs 应用程序的一步一步的过程。换句话说，您将学习如何使用 ndb 调试 nodejs 应用程序。下面你可以看到 ndb 的行动，让我们卷起袖子开始吧:

[![](img/cc1424624708fbd0e80a96f6065ad4c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wiOHXBdf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2ADfF4Dhliy9rn1pRX.png)

## 先决条件

下面是开始之前的一些先决条件:

1.  您的系统上安装了 nodejs(这是显而易见的，但仍然值得一提)

2.  您已经了解了运行节点脚本和使用 nodejs 应用程序的一般知识。

3.  你有 nodejs 或任何其他语言的调试经验。

为了调试 nodejs 应用程序而不是另一个脚本，我将使用一个完整的 nodejs express 应用程序。这是一个开源应用程序，我用它来演示测试 nodejs 应用程序。

## 调试 nodejs express 应用程序作为演示

我正在使用我的开源通用 API 一步步调试 nodejs 应用程序。它是使用 express js 框架构建的。您还可以查看托管在 [Zeit Now](https://currency-api-nodejs.now.sh/api/convert/USD/AUD/2019-01-01) 上的跑步应用程序，以查看 2019 年 1 月 10 日美元对澳元的汇率。

应用程序的想法很简单。如果数据库中有可用的转换率，它将从数据库中获取。如果没有，它将从另一个 API 中获取，并将其发送回用户，同时将费率保存在数据库中(异步)以备后用。

您可以从 github 克隆应用程序，并运行 npm install 来为调试做好准备。这是一个非常简单的应用程序，大部分逻辑都在 exchangeRates.js [文件](https://github.com/geshan/currency-api/blob/master/src/exchangeRates.js)中。它也有摩卡[测试](https://github.com/geshan/currency-api/blob/master/test/exchnageRatesTest.js)，因为它是测试 nodejs 应用程序的演示。

## 1。开始，安装 ndb

安装 ndb 非常容易。开始调试 nodejs 应用程序所需要做的就是安装 [ndb](https://github.com/GoogleChromeLabs/ndb#installation) 。我建议在全球范围内安装:

```
# with npm
npm install -g ndb

#with yarn 
yarn global add ndb 
```

Enter fullscreen mode Exit fullscreen mode

如果你愿意，你也可以在本地安装和使用它。我必须解决的一件事是获得最新版本的 chrome，因为我发现了一些权限问题。

## 2。使用 ndb(不是 node 或 nodemon)运行应用程序

为了使用 ndb 调试 nodejs 应用程序，您可以使用 ndb 而不是 node 直接运行 nodejs 应用程序脚本。比如你在开发中习惯做 node index.js 或者 nodemon index.js。要调试您的应用，您可以运行:

请注意，您不需要进行任何检查，因此体验会更加顺畅。

> # *You don't need to remember a different port or go to chrome devtools to open a different checker window for debugging. Relieved!*

当你做 ndb 的时候，ndb 打开一个如下的屏幕。或 ndb index.js:

[![](img/5a34c504527fc3d854486cbbb4d3a11e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gKPKJKfr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2A3Kv-_GNmxVjWNhRh.png)

请在第 46 行添加一个断点。由于您已经使用 ndb 运行了应用程序，当您在浏览器上点击[http://localhost:8080/API/convert/USD/AUD/2019-01-01](http://localhost:8080/api/convert/USD/AUD/2019-01-01)时，它将在调试模式下运行，并在断点处停止，如下所示。我在 exchangeRates.js 上设置了断点，比如下面截图中的 46:

[![](img/970d10f74e5c6a3e8a860e2542f4f798.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ir9a9BcB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2APT7GcfFDOc16gj_2.png)

ndb 允许您运行任何脚本进行调试。例如，我可以运行 ndb npm start，它将使用 nodemon 运行。这意味着我可以在修改代码的同时调试应用程序，这很棒。

> # *As an example, it can be run with ndb npm to debug this nodejs express application.*

您还可以使用类似 ndb npm test 的命令来调试您的测试。

## 3。让我们调试一些代码

随着调试器的运行，我可以放置更多的断点，或者以我的速度和方便来运行代码。

> # *The basic shortcut keys are F10 single-step function call and F11 single-step function call.*

我假设您熟悉通常的调试工作流程。下面我已经推进到第 52 行:

[![](img/8e374a430917046d519337734bab2b62.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tgp6M3Bp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AckBDHfBnX38eOlcG.png)

## 更多调试的东西

与其他任何使用 ndb 的调试器一样，您可以:

1.  添加手表

2.  检查调用堆栈跟踪

3.  检查流程

    > # *[console] tab is also very helpful, if you want to quickly click some nodejs code in the context.*

在官方的[自述文件](https://github.com/GoogleChromeLabs/ndb#what-can-i-do)中阅读更多关于你可以用 ndb 做什么的信息。下面是有用的控制台截图:

[![](img/d0319ee26d245d221aed37aa3b40919e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oz_xckjV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AXx_2FTtzSpWLUn3u.png)

## 结论(TLDR)

用 ndb 调试任何 nodejs 应用程序都是更好的开发者体验。要使用 ndb 调试 currency API nodejs express 应用程序，请在安装了 node > 8 的情况下执行以下命令:

1.  npm 安装-g ndb

2.  git 克隆[【电子邮件保护】](https://geshan.com.np/cdn-cgi/l/email-protection) :geshan/currency-api.git

3.  cd 货币-api

4.  npm 安装

5.  ndb npm 开始

6.  ndb 调试器打开后，在 src/exchangeRates.js 的第 46 行添加一个断点

7.  然后在浏览器中打开[http://localhost:8080/API/convert/USD/AUD/2019-01-01](http://localhost:8080/api/convert/USD/AUD/2019-01-01)

8.  现在，应用程序应该在断点处暂停，享受！并继续调试。

如果它适用于这个应用程序，您可以用这种方法调试任何 nodejs 应用程序。

> # *Welcome to the new method of debugging nodejs applications. This method is independent of the browser and smoother than the default experience. Step up debugging nodejs application game.*

我希望这篇文章能帮助你更好地调试 nodejs 应用程序。如果你有任何关于调试 nodejs 应用程序或更好地使用 ndb 的其他事情要分享，请在下面评论！

* * *

*最初发表于[geshan.com.np](https://geshan.com.np/blog/2019/01/getting-started-with-debugging-nodejs-applications-with-ndb/)。*