# CSS 的未来:配色方案

> 原文：<https://dev.to/link2twenty/future-of-css-color-scheme-10of>

最近有很多关于“黑暗”模式的讨论，iOS、MacOS、Windows 和 Android 都添加了系统范围的黑暗主题，可以自动为应用程序和程序启用黑暗模式。这种趋势很好，但是你的网站不能抓住这个全系统的主题真的很遗憾。还是可以？

在我继续之前，我应该指出这个特性是一个提议的特性，还没有出现在任何浏览器中。了解这些事情，甚至让你的项目适应未来，这很好，但不要试图在生产中推广它。

## 偏好-配色方案

Mozilla 有一个非常全面的所有原生 web 技术的列表，这里是它们如何描述这个特性的。

> **`prefers-color-scheme`** [CSS 媒体特征](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries#Media_features)用于检测用户是否请求系统使用浅色或深色主题。

让我们稍微打开包装。我们看到这是一个`CSS media feature`所以我们知道这是一个媒体查询。我们还可以看到它有两个选项`light`或`dark`。

这意味着它看起来会像这样。

```
@media (prefers-color-scheme: dark) {
  :root {
    ...
  }
} 
```

果然，这就是语法。真的简单！

## 选项

让我们从 Mozilla 获得当前选项的列表。

**无偏好**

> 表示用户没有让系统知道偏好。该关键字值在[布尔上下文](https://drafts.csswg.org/mediaqueries-5/#boolean-context)中被评估为假。

**灯**

> 表示用户已经通知系统他们更喜欢一个浅色主题的界面。

**黑暗**

> 表示用户已经通知系统他们更喜欢黑色主题的界面。

如果你看一下[规范](https://drafts.csswg.org/mediaqueries-5/#descdef-media-prefers-color-scheme)的草案，有一个他们正在看的脚注，其中也包括了`forced`变量。

> **问题 8** 我们是否应该包含`'forced-light'`和`'forced-dark'`值？我们不一定希望页面试图撤销系统可能执行的操作。

## 实际用途

你们中的一些人可能知道这个网站，dev.to，正在增加一个黑暗模式。这个网站也是开源的，所以你们中的任何人都可以加入并帮助完成这个过程！

# [![](img/375dfcc32199b4dedf2b526645c27ff7.png) 功能要求:夜间模式 #134](https://github.com/thepracticaldev/dev.to/issues/134) 

[![ghost avatar](img/29e48482b715470240aa6c57e15c1745.png) ](https://github.com/ghost)  **[ghost](https://github.com/ghost)** commented on [Jan 10, 2018](https://github.com/thepracticaldev/dev.to/issues/134)

# 特征请求

如果有一个“夜间模式”，可以将网站的颜色切换到在某些情况下看起来更好的深色版本，那将是非常棒的。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/134)

我已经开始将网站的主要部分转移到 CSS 变量上，这是有意为之的，一个缓慢的展示，但它正在实现。

# [![](img/375dfcc32199b4dedf2b526645c27ff7.png)【WIP】可主题化的 CSS 变量 #1377](https://github.com/thepracticaldev/dev.to/issues/1377) 

[![Link2Twenty avatar](img/1ba74938c13a54803539141f2e9d13e2.png) ](https://github.com/Link2Twenty)  **[Link2Twenty](https://github.com/Link2Twenty)** commented on [Dec 20, 2018](https://github.com/thepracticaldev/dev.to/issues/1377)

我认为我们应该有一个地方来记录当前的 CSS 变量，并提出应该包含的不同变量。

**目前包含:**

| 可变的 | 描述 | 默认 |
| --- | :-- | :-: |
| `--theme-background` | 主体的背景颜色 | `#f9f9fa` |
| `--theme-top-bar-background` | 顶栏的背景色 | `#fdf9f3` |

**拉取请求待定:**

| 可变的 | 描述 | 默认 |
| --- | :-- | :-: |
| `--theme-top-bar-color` | 顶栏的文本和图标颜色 | `#0a0a0a` |
| `--theme-top-bar-search-background` | 顶部栏中搜索框的背景颜色 | `#e8e7e7` |
| `--theme-top-bar-search-color` | 顶部栏中搜索框及其占位符的文本颜色 | `#0a0a0a` |

**建议:**

| 可变的 | 描述 | 默认 |
| --- | :-- | :-: |
| `--theme-color` | 正文的文本颜色 | `#0a0a0a` |
| `--theme-container-background` | 文章和导航元素的背景颜色 | `#ffffff` |
| `--theme-container-color` | 文章和导航元素的文本颜色 | `#0a0a0a` |

我认为最好的办法是在下面留下进一步建议的评论，我会更新主帖。

另外，也可以随时提出拉取请求来帮助推广这些产品。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1377)

有了这个媒体查询，我们可以这样做。

```
@media (prefers-color-scheme: dark) {
  :root {
    --theme-background: #303030;
    --theme-top-bar-background: #1C1C1C;
    --theme-top-bar-color: #FFFFFF;
    --theme-top-bar-search-background: #303030;
    --theme-top-bar-search-color: #FFFFFF;
    --theme-container-background: #424242;
    --theme-container-color: #FFFFFF;
  }
} 
```

而且，就像变魔术一样，整个网站有一个黑色的主题来匹配操作系统。

[![Quick Mock Up](img/981561a62d4bd4b25e2c4a0a3ebe129a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nAwCrJAV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l347jqkddklugbjwf41i.png)

当然，这并不完美，我们还需要更多的变量，但是你可以看到基于用户系统偏好的整个网站主题是多么简单。

## 包装完毕

正如我之前在帖子中所说的，这还不是我们所拥有的，它还没有一个 ETA，但是像这样的特性推动了网络向前发展！如果我们准备好了，当它着陆的时候，我们可以帮助把它发布出去。

使用 CSS，如果浏览器不理解你写的内容，它就会被忽略。因此，在一个浏览器中发布并准备好它并没有什么坏处。

**来源:**
[developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme)
[drafts.csswg.org](https://drafts.csswg.org/mediaqueries-5/#descdef-media-prefers-color-scheme)
**封面图片:**[pexels.com](https://www.pexels.com/photo/blue-dark-dark-blue-long-984670/)