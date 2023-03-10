# querySelector 的教训和理解机制的重要性

> 原文：<https://dev.to/stephencweiss/lessons-in-queryselector-and-the-importance-of-understanding-mechanism-4ml4>

**注意:**下面的插图来自 MDN 在 [QuerySelector](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) 上的主页。这些例子不是我想出来的(虽然为了便于理解，我稍微修改了一下)。也就是说，我希望通过提供一些额外的背景来说明我现在是如何看待`querySelector`的。

根据 MDN，`querySelector`的语法是:

> `elementList = parentNode.querySelectorAll(selectors);`

虽然我曾不时地使用过`querySelector`，但最近我意识到它比我一直使用的简单方式要强大得多。这种认识是我最终认识到参数(`selectors`)实际上只是如何在 CSS 中应用样式的字符串。

当人们说在 HTML 和 CSS 方面有一个坚实的基础对成为一名前端工程师很有帮助时，**这个**就是他们所说的。

这里有一个我录制的简短视频，涉及一些基础知识和链接，我将在下面详细描述。

[https://www.youtube.com/embed/kMvdfn5GO3s](https://www.youtube.com/embed/kMvdfn5GO3s)

# 基础知识

让我们快速后退一步，只考虑一些基本问题。

当我第一次开始使用`querySelector`时，它是在 HTML 中挑选出单独的标签，然后从那里开始。

一个基本的例子是选择页面上的第一个或所有段落标签。

`let paragraphs = document.querySelectorAll(“p”)`。

# 增加复杂性

但是，如果你想要更多的细节呢？

回想一下 CSS 类选择器使用点符号(例如，`.intro`表示带有`class=“intro”`的元素)。querySelector 方法使用相同的表示法。

所以，如果你对这样的 CSS 样式感到舒服…