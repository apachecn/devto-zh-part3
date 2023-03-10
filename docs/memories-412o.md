# 记忆

> 原文：<https://dev.to/fluffy/memories-412o>

关于电子应用程序如何占用大量内存的问题已经写了很多；毕竟，每一个都运行着自己的 web 浏览器实例，并包含了大量的支持代码。Slack 很容易占用 1GB 的内存，而 Discord 通常也会占用几百个内存。作为一个曾经在 90 年代使用 IRC 的人，当一个任务占用 1 MB 内存都被认为是很多的时候，这感觉相当可怕:

[![Slack taking about 1.2GB](img/c7fd86ebe638c2186fb6ec313ba70d52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aSA-hI0f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6g4pzetmwo3dmu8rfjlg.png)
[![Discord taking about 400MB](img/0cc2b0fcdd788a364ea883cd3374e4f1.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--kA3TUp19--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8j0uygw18xslr1xdejqb.png)

在我的 iMac 上，内存为 24GB，这意味着聊天应用程序——相当于一个 IRC 客户端(当然，有更多的视觉内容，但没有*T1]那么多——占用了我大约 6%的内存！*

但是回想一下，在 90 年代中期，当一台典型的计算机有 8MB 时，一个 IRC 客户端可能占用大约 400KB 的 RAM，也就是 6%。那么，事情真的以那种方式成比例地增长了吗？

好吧，我已经找到了一种方法，让这些聊天应用程序占用我总内存的一半，但首先，让我们谈谈我个人的内存使用历史。

* * *

1983 年，我们家有了第一台电脑。这是一台 [Commodore 64](https://en.wikipedia.org/wiki/Commodore_64) ，一台 8 位微型计算机，64KB 内存(没有单独的视频内存)，虽然我们开始使用 Datasette 磁带机，但我们很快升级到了 [1541 磁盘驱动器](https://en.wikipedia.org/wiki/Commodore_1541)，这让我们可以每面存储 170KB(每个磁盘 340KB 尽管需要使用打孔机。

我主要用这台机器来制作艺术和音乐，以及玩游戏。

几年后，我们升级到了 Commodore 128 T1，尽管我们继续使用 1541 磁盘驱动器。它有 128KB 的内存，尽管我主要是在上面运行 C64 应用程序(尽管作为家庭文字处理器，C128 增强版的 Pocket Writer 对我们来说是一个巨大的升级)。

80 年代末，我们在克隆公司买了一台个人电脑。80286，12MHz(我想)，有巨大的内存。这也是我第一次上网的地方；我们有一个用于 C64 的调制解调器，但它只有 300 波特，286 上的 2400 波特调制解调器实际上是有用的。所有的在线访问都是通过拨号帐户，我怀疑调制解调器程序使用了超过 64KB 的内存(因为 DOS)。所以聊天的能力(有效地)使用了大约 6%，算是吧。我们还有一个 40MB 的硬盘。

我主要用这台机器来制作艺术品、玩游戏和上网聊天。(我还是用 C64 做音乐。不是 C128——它的 SID 芯片由于静电事故不幸遭遇了悲惨的结局。)

90 年代初，我们有了 486，内存为 4 MB。它主要运行 Windows，我们通过 AOL 上网。我不知道客户端使用了多少 RAM，但似乎可信的是，它会使用大约 250KB，也就是 6%。我们最终升级到了 8MB(这额外的 4mb SIMMs 在 1994 年的价格大约是 180 美元！).我相信我们从 100 兆硬盘开始，最终升级到 200 兆硬盘。

我主要用这台机器进行艺术创作、玩游戏、制作音乐(Pro Audio Spectrum 16，heck yes)和在线聊天。

当我 1995 年去上大学时，我给自己造了一台 486/100。我全力以赴，给它配备了 16MB 的内存，并让它双启动 Windows 和 Linux。我觉得硬盘是 1.2GB！太神奇了。

我主要用这台机器进行艺术创作、玩游戏、在线聊天(使用 mIRC，如果它使用大约 1MB 的内存，我一点也不会感到惊讶——即 6%)，以及制作音乐。

好吧，那么，生活是向前的。越来越多的计算机出现，存储变得更大，RAM 变得更大。

展望 2017 年，我买了我现在的机器，一台 iMac，内置 2TB 的存储空间，一个 1TB 的外置硬盘，一个...相当大的 NAS。我在*游*于储物能力，它*还是*觉得空间不够。

我主要用这台机器来...制作艺术和音乐，玩游戏，网上聊天。

聊天客户端仍然占用我 6%的内存。

但是等等！在介绍中，我说过我有一种方法可以让聊天客户端只占用我 3%的内存。这很容易解决！

我是否切换到了类似于 [Sblack](https://www.sblack.online) 或 [Ripcord](https://cancel.fm/ripcord/) 的备选客户端之一？嗯，我已经试过了，但是他们的 UX 不太适合我的可访问性需求。

我切换到基于 IRC 的前端了吗？不，那移除了太多有用的功能。

像强迫 macOS 压缩我的 RAM 这种奇特的事情怎么样？再说一次，不！

解决方法比这简单得多...

[![...I upgraded my machine to 48GB.](img/b84637dd371ea015d379bf24098087d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KwnSfsE0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dy5uazy03cjlxca0p8l1.png)

无论如何，希望这能帮助我制作艺术和音乐，玩游戏，和网上聊天。