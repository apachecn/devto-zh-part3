# 谷歌很慢

> 原文：<https://dev.to/xowap/google-is-slow-3i0h>

不是我说的，是谷歌说的。

但首先让我们回顾一下互联网的一些基本规则:

> 你不应该制作速度慢的网站

— *王志恒与多安塔姆潘之书[2018:1:17](https://webmasters.googleblog.com/2018/01/using-page-speed-in-mobile-search.html)T3】*

这当然并不令人惊讶，因为谷歌[让](https://developers.google.com/amp/) [无休止](https://play.google.com/store/apps/details?id=com.android.chrome&hl=en) [陈述](https://developers.google.com/speed/) [关于](https://www.youtube.com/watch?v=aXJklICrFJI) [速度](https://dev.to/nickytonline/google-to-pay-javascript-frameworks-to-implement-performance-first-code-3f57)。

## 谷歌的页面速度

谷歌真好，他们提供工具帮助我们更快地建立网站。他们肯定在带路。让我们来看看。

从简单开始，上面引用的博客文章说速度会影响你的搜索引擎优化，你怎么看？就是一篇博文，只有文字和几个框。如今，Webpack 会优化你的代码和资产，而你不用动一根手指，所以在这种网站上，几乎不可能得到低于 95%的正确率。

[![78/100](img/c7097edc54362dbf7d9e76baed7fcc19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KD1tUQjQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/um9rdr0r0okp6s8c52o0.png)

好的等等。但这是个老网站。他们当时不一定知道。让我们看看别的东西。当然，当他们为 Google Lighthouse 创建网站时，他们已经考虑到了所有的工具和优化。

[![49/100](img/5c2619fa9209c12974e55241dbaa0ef3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1BYfkQmI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lm8nsp0v9ftwspk6snmm.png)

该死的。这肯定是有原因的。网页上有很多内容，在某些时候你需要做出牺牲。我们能看看一个有代表性的“内容丰富”的网站吗？维基百科看起来非常适合这种比较。它充满了旧的 PHP 代码，可能会从各个方面臃肿。

[![94/100](img/5af8d5fdb363d30995512c98e530e836.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2TF7jPFv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tjalehqm62okhfwjhf8u.png)

显然不是。维基百科完全是绿色的。如果你看看这些警告，大多数看起来都是完全有意识的决定。

虽然速度对谷歌来说听起来很重要，但看看他们的网站就会发现，他们远没有达到自己设定的标准。我甚至没有谈论如何 [AMP 网页只有 83%](https://developers.google.com/speed/pagespeed/insights/?url=https%3A%2F%2Fdevelopers.google.com%2Famp%2F) 也没有谈论如何[google.com 有 70 毫秒的输入延迟](https://developers.google.com/speed/pagespeed/insights/?url=https%3A%2F%2Fwww.google.com%2F)。

## 谷歌的嵌入

当然灯塔只是工具之一。谷歌还提供了许多其他工具，如谷歌分析、YouTube 或谷歌字体。由于它们注定是供外部使用的，而且确实被广泛使用，你唯一能期望的就是它们能提供最先进的体验。

为了验证这一点，我创建了几个基准页面。

*   [一个基线](https://xowap.github.io/google-bench/plain.html)，它只包含一个`h1`
*   该页面的谷歌分析版本
*   一个谷歌字体版本
*   对于视频
    *   [YouTube](https://xowap.github.io/google-bench/youtube.html) 版本
    *   [Plyr](https://xowap.github.io/google-bench/plyr.html) 版看看另一个视频播放器表现如何

你可以在 PageSpeed 中运行所有这些软件，你会发现无论是谷歌分析(T0)还是谷歌字体(T2)都不会影响你网站的速度。虽然在报告的底部有一些警告，但它们实际上似乎不会影响分数。

另一方面，如果你用 YouTube 运行它...这个页面的分数**下降到 75%** ！YouTube 正在扼杀你的网页。这是视频播放所固有的吗？这就是为什么我也[让 Plyr](https://developers.google.com/speed/pagespeed/insights/?url=https%3A%2F%2Fxowap.github.io%2Fgoogle-bench%2Fplyr.html) 坐冷板凳，它仍然得到 99%的分数。

## 主要学习内容

谷歌一直非常强调速度，但你可以看到双重标准正在出现。

*   能卖广告的东西非常非常快。Google Analytics/gtag embed 正好说明了这一点。
*   关键产品，如谷歌本身，保持相当快，而你可以看到一些妥协，影响性能，在一个不明显的方式。
*   但其他产品，如开发者网站或 YouTube 播放器的性能得分很低。

我们还表明，虽然这些次级产品表现不佳，但有一些替代品可以显示更高的分数。

从远处看，谷歌似乎很强大，但他们似乎无法在对我们普通人来说很重要的几个领域做出高质量的工作。这可能有原因，但我真的不关心他们:他们采取的立场太强硬了，不能以借口离开。

最后，我会从这项研究中记住的是:

1.  不要轻信谷歌虚伪的废话
2.  相信自己，比谷歌做得更好真的没那么难💪