# 保护 Webapp -步骤 0:简介

> 原文：<https://dev.to/rjjsoftware/securing-a-webapp-step-0-an-introduction-5no>

*这篇文章的封面图片是由[阿尔瓦罗·雷耶斯](https://unsplash.com/photos/qWwpHwip31M)T3 拍摄的*

一段时间以来，我一直有一个制作连续剧的想法

> 一有客户来找我，我就把安全性植入新网站

我甚至在最近的评论中结束了这个想法:

[![dotnetcoreblog profile image](img/8756136f44a0bc1562d9bd8568defb6b.png) ](/dotnetcoreblog) [ Jamie ](/dotnetcoreblog) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/dotNetCoreShow) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/GaProgMan) [<time datetime="2019-05-21T07:39:30Z">May 21 '19</time>](/dotnetcoreblog/comment/b1ed)

我在 [one post](dev.to/dotnetcoreblog/three-steps-for-increasing-the-security-of-your-web-apps-3clg) (在 MageCart 第一次出现之前不久)、[一篇关于 OWASP 的讨论](https://dev.to/dotnetcoreblog/owasp---who-jack)和[它如何会毁掉你的公司或品牌](https://dev.to/rjjsoftware/security-for-your-web-apps---and-why-its-important-1b66)中使用了 Feedify 和二维码作为例子，说明为什么人们应该在过去研究这个问题。尤其是因为这是一个相对容易解决的问题。

我觉得你可以把它做成一系列的帖子。即使作为一个知道这些东西和如何实现它的人，我也喜欢阅读作者从“这是理论”到“这是我的初始计划”，到“这是为什么我的假设是错误的”(因为有了 CSP，它们将会是错误的)，再到“这是我的站点的最终标题”的系列文章。

许多写标题的人倾向于关注第一步和最后一步，但是开发者需要知道中间部分是困难的。也许你可以报道这件事，或者提名某人来做这件事。也许我会为我的一个客户开发一个应用程序来尝试一下😉

那些一直关注我写作的人会知道，我以前也写过这个话题，但总是从更理论的角度出发:

*   [提高网络应用安全性的三个步骤](https://dev.to/dotnetcoreblog/comment/dev.to/dotnetcoreblog/three-steps-for-increasing-the-security-of-your-web-apps-3clg)
*   谁是 OWASP？
*   [网络应用的安全性](https://dev.to/rjjsoftware/security-for-your-web-apps---and-why-its-important-1b66)

但是在这个系列中，我将带你经历整个过程。从没有 webapp 一直到有安全头的 webapp，中间都有。

### 但是为什么呢？

除了我为我创建的每个 webapp 都这样做的事实之外，我注意到几乎没有人谈论这些事情，包括中间步骤——这是当 CSP 仍然不起作用时，或者当营销人员想在没有先与您核实的情况下向网站添加不安全的功能时(这是最有可能的事情)您会感觉到的痛苦。

说真的，你可能会花大部分时间试图弄清楚你所有的 disperate JS 和 frames 是从哪里加载进来的，并且会为为什么要加载这些东西而挠头。这样，单独创建一个 CSP 有助于对您的应用程序进行某种彻底的清理，但是我们将在后面的文章中讨论这个问题。

### 你对这个系列有什么期待？

我想说的是，通过示例，您可以期待在 web 应用程序中实现推荐的安全头的实用建议。

具体如何添加这些头会有所不同，这取决于您使用哪种技术来构建 webapp。所以我不会花太多时间在如何将它们添加到你的应用上(尽管大多数技术都提供了类似的方法)。

我将为我的一个真正的客户开发一个真正的应用程序，并记录这个过程。我还将链接到一个 dev.to 应用程序的特定版本，因为我正在构建它，所以你们都可以看到它正在构建。

(我也会提供源代码)

所以留下来，看看这个空间，你可能会学到一些东西——希望如此。