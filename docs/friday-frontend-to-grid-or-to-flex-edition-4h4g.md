# 星期五前端:网格还是灵活版

> 原文：<https://dev.to/kball/friday-frontend-to-grid-or-to-flex-edition-4h4g>

本周的时事通讯以几个有趣的问题开始:什么时候应该使用 Flexbox 还是 CSS Grid？一个着眼于全局差异，而另一个给你一套简单的问题问自己。

还有一些 CSS 技术的快速点击，一个很棒的资源列表，一些 JavaScript 生态系统公告和教程，我们以一个新的调查结束，重点是#StateOfCSS！尽情享受吧！

最佳，

来自 ZenDev 的 KBall

附:对网络观众抱歉，这是在本周邮件发出几天后重新发布的，因为我周四晚上病得很重，现在才恢复正常。

#### CSS&SCS

##### [快！Flexbox 和 Grid 有什么区别？](https://css-tricks.com/quick-whats-the-difference-between-flexbox-and-grid/)

Flexbox 和 CSS Grid 都是非常强大的布局工具，但是如果你还在学习的话，有时候很难知道你应该专注于哪一个。这篇文章很好地解决了这个问题——没有详细说明什么工具适合什么工具，而是说明了这些工具处理问题的方式在概念上和总体上有什么不同。

##### [要网格还是要伸缩？](https://css-irl.info/to-grid-or-to-flex/)

另一个完全相同的问题——事实上，它似乎受到了 twitter 上相同主题的启发。:)当你决定使用 Grid 还是 Flexbox 来解决布局问题时，看看你应该问自己的一些问题。

##### [2019 年哪里学 HTML & CSS？](https://css-tricks.com/where-do-you-learn-html-css-in-2019/)

随着全行业对 JavaScript、JavaScript 以及更多 JavaScript 的如此重视，很高兴看到有人在学习 HTML 和 CSS 的地方收集了这样的资源。我非常同意他们推荐的资源。minor self plug——如果你正在按主题领域寻找 CSS 资源的完整列表，你也可以看看我去年出版的指南。

##### [快速提示:CSS 三角形](https://pineco.de/quick-tip-css-triangles/)

当你需要的时候，用 HTML 和 CSS 创建三角形的四种不同方法。我今天几乎只使用#1，边界技巧，但是我很好奇尝试其他的一些技巧。

##### [文字环绕&行内伪元素](https://www.jayfreestone.com/writing/wrapping-and-inline-pseudo-elements/)

非常酷，看看在链接末尾自动插入图标的挑战。在找到一个(大部分)全球通用的解决方案之前，浏览几种不同的可能方法及其缺点。我很喜欢这不仅展示了技术，而且真的完成了达到它的过程。

#### JavaScript

##### [JavaScript:好玩的部分](https://dev.to/solkimicreb/javascript-the-fun-parts-15cb)

双关语经典的“ [JavaScript:好的部分](https://www.amazon.com/gp/product/0596517742/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0596517742&linkCode=as2&tag=kball00-20&linkId=2b4445bdd53aa44c7e12e08c6c466640)”(有点过时，但仍然是我读过的关于 JavaScript 的最好的书之一)，这是一个非常有趣的关于 JavaScript 的一些新特性以及如何应用它们的观点。

##### [Vue 2.6 发布](https://medium.com/the-vue-point/vue-2-6-released-66aa6c8e785e)

上周，我们发布了一个关于 React 新版本的帖子；看起来 Vue 也刚刚发布了一个新版本，有一些很酷的功能。没有什么像钩子一样具有突破性，但我对新的异步错误处理感到兴奋，如果有人在做服务器端渲染而不使用 Nuxt(它已经处理了这个)，那么`serverPrefetch`钩子将会非常有帮助。

##### [普通人用的 JavaScript 正则表达式](https://alligator.io/js/regular-expressions-for-regular-people/)

正则表达式是那些从外面看起来完全不透明的工具之一，但是一旦你花一点时间就会发现它非常强大。事实上如此强大，以至于他们最终会习惯于各种他们可能不应该习惯的事情。😉但这并不意味着你不应该学习它们——你绝对应该学习。这是一个很好的循序渐进的介绍。它不会让你完全掌握，但它确实以一种容易理解的方式向你介绍了一大堆正则表达式的概念。

##### [useContext():一个 React 钩子这是一个明显的胜利](https://frontarm.com/james-k-nelson/usecontext-react-hook/)

我不得不同意作者的观点，从清晰度和可读性的角度来看，这对钩子来说是一个非常明显的胜利。当使用多个上下文时，深度嵌套的 JSX 组件方法无疑是一种痛苦，而钩子给了你一条清晰的出路。考虑到钩子的可组合性，我认为它也有助于收集常用上下文集合的助手的使用。非常酷！

##### [8.0 版本和常春藤](https://blog.angular.io/a-plan-for-version-8-0-and-ivy-b3318dfc19f7)的计划

长期读者都知道，我对 Angular 的关注不如对 React 和 Vue 这样的框架那么密切，但这非常有趣。我特别喜欢 Angular 能够根据浏览器支持的内容生成不同的包。我认为这是许多框架想要采用的东西；我们有能力根据需要自动拆分和加载代码，而不需要开发人员考虑太多，我们应该为更小的现代 JS 包和向后兼容的 transpiled 包做同样的事情。

#### 提升链接

(注意:我把这个放在推广下，因为它不是我们的标准内容，它是一个附属链接，所以如果你以后从他那里买东西，我会受益，但这个特定的内容是 100%免费的，如果你对在线业务或营销感兴趣，我强烈推荐它)

##### [杰夫·沃克的产品发布蓝图](https://productlaunchformula.com/go?p=kball&w=alum_blueprint)

我上周推荐了第一部分，但它值得再做一次，特别是因为 Jeff 的整套免费培训视频现已可用，包括他的“产品发布蓝图”:这是一个可下载的 18 页 PDF，外加一个完整长度的培训视频，引导您完成蓝图的每一步。如果我必须选择一件事来帮助我超越自由开发，开始建立一个可扩展的业务，那将是杰夫·沃克的产品发布公式。这不会持续很久，但是现在这些培训是免费的:[https://productlaunchformula.com/go?p=kball&w =明矾 _ 蓝图](https://productlaunchformula.com/go?p=kball&w=alum_blueprint)

#### 其他牛逼

##### [Firefox 66 阻止自动播放可听视频和音频](https://hacks.mozilla.org/2019/02/firefox-66-to-block-automatically-playing-audible-video-and-audio/)

谢天谢地！！！没有什么比一个未经允许就开始制造噪音的网站更让我反感的了。特别是因为我每天大部分时间都戴着耳机听音乐，未经授权的音频可能是我最讨厌的。仅此一点就可能让我切换回火狐浏览器(虽然说实话，我喜欢勇敢的 T1)

##### [即将召开的前端设计相关会议&开发](https://conferences.css-tricks.com/)

我最近注意到我非常喜欢参加会议，以及它如何帮助我保持兴奋和了解前端开发。如果你想知道有什么可以去，看看这个由 CSS Tricks 建立的新网站。这么多会议！我希望我有更多的时间和精力去更多。还有一篇很酷的文章是关于[他们如何将](https://css-tricks.com/a-site-for-front-end-development-conferences-built-with-11ty-on-netlify/)构建成一个每天更新的静态应用程序。

##### [界面复制影响决策](https://www.nngroup.com/articles/interface-copy-decision-making/)

在做前端开发或设计时，我们不经常谈论复制…我们只是插入 [Lorem Ipsum](https://www.lipsum.com/) 并继续我们的工作，对吗？也就是说，如果我们关心我们的界面鼓励用户做什么…复制实际上是其中的一个关键部分。这篇文章展示了复制可以推动人们的一些方式，无论是好的还是坏的。

##### [公布#StateOfCSS 2019 调查](https://medium.freecodecamp.org/announcing-the-state-of-css-2019-survey-e1e4268df64d)

我很高兴看到前端开发社区对 CSS 部分有足够的兴趣来进行这样的调查…我鼓励你们都来参与。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】