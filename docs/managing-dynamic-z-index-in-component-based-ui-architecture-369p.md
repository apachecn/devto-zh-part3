# 在基于组件的 UI 架构中管理动态 z 索引

> 原文：<https://dev.to/huy/managing-dynamic-z-index-in-component-based-ui-architecture-369p>

这篇文章是对最近发表在 Smashing 杂志上的一篇文章的回应，这篇文章讨论了在一个复杂的 UI 应用程序中管理`z-index`的困难。
实际上，`z-index`决定了一个 DOM 元素的“堆叠顺序”,即当一个元素的空间范围重叠时，它是否出现在另一个之上。尽管这个功能相当简单，`z-index`在实践中可能很难使用。

我在开发交互式图形和图表时经常遇到的一个例子是相邻的交互式 DOM 元素有相互重叠的悬停工具提示。
例如，为每个数据点提供悬停工具提示的图表可以出现在图例旁边，图例也有自己的悬停工具提示，我们希望在显示时悬停工具提示完全可见。

下面是现场演示(此处代码[为](https://github.com/huy-nguyen/z-index-management/tree/problem-demo))。
视口被分成 3 个相邻的区域，标记为 A、B 和 C，具有不同的`background-color`。
它们每个都应该通过增加它们的`background-color`的`opacity`来响应悬停交互，并显示一个指向它们中心的工具提示。

因为我相信对内容的强烈拥有会带来更高质量的内容，所以我把我所有的作品都放在我的网站上。你可以在这里阅读这篇文章[的其余部分。](https://www.huy.dev/z-index-management-2019-04/)