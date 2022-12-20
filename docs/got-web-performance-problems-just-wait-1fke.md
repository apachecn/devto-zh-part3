# 有网络性能问题吗？等着吧...

> 原文：<https://dev.to/chromiumdev/got-web-performance-problems-just-wait-1fke>

我看到一个好朋友兼同事[真理子](https://twitter.com/kosamari)的推文，关于在一系列低端设备上测试让你真正脚踏实地。

这条推文的背景是，我们正在研究为日常生活在这些设备上的用户构建 Web 开发是什么样的。

[![](img/1d5138995e8ebc7dd92ba4084f4fd401.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4I9QDpix--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://paul.kinlan.img/2019-03-09-got-web-performance-problemsjust-wait.jpeg)

这个团队现在在这个领域做了很多工作，但我花了一天时间建立了一个网站，要让任何东西以哪怕稍微合理的性能水平工作都非常困难——下面是我遇到的一些问题:

*   奇怪的视窗，神秘的 300 毫秒点击延迟(可以解决)。
*   整个屏幕的巨大重画，而且很慢。
*   网络很慢
*   内存受到限制，随后的 GC 会将主线程锁定几秒钟
*   极其缓慢的 JS 执行
*   DOM 操作很慢

对于我构建的许多页面来说，即使在高速 wifi 连接下，页面加载也需要几秒钟，随后的交互也非常慢。从技术层面来看，看到算法和逻辑上的变化(我在所有传统的 web 开发中不会这样做)带来了性能上的巨大改进，这很难，但令人难以置信地满意。

我不确定长期要做什么，我怀疑我们在更发达市场合作的大量开发者会有一种反应‘我不是在为[插入 x 国]的用户建网站’，从高层次来说，很难反驳这种说法。 但我不能忽视这样一个事实，即每年有数以千万计的新用户开始计算，他们将使用这些设备，我们希望网络成为内容和应用的首选平台，以免我们对元平台的崛起感到高兴。

在未来很长一段时间内，我们需要不断提高性能。我们将继续创建工具和指南来帮助开发人员快速加载并拥有流畅的用户界面:)