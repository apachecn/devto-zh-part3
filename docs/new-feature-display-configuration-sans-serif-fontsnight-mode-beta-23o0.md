# 新功能:个人配置(无衬线字体/夜间模式测试版)

> 原文：<https://dev.to/devteam/new-feature-display-configuration-sans-serif-fontsnight-mode-beta-23o0>

更新:夜间模式进展顺利，看起来棒极了！我第一次写这篇文章的时候，时间还不算太久。

我们刚刚发布了一个新功能:显示定制。

它可以在您的[/设置/杂项](https://dev.to/settings/misc)页面中找到。

两个可用的配置是“主题”和“字体”(用于文章正文)。这些是个人用户可以进行的定制，以实现他们喜欢的体验，而不必依赖于浏览器扩展或其他非本机黑客。每个配置都有一个可用的非默认配置。在未来，也许会有更多的变化。

当您登录时，此配置自然会在所有设备上运行。

个人偏好和可访问性问题都在起作用，为每个人提供更好的选择令人兴奋。衬线字体对于有阅读障碍的人来说尤其困难。

更多信息请点击此处:

[![lkopacz](img/cd7d14f0da8b5733c447c96ff9f5a2f6.png)](/lkopacz) [## 和我一起了解网络阅读障碍！

### 林赛·科帕奇 11 月 14 日 185 分钟阅读

#a11y #discuss #content](/lkopacz/learn-about-dyslexia-for-the-web-with-me-1b9n)

就色彩搭配而言，夜晚主题仍是一项进行中的工作。所以你现在看到的是一种暂时的实现，但我认为它将启动向全面主题的进展。来自社区的第 20 位做了一件了不起的工作，引领 CSS 主题成为可能。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)【WIP】可主题化的 CSS 变量 #1377](https://github.com/thepracticaldev/dev.to/issues/1377) 

[![Link2Twenty avatar](img/1ba74938c13a54803539141f2e9d13e2.png)](https://github.com/Link2Twenty) **[Link2Twenty](https://github.com/Link2Twenty)** posted on [<time datetime="2018-12-20T16:45:05Z">Dec 20, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/1377)

我认为我们应该有一个地方来记录当前的 CSS 变量，并提出应该包含的不同变量。

**目前包含:**

| 可变的 | 描述 | 默认 |
| --- | :-- | :-: |
| `--theme-background` | 主体的背景颜色 | `#f9f9fa` |
| `--theme-color` | 正文的文本颜色 | `#0a0a0a` |
| `--theme-secondary-color` | 正文的辅助文本颜色 | `#557de8` |
| `--theme-top-bar-background` | 顶栏的背景色 | `#fdf9f3` |
| `--theme-top-bar-color` | 顶栏的文本和图标颜色 | `#0a0a0a` |
| `--theme-top-bar-search-background` | 顶部栏中搜索框的背景颜色 | `#e8e7e7` |
| `--theme-top-bar-search-color` | 顶部栏中搜索框及其占位符的文本颜色 | `#0a0a0a` |
| `--theme-top-bar-write-background` | 顶栏中“写入”按钮的背景色 | `#66e2d5` |
| `--theme-top-bar-write-color` | 顶栏中“写入”按钮的文本和边框颜色 | `#0a0a0a` |
| `--theme-container-box-shadow` | 文章和导航元素的框阴影设置 | `3px 3px 0px #bababa` |
| `--theme-container-border` | 文章和导航元素的边框设置 | `1px solid #d6d6d6` |
| `--theme-container-background` | 文章和导航元素的背景颜色 | `#ffffff` |
| `--theme-container-background-hover` | 导航元素的悬停背景 | `#f5f6f7` |
| `--theme-container-color` | 文章和导航元素的文本颜色 | `#0a0a0a` |

**拉取请求待定:**

| 可变的 | 描述 | 默认 |
| --- | :-- | :-: |
|  |  |  |

**建议:**

| 可变的 | 描述 | 默认 |
| --- | :-- | :-: |
|  |  |  |

我认为最好的办法是在下面留下进一步建议的评论，我会更新主帖。

**测试主题:**

黑暗:

```
:root {
    --theme-background: #161f2b;
    --theme-color: #fff;
    --theme-secondary-color: #cedae2;
    --theme-top-bar-background: #141d26;
    --theme-top-bar-color: #fff;
    --theme-top-bar-search-background: #424a54;
    --theme-top-bar-search-color: #fff;
    --theme-top-bar-write-background: #00af81;
    --theme-top-bar-write-color: #fff;
    --theme-container-background: #27374c;
    --theme-container-background-hover: #37475c;
    --theme-container-color: #fff;
    --theme-container-box-shadow: none;
    --theme-container-border: 1px solid #141d26;
}
```

Enter fullscreen mode Exit fullscreen mode

此外，请随时提出拉取请求，以帮助推广这些服务。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1377)

你可能会发现你实际上并不想使用夜间模式，直到主题有了更多的进展。我认为字体定制会更有价值。

CSS 主题方法的精彩阅读:

[![link2twenty](img/658801eb0300e3b6e98dde9a1fc0aa97.png)](/link2twenty) [## CSS 的未来:配色方案

### 安德鲁·伯恩 12 月 20 日 183 分钟阅读

#css #future #darkmode #design](/link2twenty/future-of-css-color-scheme-10of)

再次，转到这里进行这个更改:[/设置/杂项](https://dev.to/settings/misc)。

我们很久以前就考虑过普遍改为无衬线字体，但是没有人同意什么是最好的。这种选择由作者决定实际上没有意义——这实际上是读者的个人品味问题。

下面是老帖子反复思考的话题:

[![ben](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## 考虑将文章正文改为无衬线字体

### 本哈尔彭 10 月 18 日 171 分钟阅读

#discuss #meta](/ben/considering-changing-to-sans-serif-for-post-body-8f5)

关于实现:我们不为来自原始服务器的大多数请求提供服务，而是为不了解用户上下文的缓存页面提供服务。由于这个约束，单个配置存储在`localstorage`中，并在呈现时修改页面。我试图通过 serviceworkers 使事情变得过于复杂，这可能仍然是一个长期的解决方案，但目前这在所有环境中都工作得很好。

这是今天早上的拉取请求:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 允许用户配置使用夜间模式(测试版)和无衬线文章正文 #2072](https://github.com/thepracticaldev/dev.to/pull/2072) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-03-15T16:27:34Z">Mar 15, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2072)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

一些用户可能出于各种原因想要夜间阅读模式或无衬线字体:个人偏好、可访问性等。

该配置提供了选项<g-emoji class="g-emoji" alias="smile" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f604.png">😄</g-emoji>

无衬线: [![](img/2ebeff11554361d13052fe3ec54f9305.png)](https://camo.githubusercontent.com/16a90784343ee87ab81693cd8dd3bbad0a74a628/68747470733a2f2f636c2e6c792f3330643139316533393730372f496d616765253230323031392d30332d3135253230617425323031322e32322e3131253230504d2e706e67)

仍然有工作要做，让夜间模式看起来刚刚好，但这个公关应该加快，因为更多的人会开始使用它。感谢 [@link2twenty](https://dev.to/link2twenty) 在主题方面的出色工作。

实现无衬线 css 可以做主题风格，但我不确定，所以我这样做了。我们可以以后再改变。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2072)

在清除一些缓存之前，这可能不会在网站上的所有页面上起作用，但应该很快就会在全局范围内起作用。

编码快乐！