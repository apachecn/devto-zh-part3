# 如何在你的博客中添加 Twitter 卡片元标签

> 原文：<https://dev.to/therealdanvega/how-to-add-twitter-card-meta-tags-to-your-blog-4iei>

对于那些最近没有看我博客的人来说，我正在将我的网站从 WordPress 转换到一个名为 [Gridsome](https://gridsome.org/) 的静态网站生成器，它建立在 VueJS 之上。在使用像 WordPress 这样成熟的博客平台时，有很多事情我都认为是理所当然的。在 WordPress 中，当我创建了一篇新的博客文章，然后在 Twitter 上分享时，我的博客文章得到了这个漂亮的显示，而不仅仅是一个链接。

对大多数人来说，这似乎不是什么大事，但是如果没人读，为什么还要写呢？参与是 Twitter 等社交媒体平台上的游戏名称，Twitter 卡片(以及一般的图片)被证明可以提高参与度。在这篇文章中，我将告诉你什么是 Twitter 卡，以及如何在你的博客中添加 Twitter 卡元标签。

## 什么是推特卡

在我们深入讨论如何在你的博客中添加 Twitter 卡的技术细节之前，了解一下 Twitter 卡实际上是什么是有意义的。我目前没有为我的网站主页设置任何 Twitter 卡。坐下来写这篇文章后，我意识到我应该解决这个问题。如果我在 Twitter 上分享我主页的链接，它看起来像这样。

[![Twitter with no card](img/4a7284ba30b7926c507b5bff6adbb454.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TdS4UVm5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gto5stsqfkkw9lubnjgr.png)

很无聊吧？不管我的信息是什么，你都不会被强迫去点击那个链接。

这是我刚刚在 Twitter 上发布的一篇博文的链接。

[![Twitter with Card](img/ff8d6e342333c0fcbe6b1d34d2196fb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wqrRiONP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4vpbp7rxwdn5zft6reze.png)

在这种情况下，您可以看到一张格式精美的卡片，上面有摘要和图像。现在，我承认图像可以使用一点工作，因为一些文字运行反对的一方，但你得到的想法。我认为这是一个非常好的补充，你今天可以把它添加到你的博客中，只需要几行代码。

## 什么是元标签

如果你熟悉 HTML，你可能以前遇到过 meta 标签。元数据是关于数据的数据(信息)。这意味着 meta 标签提供了关于 HTML 文档的元数据。元数据不会显示在页面上，但可以进行机器分析。这只是意味着它不会显示，但会存在于您的页面源代码中。

Meta 标签在 HTML 文档的头部定义，包含以下属性:

| 属性 | 价值 | 描述 |
| --- | --- | --- |
| 字符集 | 字符集 | 指定了 html 文档的字符编码 |
| 内容 | 文本 | 给出与 http-equiv 或 name 属性关联的值 |
| http-equiv | 内容类型
默认样式
刷新 | 为内容属性的信息/值提供 HTTP 头 |
| 名字 | 应用名称
作者
描述
生成器
关键词
视口 | 指定元数据的名称 |

这是元标签的一个例子。

```
<head>
    <meta name="description" content="Welcome to my website!">
</head> 
```

### Meta 标签用例

既然你已经知道了什么是元标签，那么理解它们的用途可能会有好处。这里有一些你可能已经遇到过的元标签的例子。

```
<head>
  <meta charset="UTF-8">
  <meta name="description" content="Welcome to my website!">
  <meta name="keywords" content="HTML,CSS,JavaScript">
  <meta name="author" content="Dan Vega">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head> 
```

如果你做过任何类型的搜索引擎优化(SEO ),你可能会遇到元描述。

> 元描述是一个大约 155 个字符的片段 HTML 中的一个标签——它概括了一个页面的内容。搜索引擎通常在搜索结果中显示元描述，因此优化元描述对于页面 SEO 至关重要。

大多数元标签只不过是名称和内容，事实上，如果你愿意，你可以创建自己的标签。

```
<meta name="msg" content="hello meta tags"/> 
```

虽然这是有效的语法，但它实际上并不做任何事情。如果你要写一些程序来扫描 HTML 来寻找这个特定的 meta 标签，那么包含它是有意义的，否则就没用了。

## 将 Twitter 卡片添加到您的博客或网站

不，你知道更多关于元标签的知识，让我们把这些知识应用到 Twitter 卡片上。您需要设置的第一个元标记是 twitter:card 元标记。

```
<meta name="twitter:card" content="summary_large_image" /> 
```

该标记可以具有下列值之一

*   [总结](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/summary)
*   [概要 _ 大 _ 图像](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/summary)
*   [玩家](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/player-card)
*   [app](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/app-card)

在我的例子中，我之前给你展示了我正在使用的博文 **summary_large_image** 如果您将其他标签与示例卡进行比较，它们就非常简单明了。我要说的一件事是，确保给 twitter:image 标签提供您想要使用的图像的完整路径。

以下是我在这篇博文中使用的所有标签，以及它的外观。

```
<head>
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:description" content="How to create your first npm package and publish it." />
  <meta name="twitter:title" content="Creating your first npm package" />
  <meta name="twitter:site" content="@therealdanvega" />
  <meta name="twitter:image" content="https://www.danvega.me/assets/static/npm_cover.bd64798.eced3da.png" />
  <meta name="twitter:creator" content="@therealdanvega" />
</head> 
```

[![Link with Twitter Card](img/ff8d6e342333c0fcbe6b1d34d2196fb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wqrRiONP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4vpbp7rxwdn5zft6reze.png)

如果您想了解更多关于您可以创建的不同类型的卡以及所有选项，请通读[文档](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/abouts-cards)。

### 推特卡验证器

如果你的 Twitter 卡片显示不正确，有一种方法可以测试它们。您可以使用 Twitter Card Validator，插入想要测试的 URL，并查看结果以及一些调试信息。在这里，我输入了另一篇博文，并预览了我的 twitter 卡片的外观。

[![Twitter Card Validator](img/17747b2522bc8c92140d819a4eb6166c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OYZeab9h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6vga4csk8nus110ef5ra.png)

## 结论

我在本文开头提到过，我正在将我的网站从 WordPress 转换到 Gridsome。如果你对我如何在这个博客上添加 Twitter 卡片感兴趣，请告诉我。如果你也对如何为其他社交网络添加类似的元标签感兴趣，你可以在 open graph 上做一些研究，或者告诉我你想看关于这个的另一篇文章。

快乐编码

丹

*这篇文章最初是在 https://www.danvega.dev/blog 的[发表在我的博客上的。如果你觉得这篇文章有趣，请考虑](https://www.danvega.dev/blog)[订阅我的时事通讯](https://www.danvega.dev/signup/)或者在 [Twitter](http://twitter.com/therealdanvega) 上关注我。*