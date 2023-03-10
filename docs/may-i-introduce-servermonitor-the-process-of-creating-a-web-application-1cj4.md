# 我可以介绍一下吗？ServerMonitor！|创建 web 应用程序的过程

> 原文：<https://dev.to/inspiredprogrammer/may-i-introduce-servermonitor-the-process-of-creating-a-web-application-1cj4>

是时候了！🎉🎉 🔥🔥
服务器监控 1 . 0 . 0 版本正式发布，感觉棒极了！💪

[![ServerMonitor V1.0.0](img/0518297454cf7ef7c57d245d0dc40996.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u94TeOx_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/abCnQsI.png)

# 简介🤝

在这篇文章中，我想告诉你我从开始到结束开发我的第一个真实项目的过程。我经常遇到这样的问题:我开始的任何项目都无法完成。

也许这篇文章会激励你开始你自己的项目，并努力完成它。

* * *

# 项目是如何开始的？🙋

ServerMonitor 最初只是一个检查网站是否在线的简单想法。我对这个项目的目标是尽可能简单地开始。在之前的尝试中，我对 web 应用程序的想法太大了，无法在可管理的时间内完成。如果你想成功地完成你的项目，从你能想象的最小开始的方法是至关重要的。

* * *

# 规划📝

为了了解网站监控应用程序的样子，我搜索了其他已经这样做的网站。经过一番搜索，我找到了 [isitdown.site](https://isitdown.site/) 。这个网站已经做了我想在我的网络应用程序中拥有的一切。最棒的是，它是用 Flask 创建的，并且是开源的。如果我不知道更多的话，这个网站的源代码有时会很有帮助。

现在，我知道了我的项目应该是什么样子和行为。这帮助我找到了我必须做的第一个任务。

* * *

# 发展中！💻

### 起始阶段🚲

我必须做的第一个任务显然是创建一个新的 Flask 项目。
之后，我开始编写 web app 的基本功能。
意思是:创建一个[函数](https://github.com/ChristianLutzCL/Website-Monitoring/blob/db798e69d162bc9ab7f507157d1362b2cb49db60/monitor.py)，检查网站的响应并将其打印在屏幕上。

[![First result](img/a1458b7fe8823793adae9ed04b347f6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OGqaQbcY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xf7igi5pg2z35p2cd1l7.png)

到目前为止一切顺利。在基本功能实现后，我更加有动力去完成我的项目。下一步也很清楚。这个应用程序需要一个用户界面，以便您也可以检查其他网站。

[![Added UI](img/fc48e4207bc915470ef2ca8c5d50ca5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mu5Iw6dk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nvcegm55xwp05hpu5gbi.png)

### 一半时间🚗

我想大约在开发阶段的一半时间，我开始为我接下来要做的事情制定计划。所以我在 Github 上设置了一个“项目”面板，以便更好地了解我接下来要做的事情。

将下一个更大的目标分解成小部分，并写在看板上，这对我专注于目标帮助很大。这种工作方式非常有益，因为每当你完成一件事，你就可以把卡片移到“完成”状态。

[![Kanban Board](img/02fc040f949a19768687dea8257047ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--80fm_SV0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cxlfb7227j6ulzz9ryp6.png)

在我开发过程的一半时间里，我的 web 应用程序看起来就是这样。你可以看到，这个设计和现在的版本相比没有太大的不同。只是颜色变了。

[![Half time version](img/f01440a5ae5928423bcfeeef9e38fffd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W6WIjdBk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ac6k90ui36gxnchbd1h9.png)

当然，在这个开发阶段，web 应用程序可以比第一个版本做得更多。我实现了一个历史来查看最近 10 个检查过的网站和一些关于检查过的网站本身的附加信息。

### 终点线✈️

在开发过程的这个阶段——谈论日历周 16 和 17——做了很多工作。

我...
...已将用户界面更改为当前版本。
...添加了更多行以获取关于所检查网站的附加信息。
...修改了监控功能。
...添加了关于此 web 应用的内容和使用方法的说明。
...修改了“信息”页面——增加了一个更新日志和一个“联系我”的表单。

我还修复了漏洞。

此时，我坚持下去的动力来自我的❤️.我想让这个项目有一个让我自豪的结局。如果有一个基础可以建立的话，在一个项目上工作会容易得多。你现在所做的一切都会扩展你的网络应用。

* * *

# 部署到数字海洋🤓

我第一次部署我的网络应用是在 4 月 20 日。就在“终点线”周。这意味着，项目工作和部署同时进行。

[https://www.instagram.com/p/BweRz6FhLCN/embed/captioned](https://www.instagram.com/p/BweRz6FhLCN/embed/captioned)