# 星期五前端:JavaScript 无处不在版

> 原文：<https://dev.to/kball/friday-frontend-javascript-goes-everywhere-edition-3ob3>

希望你这一周过得愉快！我本周发现的一些文章中最突出的是 JavaScript 是如何渗透到科技世界的方方面面的。关于在 crypto 中使用 JavaScript 和在终端/CLI 应用程序中使用 React 的文章，这确实是通用的(编程)语言。

当然，我们也有非 JavaScript 的东西…一些有趣的 CSS 技术，一个超级酷的“点击复制”CSS 效果库，以及一些关于 UX 的角色和前端开发的许多二分法的想法..尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [哦，制作三角形面包屑丝带的许多方法！](https://css-tricks.com/oh-the-many-ways-to-make-triangular-breadcrumb-ribbons/?utm_source=CSS-Weekly&utm_campaign=Issue-362&utm_medium=email)

围绕同一个例子——创建一个三角形面包屑功能区——的各种 CSS 工具的一个很好的浏览。触及的主题包括 CSS 网格，伪选择器和伪元素，花式边框技巧，剪辑路径，等等

##### [用 flexbox 换到新的一行](https://tobiasahlin.com/blog/flexbox-break-to-new-row/)

利落的手法！使用一些具有 100%伸缩基准的 0 高度(或 0 宽度的列)元素来强制 flexbox 布局中不同点之间的中断。结合 order 属性进行动态切换和播放。我喜欢！

##### [用裁剪路径在 CSS 中创建圆角三角形](https://codyhouse.co/blog/post/css-rounded-triangles-with-clip-path)

我非常习惯于用所谓的“边界黑客”来创建三角形，但是这篇文章着眼于一种通过 CSS 创建三角形的新方法

##### [实现一个模型:CSS 逐步布局](https://dev.to/dceddia/implementing-a-mockup-css-layout-step-by-step-2756-temp-slug-3312552)

这篇文章实际上是针对初级到中级 CSS 的人的，所以如果你是一个专家，你可以跳过它。然而，如果你所在的领域被要求实现一个完整的布局或模型听起来非常吓人，这是一个很好的一步一步指导你如何完成它。

##### [精美简单的点击复制 CSS 效果](https://cssfx.dev/)

一个漂亮的 CSS 效果的集合，可视化显示，如果你喜欢，你可以点击它，立即看到生成它所使用的 HTML & CSS。伟大的灵感和资源。

#### JavaScript

##### [JavaScript 异步并循环等待](https://zellwk.com/blog/async-await-in-loops/)

看看在循环内部使用 async/await 的方法，超级酷。这是一个有很多陷阱的领域，非常值得通读和思考。还有一些很棒的技巧——我喜欢使用`Array.map`将一组`await`电话变成一系列承诺的心理模型。

##### [用 React、GraphQL 和 Relay 构建新 facebook.com](https://developers.facebook.com/videos/2019/building-the-new-facebookcom-with-react-graphql-and-relay/)

我通常不链接到视频，但这一个是超级相关的-重建世界上最常用的 web 应用程序之一，使用尖端工具优化极高的性能。很有意思。我以前根本没有研究过 Relay，但是它看起来超级酷！大约在 4:30 开始进入高科技领域。

##### [用墨水创作出 CLI，反应过来又有点魔力](https://vadimdemedes.com/posts/creating-clis-with-ink-react-and-a-bit-of-magic)

使用 React 创建终端/CLI 应用程序。我对此的第一反应是“咦，为什么？”但是后来通过与 JSParty 上的一些人讨论，我看到了这可以带来难以置信的突破的两大领域。首先是完全终端吸收的应用程序(想想`top`和它的兄弟),用 readline 来控制整个终端感觉像是黑魔法。如果这成为公正的反应，那么进入的门槛将会低得多。另一个是关于可组合性的标准…安装和使用 NPM 的 React 组件很容易，因为接口是如此标准化。传统的 CLI 应用程序不存在这样的标准，因此这可以为创建 CLI 应用程序创建一个更加健壮的可插拔生态系统。

##### [JavaScript 开发者的密码入门](https://medium.com/the-challenge/the-javascript-developers-intro-to-crypto-3dcd7839f2e7)

我倾向于对加密持怀疑态度……对于很多应用程序来说，区块链并不是好的解决方案，很多宣传都被夸大了。也就是说，仍然有一些非常有趣的东西，不可否认这是一个热门话题。本文展示了开始使用您最喜欢的 web 技术是多么容易。

##### [交叉点观察器的一些功能用途，用于了解元素何时在视图中](https://css-tricks.com/a-few-functional-uses-for-intersection-observer-to-know-when-an-element-is-in-view/)

浏览器中有了新的 API 和功能后，通常需要一点时间来确定它们在何时何地有用。本文针对交叉点观察器 API 研究了几个这样的案例。第一个(延迟加载图像)是我以前见过的一个，但它仍然令人印象深刻的是，现在有了这个 API，这是多么简单。其他的例子我没想过，但是很有意义。

#### 提升链接

##### [Udemy 母亲节储蓄](http://click.linksynergy.com/fs-bin/click?id=hIdOL5Z4eK4&offerid=507388.10137&type=3&subid=0)

似乎 Udemy 每隔一个月就有一次促销，但我仍然惊讶于你只需 11.99 美元就能买到这么多令人惊叹的课程。如果你一直在坚持，或者打算坚持，但还没有，这是另一个机会。提醒一下，我推荐的一些顶级课程有:[这个 Vue 课程](https://click.linksynergy.com/deeplink?id=hIdOL5Z4eK4&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Fvuejs-2-the-complete-guide%2F)，[这个 React 课程](https://click.linksynergy.com/deeplink?id=hIdOL5Z4eK4&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Freact-redux%2F)，还有[这个 CSS 课程](https://click.linksynergy.com/deeplink?id=hIdOL5Z4eK4&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Fcss-the-complete-guide-incl-flexbox-grid-sass%2F)。

#### 其他牛气

##### [UX 之地](https://m.signalvnoise.com/the-place-of-ux/)

这是一篇关于用户体验以及它在产品和组织中的角色的有趣的思考文章。我喜欢“UX”这个概念，它位于不同事物的供给和需求之间的界限上，这一概念在多个层面上体现出来。

##### [为不同的上下文制作 Web 组件](https://css-tricks.com/making-web-components-for-different-contexts/)

我看到的大多数关于 Web 组件的文章都在谈论它们如何工作以及如何构建它们。这是我第一次真正看到它们在整个生态系统中的作用，并思考我们可以和应该如何使用它们。如果你也需要的话，它还链接到一些关于“如何做”的很棒的资源。精彩！

##### [微软正在其新的 Chromium Edge 中内置 ie 浏览器，增加新功能](https://www.theverge.com/2019/5/6/18527875/microsoft-chromium-edge-new-features-build-2019)

这真是太棒了。尽管微软希望 ie 浏览器寿终正寝，但它仍被长期使用的原因之一是老旧、无人维护、但任务关键的内部业务应用程序只能在 IE 上运行。所以企业会强迫员工继续使用它。但有了这个，他们将能够使用新的现代浏览器，为那些旧的遗留怪物嵌入 IE，并使用 Chromium 做其他事情。赢赢！

##### [分裂](https://adactio.com/journal/15050)

对现代前端开发核心的许多二分法和权衡的有趣探索。看看开发人员的复杂性、性能预算、把关等等。

##### [框架的游戏:2019 JavaScript 趋势](https://cvcompiler.com/blog/game-of-frameworks-javascript-trends-of-2019/?ref=fridayfrontend)

(偏见提醒:本文引用我的话)。根据对招聘信息的分析，有趣地观察了雇主寻求什么样的技能和框架的趋势。我认为有一些通用的“关于所有开发者的帖子”项目，但仍然是有趣的市场需求。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】