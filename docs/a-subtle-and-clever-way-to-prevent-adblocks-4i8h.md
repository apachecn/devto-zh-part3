# 防止 adblock 的巧妙方法

> 原文：<https://dev.to/lukasmeine/a-subtle-and-clever-way-to-prevent-adblocks-4i8h>

一些网站对他们的广告拦截政策非常直接。谁以前没见过这样的东西？

[![adblock](img/fcc1165a056cf3d6255e98727124acc1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GEVOWi09--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pcmobitech.com/wp-content/uploads/2016/06/Disable-adblocker-to-view-content-on-this-site.png)

他们中的一些人甚至撰写完整的文章或帮助帖子，描述如何禁用广告拦截软件，以便用户可以继续上网冲浪。(当然是在看到很多广告的同时。)

这是否可以接受有待讨论。有些人完全反对使用网络广告，比如在 pihole 的那些人。有的认为有可以接受的广告，有的认为各种广告都是很好的收入来源。我个人认为，网络广告是一种过时的做法，应该灭绝。

嗯，在过去的几天里，我遇到了一些网站完全崩溃，无法使用。检查开发者工具(习惯的力量，试着调试东西，即使这不是我的工作，哈哈)，我注意到我的广告拦截器破坏了网站。我的第一个想法是:

嘿，也许这些人是初级开发人员，没有考虑到这一点，而且网站所有者没有足够的钱来支付合适的开发人员。

然后我就把我的 adblock 软件停了，继续浏览。但是后来一些想法进入了我的脑海。

如果这是故意的呢？

然后我意识到，我不是第一个高兴地禁用广告拦截器的人，因为网站因此完全停止了工作。就像我的潜意识告诉我，这是我的错，而不是网站的错。然后我问你们:

如果它检测到广告拦截器，而不是仅仅要求用户禁用它，那么故意破坏您的网站可能会更有效？这是一件道德的事情吗？