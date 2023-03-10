# 你可能想知道的 HTML5 web APIs

> 原文：<https://dev.to/christopherkade/html5-web-apis-you-might-want-to-know-4cbm>

几天前，我在我的[博客](https://christopherkade.com/web-apis)上发布了这篇文章，并希望与你分享，以获得一些反馈。我希望你们中的一些人能够学习 API 来升级你们项目的 UX，祝你们愉快！

* * *

我将在明天的 BBL 上展示这个超级有趣的主题，所以我不妨写下它来巩固我的发现。

我们将讨论 HTML5 在过去几年中引入的 Web APIs，这些 API 将允许您用几行代码为您的应用程序添加新的用户体验，并且有很大的潜力。对于每个 API，我将展示如何实现它、它的浏览器支持以及如何在具体项目中使用它们的想法。

## 页面可见性👀

这个函数允许您通过捕获以下事件来了解页面当前对用户是否可见:

[![page-vis](img/a748e1b78278a29b85b5d07d73ba3439.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_BHllaBx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/media-p.slid.es/uploads/9869img/5913262/carbon.png)

如您所见，它实现起来很简单，并且有相当好的浏览器支持:

[![page-vis2](img/dcec845f90306975249f23167aaab60a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---A53sZ-y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/media-p.slid.es/uploads/9869img/5906929/page-visibility-support.png)

这使得它可以在大多数应用中生产。

这是我做的一个小的演示,展示了这个 API 的一个用例:每当你离开当前标签，视频暂停，favicon 改变以反映这一点。

我相信您已经有了一千个关于如何实现这个 API 的想法，但这里是我的两点:

*   当用户被取消时，避免无用的操作
*   当收到新数据但尚未查看时，通过标签的标题通知用户(就像 Messenger 和 Whatsapp 已经做的那样)
*   当用户退出时暂停浏览器游戏

## 环境光💡

这一个暴露了由用户的传感器感知的当前照明水平的数据。

[![light](img/676fa206a4c1b4fae4146ee7b6c1ddbc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G4i5JA2Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/media-p.slid.es/uploads/9869img/5906957/ambient-light-code.png)

如您所见，这些 API 的共同主题是易于实现。你要做的就是抓住`devicelight`事件，通过`e.value`获得[力士](https://en.wikipedia.org/wiki/Lux)的数量！

值得注意的是，这种方式最终会被否决，因为一个通用传感器 API 正在开发中，它将包含大量的传感器数据。

它最终看起来更像这样，您可以实例化传感器，启动它，捕捉其状态的变化，并随时停止它:

[![light](img/d5d389a9ccd671b6f7315886d32d54f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3dqA-Kv4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/media-p.slid.es/uploads/9869img/5907037/ambient-light-code-3.png)

大 bummer tho，浏览器支持不是很好，因为你可以在这里看到

[![light](img/e54e86d90a8e7b796f3562d124d6f076.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gwxEeKig--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/media-p.slid.es/uploads/9869img/5907028/ambient-light-browser.png)

一旦通用传感器 API 正式获得推荐状态，这种情况可能不会持续太久。

以下是如何使用它的一些想法:

*   根据当前照明调整应用程序的对比度(高亮度可能会使某些对比度更难读取)
*   每当用户在晚上阅读时进入黑暗模式(尽管我认为你应该总是处于黑暗模式)😛)

## 在线状态📡

很明显，它告诉我们用户当前是否连接到网络。它还允许我们捕捉如下网络变化:

[![online-state](img/98ac8f98395c64470b79f027c2228ef9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qua_COCG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/media-p.slid.es/uploads/9869img/5917760/carbon.png)

它的支持非常好:

[![online-state](img/543c6751f66518c34a7200368903c494.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ojgC0Sov--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/media-p.slid.es/uploads/9869img/5907114/online-state-support.png)

这里有一个小小的[演示](https://codepen.io/christopherkade/pen/MxXdmY)来展示如何使用它。

使用案例包括:

*   当用户未登录网络时通知用户
*   当用户长时间后重新联机时提取数据

## 振动📳

最后但同样重要的是，这个 API 允许我们让用户的设备振动。

[![vibration](img/4ab2dc1d3994facc5d801223da529741.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9krJ7dbH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/media-p.slid.es/uploads/9869img/5917802/carbon__1_.png)

浏览器对它的支持可以更好，但是它的使用案例几乎都是围绕着手机。正如你所看到的，大多数 Android 浏览器支持这个功能，而 iOS 的 Safari 不支持。

[![vibration](img/f4834c1002694925dee1c0e327169326.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u2pqcR2k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/media-p.slid.es/uploads/9869img/5907174/vibrate-support.png)

这里有另一个[演示](https://codepen.io/christopherkade/pen/eXKPzN)展示它的使用，请确保在兼容的浏览器上尝试。

实施思路包括:

*   在表单输入中加入触觉反馈，例如，当用户输入错误的电子邮件地址时，可以感觉到振动。
*   给手机浏览器游戏增加震动(如果有的话)

## 结论

还有许多其他的 web APIs 可以帮助您构建真正有趣和独特的用户体验。我推荐一个名为 [What Web Can Do Today](https://whatwebcando.today/) 的网站，它展示了所有这些功能的完整列表，所以一定要去看看。

感谢阅读，

[@christo_kade](https://twitter.com/christo_kade)