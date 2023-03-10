# 修订演示硬件

> 原文：<https://dev.to/davidgs/rev-ing-the-demo-hardware-3a3a>

如果你阅读我的文章有一段时间了，你会知道我做了很多小硬件项目。有什么比构建不断向其写入数据的硬件更好的方式来突出 InfluxDB 的物联网功能呢！但我的一些硬件演示已经变得陈旧，其中一些已经被滥用，所以我决定是时候用一些新的硬件来改造它们了，我甚至打算通过添加 LiPo 电池来使它们完全无线化，以便它们可以在演示和演示期间移动！

我的许多演示都依赖于可靠的(非常便宜的)围绕 ESP-8266 构建的 Wemos D1。当我说非常便宜时，我指的是每个不到 3.00 美元，所以我通常按打购买。但是它们也有问题。首先，它们不是 100%可靠的，它们确实有相对规律性地发生故障。所以我才批量购买！此外，他们天生缺乏安全感。所以我正在转向基于 ESP32 的系统。它们的功能稍微强大一些，而且同样容易构建。我从 Adafruit 订购了一些 ESP-32 羽毛，主要是因为它们带有内置的脂肪电池充电电路。幸运的是，运行在 ESP8266 上的代码在 ESP32 上运行时没有变化，所以至少我不用移植任何东西。

如果你在推特上关注我(如果没有，为什么不呢？！)那么您可能已经看过我最近的 7 段展示，它从一个 MQTT 代理(由 InfluxDB 提供)读取数据。在另一个帖子里有更多的内容！)并显示它。[![IMG 5243](img/b6862a2e02f89ed707b24e597655a3b6.png "IMG\_5243.jpeg")](https://res.cloudinary.com/practicaldev/image/fetch/s--X6xLDxc2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidgs.com/wp-content/uploads/2019/06/IMG_5243.jpeg)

很酷，但是缺少了一些东西。一件事是能够告诉*正在显示什么*！我的意思是，数据是伟大的，但没有上下文，它只是数字。但是如何应对呢？进入 14 段显示器，它可以显示几乎任何字母数字字符，并且具有与 7 段显示器相同的外观和感觉。

[![IMG 5478](img/a780d7c8ef592704ebe6573b5d43daef.png "IMG\_5478.jpeg")](https://res.cloudinary.com/practicaldev/image/fetch/s--YdZ9g-Q4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidgs.com/wp-content/uploads/2019/06/IMG_5478.jpeg)

但是如果你看过我的仪表板，你会注意到那里有很多其他数据，如果能改变显示的内容就好了。

[![Screen Shot 2019 06 04 at 3 14 53 PM](img/fa2e75154e8f46d39a6c6d680ea5f8de.png "Screen Shot 2019-06-04 at 3.14.53 PM.png")](https://res.cloudinary.com/practicaldev/image/fetch/s--xJGihW59--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidgs.com/wp-content/uploads/2019/06/Screen-Shot-2019-06-04-at-3.14.53-PM.png)

我已经使设备能够基于另一个 MQTT 消息改变数据，但是我想要更容易处理的东西。输入触觉按钮。我买了一大堆各种颜色的，还有…

[![IMG 5477](img/ec4830dace7684e88884016f85cc5ea0.png "IMG\_5477.jpeg")](https://res.cloudinary.com/practicaldev/image/fetch/s--B9XhYNGY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidgs.com/wp-content/uploads/2019/06/IMG_5477.jpeg)

现在我们有按钮来改变我们得到的数据！

当然，这意味着我必须重新设计和重新印刷盒子，但这只是一个 9.5 小时的印刷工作。最终的结果将是一个便携式的无线数据显示器，与正在显示的内容相匹配的图例，配有 2500mAh 的 LiPo 电池，以便可以四处传递。为了防止人们丢弃和虐待它。3D 打印的外壳并不像人们想象的那么坚固！

我会在我的推特上发布最终设备的照片，所以你最好[跟我来](https://twitter.com/intent/follow?screen_name=davidgsIoT)！

更新演示硬件的帖子最先出现在[的大卫·g·西蒙斯](https://davidgs.com)上。