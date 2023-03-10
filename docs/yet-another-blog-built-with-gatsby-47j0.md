# 又一个与盖茨比一起建立的博客。

> 原文：<https://dev.to/doppelganger9/yet-another-blog-built-with-gatsby-47j0>

*注意:这应该是我的第一篇帖子，所以算是一个“试点”帖子。*

*(最早发布于[https://lacourt.dev/2019/03/20/](https://lacourt.dev/2019/03/20/)，也就是本文提到的博客)*

## 什么是

[![Gatsby](img/74bc6229493a736559e3ebcc94a7f2ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hqM-MV2d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/21834/35175472-11db4c98-fd74-11e7-9886-7e4e418c3f90.png)

> Gatsby 是一个基于 React 的免费开源框架，它可以帮助开发者构建速度极快的网站和应用

### GraphQL

> GraphQL 是 API 的查询语言，也是用现有数据完成这些查询的运行时

[GraphQL](https://graphql.org/) 这里用于从不同的来源获取数据，这允许聚合来自无数来源的内容:

*   本地“经典”反应 JS 视图，
*   Web 服务/ REST 端点
*   数据库ˌ资料库
*   降价文件
*   ...因为它是可扩展的！

### 首发

Gatsby 附带了很多核心团队和社区提供的入门模板。这真的很方便，也是我选择盖茨比的原因之一:快速建立我的网站原型。

为了开始这个博客，我选择了我在官方市场上找到的第一个首发。
这是《盖茨比》的首发博客。

[![Doge](img/e7dc6c0021cd36aa4a647a152665a2c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B2Fv5vCu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lacourt.dev/static/d3b01d94cc0d1706ff8272aed131a7a6/adc74/doge.jpg)

这是开源的梦想！你可以获得大量免费共享的东西，你只需要组装和定制它，让它成为你自己的。您仍然可以研究所有的代码，并以不同的方式为其他人改进代码，反过来，其他人也会从中受益。

所以我有了这个软件，[非常好的文档](https://www.gatsbyjs.org/docs/)和[教程](https://www.gatsbyjs.org/tutorial/)，[例子](https://www.gatsbyjs.org/showcase/)，[很多可供选择的插件](https://www.gatsbyjs.org/plugins/)，例如:

*   我想在标题上添加内部链接(锚)，有一个插件
*   我想显示每个帖子的阅读时间，有一个插件
*   我想使用有趣的图标，有一个插件。
*   等等。

> 功能插件、源插件和转换器插件

所有这些让我有机会用插件抽象快速添加功能，而不需要我完全理解它们是如何工作的。如果我找不到满足特定需求的插件，我可以创建自己的插件。只是找到现有的节省了我很多时间。这样我就可以专注于写帖子这个“生意”了！

* * *

现在开始写内容了，感觉思想是自己形成的。这是一种冥想练习，你可以把想法释放出来。然后是“重构”,你重新组织一切，使之更加线性，并表达一些目标，无论是讲故事还是描述产品还是其他什么。而不是头脑风暴。喜欢这个帖子。🤣🤷‍♂️
当我写作的时候，通常它会引发另一个想法，我会有抓住它的冲动，并在它飞走之前用文字把它刻在屏幕上。比如捕捉转瞬即逝的想法。然后他们就走了。给别人让路。
这也是一种宣泄方式，你头脑中一些顽固的想法可以在一篇文章中保留下来。当你反复修改它们时，它们呈现出闪亮的品质，给你机会让第二个大脑记忆变得更好。我对书籍和这些书的作者的心态有了新的认识。
心神逶迤...冲洗精神厕所。
现在回到技术层面，准备好了吗？
*打嗝*

* * *

Starter 已经提供了一个很棒的插件列表。
我不会描述如何开始，我关注了[一个已经很好地做到这一点的帖子](https://daveceddia.com/start-blog-gatsby-netlify/)(顺便感谢！).

### 盖茨比-变形金刚-备注

这个是用来把你的 markdown 转化成博客文章的浏览量，

*   “支持一个 [RSS 提要](https://lacourt.dev/rss.xml)”？你可以将我的博客添加到你的 RSS 阅读器中...等待...这不是 2009 年吗？！
*   “对代码语法高亮显示的支持”:好吧，你可能读过我以前的一些帖子，里面的代码并没有全部被**高亮显示**，仅仅是显示出来。我可能漏掉了一些东西。
*   “包括开箱即用的离线支持插件”:这很好，[这个博客是一个 PWA](https://dev.to/future/this-blog-is-100-PWA) 你可以安装它，它将下载所有内容，你可以在离线时阅读文章。此外，由于服务人员的帮助，第一次访问该网站的速度会更快！

### 感慨

现在我完全投入了，我有点后悔我最初选择的首发。 *Lumen starter 我正看着你，你带着这个性感的侧边栏看起来真不错(尽管我的个人资料照片是一张非潮人的脸，那可能不太好)*
也许我可以[迁移到另一个 starter](https://dev.to/alternate-reality/migrating-to-lumen-starter) ？

## 其他选择

其他的可能性是使用:

*   如果你喜欢 Ruby，
*   如果你使用 PHP 或者 33%选择了它的网站中的一部分，
*   雨果，如果你喜欢围棋，
*   [GitBook](https://www.gitbook.com/) ,
*   [下一个](https://nextjs.org/)，另一个来自[时代](https://zeit.co/)的反应，
*   [Nuxt](https://nuxtjs.org/) ，使用 [VueJS](https://vuejs.org/) ，
*   ...[还有很多呢！](https://www.staticgen.com/)

## 拥有自己的内容

> 永远拥有您自己的内容

在某种程度上， [lacourt.dev](https://lacourt.dev) 上的每一篇帖子都是我自己的内容，但由于[一切都在麻省理工学院许可下](https://github.com/doppelganger9/blog/blob/master/LICENSE)，这也可以是你的或任何人的。
一些网站甚至可以使用爬行机器人来找到它，并在他们的平台上发布来赚钱。
所以我未来的目标是找到最好的方式[把我的帖子推给其他平台](https://dev.to/future/publishing-all-the-things) (dev.to？中等？其他？)来传播消息，但是把所有人都带回来。主要是因为我的大多数帖子都在解释我是如何构建他们所依赖的基础设施的。

## 安全

盖茨比是一个[静态站点生成器](https://www.staticgen.com/gatsby)。顾名思义，当部署在活动服务器上时，SSR 的安全性问题更少，因为一切都是静态的。你不会暴露你的 API 键，API 调用，或其他任何东西，所有的内容都已经包含在页面中。不要把你的钥匙放在 GitHub 上，你会没事的👌🏼

## 为什么 SSG 很适合

我将是这个博客的主要贡献者和开发者。也许你会有所贡献，那将是令人敬畏的！但是我仍然是核心维护者。该网站是一个博客。这个博客不会有很多不同的页面。我不需要一个成熟的 CMS。所以像 Gatsby 这样的静态站点生成器非常适合我的用例。

还有，[静态网站的表现是💯](https://dev.to/future/this-blog-is-100-PWA)。

而且我可以在一个免费服务上托管一个简单愚蠢的静态网站，不需要一个带数据库的大服务器等等。所以我的维护成本更低💸

另见[本文](https://hackernoon.com/why-migrate-from-wordpress-to-a-static-site-generator-c9d46bd24710)。

## 结论

在这篇文章中，我想解释这个博客的技术选择。如果不清楚，好吧，也许我不应该在早上 5 点写它，或者你可以帮我校对🤣

不管怎样，我在路上学到了很多，结果对这个博客很满意。

我希望你也学到了一些东西。

> 感谢阅读这篇文章，如果你有任何问题，请使用下面的评论，或使用 Twitter:我的 DMs 是开放的。

👋