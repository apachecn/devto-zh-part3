# 在 CSS 工具提示上

> 原文：<https://dev.to/trezy/on-css-tooltips-1foi>

**本文最初发表于 2012 年 10 月 5 日的[Trezy.com](https://trezy.com/blog/on-css-tooltips)。**

纯 CSS 工具提示已经有一段时间了，但是我发现的所有解决方案都有局限性。在阅读了 Louis Lazaris 的文章[后，我决定对他的设计做一些我自己的修改。](http://www.impressivewebs.com/pure-css-tool-tips/)

看看这个演示

## 问题

路易斯的设计打破了一些界限，但仍有一些局限性。这些是我的目标:

### Multiline

Louis 认为工具提示应该只包含少量信息，不需要多行工具提示。我在某种程度上同意他的观点，但是我仍然认为在某些情况下你会想要一个多行的工具提示。在我的例子中，我创建了一个错误消息，而不是普通的工具提示。我希望我的第一行只有错误类型，那么后面的所有行将包含错误消息。

### 兼容 IE8

这是一个大项目，因为我所在的公司需要这个项目。幸运的是，我们只需要支持 IE8、Firefox 和 Webkit。但是，请记住，这也意味着我不知道这些代码在 IE7 及以下版本中会有什么反应。

### 没有多余的 HTML

其他一些纯 CSS 工具提示解决方案使用额外的元素，如占位符跨度或 div。我希望能够在没有任何额外元素的情况下获取我需要的所有信息。我稍微变通了一下额外的 HTML 规则，因为我们确实使用了元素的属性来填充工具提示，但是我想我自己还是可以接受的。

## 解

如果您签出这支笔，您可能会注意到链接上的自定义`data-`属性。`data-tooltip`属性是二进制的，意味着它要么被设置，要么不被设置，就像`required`一样。在最终项目中，我将使用 jQuery 来创建和填充其他两个属性，但是现在我们将只使用一些 bunk 数据。

### 伪类和不常见属性

大多数伪类描述了元素的某种状态，例如，`:hover`适用于元素被悬停时，`:focus`适用于表单元素处于焦点中，`:disabled`适用于被禁用的元素，等等。然而，`:before`和`:after`有一点不同。它们分别用于为元素预先计划和追加内容。例如，您可以使用`.emailAddress:before`将标签添加到每个具有`emailAddress`类的元素的内部节点。

虽然我们使用了几个非常不常见的属性，但是你可能以前见过这个 CSS 的大部分内容。`content`属性只适用于`:before`和`:after`伪类。它决定了在元素前添加/附加什么。在这种情况下，我们使用一个非常酷的论点，叫做`attr(x)`。它允许您使用应用于元素的任何属性的值作为前置/追加元素的内容。在我们的例子中，我们使用了之前设置的自定义`data-`属性。

`white-space`是另一个奇怪的。它用于确定元素内部的空白(空格和回车)应该如何处理。我们使用了`white-space:pre-wrap;`,以便保留`content`属性中的空格，特别是作为 Unicode 回车符的`\A`,这几乎是确保在我们想要的地方换行的唯一方法。

### 定位

这是允许多行工具提示的最重要的一点。以前的一些解决方案使用了百分比定位，一些使用了像素定位，但是它们都使用了`top`和`left`属性。然而，如果你有不止一行的话，它们总是会被打断，所以它们经常和`white-space:nowrap;`一起使用。

我没有像 Louis 那样基于元素的顶部位置进行负向定位，而是将工具提示的顶部位置设置为零，这样它将与元素的顶部对齐，然后给工具提示一个`margin-top`。在我的例子中，我将工具提示放在一个链接上，所以我将底部边距设置为 1em，确保边距始终与我的文本高度相同。这可能需要根据您的行高进行调整。

如果您没有将工具提示放置在一行文本上，那么您也可以设置边距以匹配静态 height 元素的高度，或者使用一些 jQuery 魔法来调整它。

## 包装完毕

这段代码的其余部分是非常标准的东西。我们在这里做的另一件奇怪的事情是使用`:before`在工具提示的底部创建一个箭头。[路易的文章](http://www.impressivewebs.com/pure-css-tool-tips/)解释了这一部分，有很多其他教程使用 div 和边界创建形状，所以我不会涵盖这一点。

我已经在所有必要的浏览器(IE8+、Firefox 和 Google Chrome)中测试过了，还没有发现任何问题。如果你遇到一个问题或者有一个让它变得更好的建议，请在评论中告诉我！我将在以后做另一个教程，讲述如何使用 jQuery 使这个家伙可扩展。😉