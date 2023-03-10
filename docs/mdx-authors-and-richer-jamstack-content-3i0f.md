# MDX、作者和更丰富的 JAMstack 内容

> 原文：<https://dev.to/orbit/mdx-authors-and-richer-jamstack-content-3i0f>

由于 JAMstack 的增长，开发人员可以像以前一样快速地构建高性能和安全的网站。内容创作者也从这种复兴中受益，但与此同时，许多作者正在达到 markdown 作为创作工具的局限性。

好消息是现在有了更好的方法:一种叫做 MDX 的新格式，它使内容创作者能够在他们的 markdown 中嵌入动态组件。

在第三届 [JAMstack Paris meetup](https://jamstack.paris) 上，我谈到了开发人员如何通过 MDX 增强其创作者的能力。非常感谢来自 [Theodo](https://www.theodo.fr/) 的 [Nicolas Goutay](https://twitter.com/phacks) 和 [Matthieu Auger](https://twitter.com/matthieuauger) 组织这次聚会，感谢 [Algolia](https://algolia.com/) 招待我们。

[![MDX, authors and richer JAMstack content by Josh Dzielak](img/10d507f4e2943b6f640c7dd1c9471206.png)](https://mdx-talk.developermode.com/)

MDX 是一种相对较新的文件格式，刚刚在四月份由 T2 发布。

**MDX 使 JSX 组件可用于降价**。这使得开发人员可以创建丰富的交互式组件，比如表单、投票、图表、布局等等，作者可以将这些组件优雅地嵌入到他们的内容中。

下面是一个`.mdx`文件的样子，使用嵌入一个问题&答案投票和正常降价内容的例子:

```
# Hello, *world*!

This is just markdown here. The JSX is below.

<Poll>
  <Question>What is your favorite language?</Question>
  <Answer>English</Answer>
  <Answer>French</Answer>
  <Answer>JavaScript</Answer>
</Poll>

Markdown continues down here. 
```

Enter fullscreen mode Exit fullscreen mode

JSX 语法像 HTML 一样是基于标签的，但是抽象层次更高，开发者可以选择向作者提供什么 JSX 组件。

开发者可以(也应该)以一种让非技术作者也能更容易使用的方式来设计组件。

MDX 在使 JAMstack 更具可创作性方面有很多早期的承诺，这是 MDX 创建者的既定目标:

> “我所说的 **authorable** 是指一种跨职能团队的所有成员经过一定的学习(从工程到营销)后可以自然编写的格式。”— MDX 作者[约翰·奥坦德](https://twitter.com/john0)

演讲的视频即将发布，但现在你可以看到[演讲幻灯片](https://mdx-talk.developermode.com/)以及[演讲源代码](https://github.com/developermode/mdx-talk)，它本身是使用一个叫做 [mdx-deck](https://github.com/jxnblk/mdx-deck) 的优秀幻灯片制作开源项目用 MDX 编写的。