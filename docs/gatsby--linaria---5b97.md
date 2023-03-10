# 盖茨比+利纳瑞=💜

> 原文：<https://dev.to/lukehglazebrook/gatsby--linaria---5b97>

我最近经历了重建我的个人博客/网站的过程。我的旧网站包含了更多的页面(“主页”和“联系人”)，但我真的不觉得它们给我的体验增加了什么。它们非常轻内容，最终给网站增加了不必要的复杂性。

那个旧网站是在《盖茨比》中建立的，我对到目前为止发布的《盖茨比》的所有三个版本都有很好的体验，所以它看起来是一个显而易见的选择。

我还坚持使用我在旧网站上使用的 CSS-in-JS 库， [emotion](https://emotion.sh) 。同样，这看起来是一个相当简单的选择，因为我上次使用它没有任何问题，而且`emotion`是一个
库，从那以后我在工作中多次使用过。

现在，你可能想知道`linaria`在这方面的作用，因为在这一点上，看起来一切都很顺利。没错，进展很顺利——几周前我在 Twitter 上听说了`linaria`之后，当我重新发现它时，我差不多已经完成了我的网站，正在浏览我的 Github。我对`emotion`的唯一问题是，在加载黑暗主题的新页面时，我遇到了一些小的(不太明显的)性能问题。只有几个框架的 web 页面会以轻量级模式呈现，直到有时间解析样式。虽然这没什么大不了的，但我内心的完美主义者并不高兴，不得不为此做些什么。

我一直想在一个项目中尝试`linaria`，因为风格化 React 组件的 API 与`emotion`非常相似，所以我决定尝试一下。多亏了`gatsby-plugin-linaria`，在 Gatsby 项目中安装和设置`linaria`变得相当简单，所以我创建了一个新的分支并开始重构。

> 在我的例子中，设置并不简单，因为它可能是基于与`gatsby-plugin-typescript`的兼容性问题。`gatsby-plugin-linaria`(截至发稿时)的维护者正在努力寻找问题的解决方案。

关于`linaria`真正吸引我的一点是，所有的样式都是在构建时而不是运行时处理的。当您将构建时风格生成与 Gatsby 出色的内置 SSR(服务器端呈现)放在一起时，这是非常有意义的。当您可以在构建期间提取样式时，为什么要在运行时解析它们呢？

## 那么，迁移有多难呢？

由于几乎相同的 API，我原以为移植会很容易，但结果比我想象的还要容易。在我的新网站的大多数情况下，迁移看起来有点像这样(我不是在开玩笑！):

```
import styled from '@emotion/styled'; // Delete this line
import { styled } from 'linaria/react'; // Add this line 
```

我将在这里添加一点免责声明，您的里程可能会有所不同，但在我的情况下，绝大多数组件不需要从`emotion`到`linaria`的任何更改。

> 目前还没有任何 codemods 可用于自动切换，但
> 有一个[公开的 Github 问题](https://github.com/callstack/linaria/issues/112)正在讨论在未来创建一些的可能性。

## 不能用情感或风格化组件实现构建时间提取吗？

这是你过去在`styled-components`和`emotion`中能够做的事情，但是这两个库背后的团队已经表达了他们想要脱离静态提取并且停止在他们的库中支持它。在 Github/Reddit 上我能找到的帖子中，他们的建议是“使用 Linaria”😁

在阅读了网上的讨论和文档后，两个库背后的团队似乎都想脱离静态抽取。从`emotion`开始的`extractStatic`在版本 10 中已经被[弃用，在`styled components`中没有](https://github.com/emotion-js/emotion/blob/master/docs/extract-static.md)[支持它的目的](https://github.com/styled-components/styled-components/issues/1018)。

这两个非常流行的库背后的团队给出的建议似乎是，如果你想要静态提取，就使用`linaria`!😁

## 结论

总的来说，我很高兴我决定尝试一下。这个项目开始起来非常简单，移植比我预期的更加无缝。

我认为如果你打算用 Gatsby 创建一个静态网站，并且你喜欢 CSS-in-JS，那么它可能是你的完美解决方案。

如果你想问我关于我在这篇文章中所说的任何问题，请随时给我发 DM 或 Tweet:[@ LukeHGlazebrook](https://www.twitter.com/LukeHGlazebrook)

此外，如果你想看到`linaria`在行动中，然后检查我的网站@ [https://glazebrook.dev](https://glazebrook.dev)