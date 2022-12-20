# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-3jej>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

为了回应 **[Regex 备忘单](https://dev.to/emmawedekind/regex-cheat-sheet-2j2a)** ， [@bkichler](https://dev.to/bkichler) 分享了一条正面评论，并提供了另一个很棒的 Regex 资源的链接:

[![bkichler profile image](img/a30f043030093149ba75bd223187af35.png) ](/bkichler) [ Brian Kichler ](/bkichler) • [<time datetime="2019-02-19T15:16:31Z" class="date-short-year"> Feb 19 '19 </time>](https://dev.to/bkichler/comment/8pel) 

我对 regex 有偏见，但这是一个很好的参考，我肯定会回来看的。每当模式匹配开始吞噬我的大脑时，我几乎总是会去 regex101.com。

看完 **[用 instant.page](https://dev.to/lauragift21/load-pages-instantly-with-instantpage-4epc)** ， [@solkimicreb](https://dev.to/solkimicreb) 非常恭敬地提供了一些额外的上下文和一个建议来回应这个帖子。这种交流对每个参与者都非常有价值:

[![solkimicreb profile image](img/5875d43520fca2537c438310e6e4a895.png) ](/solkimicreb) [ Miklos Bertalan ](/solkimicreb) • [<time datetime="2019-02-20T15:22:39Z" class="date-short-year"> Feb 20 '19 </time> • Edited on <time datetime="2019-02-20T15:25:46Z" class="hidden m:inline-block date-no-year">Feb 20</time>](https://dev.to/solkimicreb/comment/9062) 

如今我经常看到这种回购，我觉得我必须有点反对它。抱歉。😕

TLDR: 这对于一些服务器渲染的静态页面来说很好，但是对于 spa 来说就不那么好了。

预取是一个神奇的工具，但它意味着在特定的上下文用例中有选择地使用。把所有的链接都变成一个预取链接不是一个好主意，如果是这样的话，浏览器开发者会这么做，而不是给我们一个 API。

这确实在悬停时预取页面，但预取页面只涉及获取入口点(index.html)，它不获取辅助资源。许多现代页面都有微小的 index.html 文件和巨大的二级包(JS 和 CSS)。在这种情况下，预取没有影响，服务器推送或 PWA 可以得到更好的利用。

大多数 SPA 路由器在客户端路由中使用“假的”`a`标签(带有阻止的默认事件处理)。这样做是为了结合新派和老派路由的最佳部分——比如保持右键单击“在新标签中打开”的功能。预取这些“假的”`a`标签上的悬停事件将简单地再次获取您的水疗 index.html。这只是添加了零价值的网络流量。

除了链接和整个页面之外，还有更多的东西可以预取。当你添加一个新的资源并决定它是否从预取中获益时，要一直注意。这通常不是很大的开销。

我认为这是一个机智的想法，代码看起来非常干净。也支持提高对新预取能力的认识！

编辑:我是根据我的个人经历写的，没有进入规范。如果我提到什么不正确的地方，请纠正我。

简短而甜蜜， [@nektro](https://dev.to/nektro) 在回复 **[时提供了一个推荐，有哪些你离不开的浏览器插件？](https://dev.to/priteshusadadiya/browser-plugins-that-you-cant-live-without-27l8)** :

[![nektro profile image](img/17f9a26aac31fd56edadf8c06c9ef4db.png) ](/nektro) [ Meghan (she/her) ](/nektro) • [<time datetime="2019-02-22T16:22:14Z" class="date-short-year"> Feb 22 '19 </time> • Edited on <time datetime="2019-02-22T16:54:00Z" class="hidden m:inline-block date-no-year">Feb 22</time>](https://dev.to/nektro/comment/91d0) 

所有浏览器都应该预装 uBlock Origin。

在一篇精彩的文章中， **[你作为一个新的 Web 开发人员可能犯的 6 个错误&如何避免它们](https://dev.to/emmawedekind/6-mistakes-youre-making-as-a-beginner-web-developer--how-to-avoid-them-5gj4)** ， [@hamatti](https://dev.to/hamatti) 选择了他们意见不同的一点。它引发了一场富有成效的讨论，甚至更新了最初的帖子！

[![hamatti profile image](img/543ffd9cf697e6b68ad8b16e0e3fc097.png) ](/hamatti) [ Juha-Matti Santala ](/hamatti) • [<time datetime="2019-02-25T13:32:50Z" class="date-short-year"> Feb 25 '19 </time>](https://dev.to/hamatti/comment/9311) 

好棒的帖子！

然而，我不同意这一点

> 但是大多数初级开发人员犯的最大错误之一是在没有很好地理解 JavaScript 的情况下直接开始学习这些工具。

学习有不同的方法。我从一个非常理论的角度出发，希望在前进之前有一个良好的基础理解。我最终没有做很多东西，因为总是有很多东西要学。

我对任何初学者或初级开发人员的建议只是构建东西。不要担心工具，不要担心让事情变得完美。只是建造东西。当你这么做的时候，你将会学到所有与软件开发相关的东西。这也使得理解 Javascript 的核心内容变得更加容易，因为你有了与这些知识相关的东西。

很长一段时间我试图理解 Javascript 中的`this`怪异之处。最后，在我有了很多在没有完全理解的情况下构建东西的经验，并且直接遇到问题之后，理解它是如何工作的就容易多了。

所以我认为这两种方法对初学者来说都很好，这完全取决于你学习的最佳方式。

最后， [@shoupn](https://dev.to/shoupn) 回答 **[在你开始一份软件开发员/工程师的新工作后，过了多久你才觉得自己很有效率？](https://dev.to/xngwng/after-you-start-a-new-job-as-a-software-developerengineer-how-long-it-took-before-you-feel-you-are-very-productive--fb)** 通过谈论大多数工作中涉及的爬坡时间:

未找到注释

下周见，更多精彩评论，✌