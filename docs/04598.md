# 星期五前端:感恩版

> 原文：<https://dev.to/kball/friday-frontend-gratitude-edition-327n>

我想以一句衷心的感谢开始本周的时事通讯！你们中的许多人在本周早些时候回答了我关于信息、建议和对新技术领导者的问题。我正在汇编结果，并期待着发布一些伟大的信息。但我只想说谢谢你；我很感激你一直以来对我和这份时事通讯的关注。

总之，继续本周的内容。我们已经有了另一套整洁的 CSS 技巧，以及涵盖广泛样本的大量手册。有一系列不错的框架和纯 JS 文章，一些有趣的更新，以及一篇发人深省的文章，讲述了命名对于可访问性是多么重要。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

附言:如果你不在邮件列表上，或者错过了本周早些时候的通知，但你有关于技术领导的重要技能的建议，或者关于成为技术领导的问题，现在还不算太晚！请留下你的评论或给我发电子邮件，告诉我你最喜欢的两条建议或问题。

#### CSS&SCS

##### [挖掘到显示属性:框生成](https://www.smashingmagazine.com/2019/05/display-box-generation/)

查看`display:none`和`display: contents`如何影响元素的盒生成。这个`display:none`案例相对来说比较琐碎，我知道一个，但是`display: contents`很吸引人。我喜欢在基于网格或 flex 的布局中嵌入孩子时维护语义嵌套的潜在用例(一旦浏览器错误被消除)。

##### [如何在纯 CSS 中构建过滤组件](https://webdesign.tutsplus.com/tutorials/how-to-build-a-filtering-component-in-pure-css--cms-33111)

使用`:checked` pseudoselector 和一系列同级选择器构建过滤组件的有趣练习，不需要一点 JavaScript。

##### [相对尺寸用 EM 单位](https://andy-bell.design/wrote/relative-sizing-with-em-units/)

使用`em`和`rem`调整的好处的一些简单明了的例子。由于我总是需要查找(文章没有明确说明这一点，而是更多地关注好处)，`em`的大小是相对于本地字体大小的，而`rem`的大小是相对于根字体大小的。

##### [CSS 中对角容器](https://codyhouse.co/blog/post/css-diagonal-containers)

一个很酷的技巧，只使用 CSS 就可以修剪出一个倾斜的容器。

##### [《CSS 手册:开发人员使用 CSS 的便捷指南》](https://medium.freecodecamp.org/the-css-handbook-a-handy-guide-to-css-for-developers-b56695917d11)

哇哦。这是巨大的。一本全方位的指南，让你了解大部分的 CSS。每个主题领域不会太深入，但会给你一个很好的概述，然后你就知道如果你想了解更多，可以去谷歌搜索。

#### JavaScript

##### [用 Auth0 保护盖茨比](https://auth0.com/blog/securing-gatsby-with-auth0/)

我通常不包括供应商关于使用他们自己产品的博客帖子，这是在 Auth0 博客上，但我在这种情况下破例，因为这个问题——如何在 Gatsby 站点中创建认证屏蔽区——在我最近关于 Gatsby 的 JSParty 文章之后出现了很多。虽然这个例子的细节使用 Auth0，但是它们通过展示如何在 Gatsby 中创建一个门控客户端专用区域来开始整个事情，然后您可以将它与任何种类的 Auth 解决方案一起使用。

##### [如何在 JavaScript 中使用 Getters 和 Setters】](https://www.developerdrive.com/2019/04/javascript-getters-setters/)

Getters 和 setters 是一个非常强大的模式，围绕它们的内置语言特性得到了广泛的支持。这个工具允许您基于看起来正常的赋值创建各种各样的“自动”反应… Vue.js 利用 getters 和 setters 来实现“反应”，其中模板和计算的数据自动对数据变化做出反应，还有许多其他潜在的用例。

##### [了解 Vue JS 中的 mixin](https://blog.bitsrc.io/understanding-mixins-in-vue-js-bdcf9e02a7c1)

Mixins 是 Vue.js 区别于 React 的关键特性之一。它们提供了一个工具，可以重用绑定到组件状态的逻辑，这可能非常有用。也就是说，结合 mixins 也很容易搬起石头砸自己的脚(这是我对 [hooks 来到 Vue](https://css-tricks.com/what-hooks-mean-for-vue/) 感到兴奋的原因之一)，所以理解它们是如何工作的是关键。这篇文章给你详细的介绍。

##### [了解变异观察器 API](https://www.smashingmagazine.com/2019/04/mutationobserver-api-guide/)

我认为变异观察器非常酷——它们让您可以在 JavaScript 中观察，并知道 DOM 的某个部分何时被修改。有用吗？我想到的主要用例是当你创建可重复使用的容器时，这些容器需要根据它们的内容表现出不同的行为……但是我确信还有其他的用途。有什么想法吗？

##### [苗条 3:反思反应能力](https://svelte.dev/blog/svelte-3-rethinking-reactivity)

这很有意思。我一直没有太关注苗条，但它出现在最近一集的更新日志中，我开始看到越来越多关于它的东西。将尽可能多的代码转移到编译时步骤的想法让人想起静态站点生成器，我们可以通过编译时优化获得“无成本”抽象的想法让我想起了 Rust 生态系统中正在发生的许多事情。我很想知道它会走向何方。

#### 提升链接

##### [最后一天:11.99 美元的 Udemy 课程](http://click.linksynergy.com/fs-bin/click?id=hIdOL5Z4eK4&offerid=507388.10135&type=3&subid=0)

Udemy 正在进行另一次销售，据我所知，现在只剩下一天了。庆祝新的一个月，今天就给自己一个承诺，开始新的课程。

#### 其他牛气

##### [7 个萌 web 开发招数](https://areknawo.com/7-adorable-web-development-tricks/)

一套使用 JavaScript 和 CSS 的小技巧。有些很有趣，但基本上没用，而有些我每天都在用。值得一看。

##### [为什么网页速度分数会很危险](https://www.machmetrics.com/speed-blog/why-page-speed-scores-can-be-dangerous/)

最近，人们越来越重视 JavaScript 的成本和提高 web 性能，我认为这总体上是一件非常好的事情，但这篇文章强调了过于关注页面速度分数的一些危险。把你的眼睛放在奖品上:一个快速和可用的网站，让工具成为一个向导而不是目标。

##### [命名事物提高可达性](https://hiddedevries.nl/en/blog/2019-04-18-naming-things-to-improve-accessibility)

这是一本很好的读物，讲述了你给事物命名的方式是如何影响它们的易接近性的。老实说，我从来没有意识到拥有“阅读更多”链接对使用辅助技术浏览网站的人的影响，以及包含一些上下文的价值。“继续阅读[标题]”可能更冗长，但是突然之间上下文就在链接中了&非可视化浏览器很容易理解。

##### [用 React 建一个 AMP 网站& Next.js](https://reacttricks.com/building-an-amp-website-with-react-and-next/)

有趣的是，像 Next.js 这样的高级框架正致力于简化使用现代 JavaScript 框架构建 AMP 页面的工作，无论是独占还是与整个页面并行。

##### [设计师更好决策指南](https://www.smashingmagazine.com/2019/04/designers-guide-better-decisions/)

上下文可能是设计，但四个决策陷阱是通用的。我们都深受其害。本文中描述的减轻这些问题的技术和启发非常有用。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】