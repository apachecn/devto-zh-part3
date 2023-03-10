# LWC–第一眼–处理 DOM 事件

> 原文：<https://dev.to/brettmn/lwc--first-look--handle-dom-events-35nd>

[https://www.youtube.com/embed/YaCbbmbXHQA](https://www.youtube.com/embed/YaCbbmbXHQA)

你好，我是布雷特和 WIPDevelopers.com。既然我们已经使用属性将 JavaScript 中的数据暴露给 HTML 模板，我们为什么不看看如何处理 DOM 事件并在 JavaScript 文件中对其做出反应。这是我们的 HTML 模板。

## DOM 事件

我想做的是当有人点击时进行处理。

如果我拼写正确会有帮助。当有人点击这个`h1`时，我想

我想在我的类上调用一个名为`handleClick`的方法

我分配给属性`onclick`的`h1`上的 JavaScript 没有引用我想在我的 JavaScript 文件上调用的方法名，它被花括号包围着。

因此，我们将进入 JavaScript 文件，并将其作为一个方法添加。

因为这是一个 JavaScript 类，所以我们不必键入 function。

我要做的第一件事是说它是在控制台中被点击的

要去掉方括号，请调用`window.console.log`。

这不会很令人兴奋。因此，让我们也改变我们的标签的价值。

我要把它分开，然后反转。然后，我们将加入它，没有空格或字符，我肯定应该重新分配给它自己。

所以我在这里做的是，我把标签或者每个字符都分成一个数组，我把数组反过来，然后我把它重新组合成一个字符串，再把它重新赋值给标签。一旦部署完毕。

现在我们可以刷新页面了，如果我单击它，它会反转。

我们可以一直点击一个箭头键。

但是如果我们想处理另一种事件呢

不是 handle 和`onclick`方法，什么是 handle `onmouseover`？

现在当我的鼠标经过它的时候，它反转了。

不是很有用，但我们现在知道如何将 HTML 中的事件直接连接到 JavaScript。

## 现在就这样。

记得报名参加 **[每周一次的单口相声！你可以得到我们关于 WIPDeveloper.com 的任何最新信息。](https://wipdeveloper.com/newsletter/)**

帖子[WIPDeveloper.com](https://wipdeveloper.com/lwc-first-look-handle-dom-events/)最先出现在[上【Look 第一次看-处理 DOM 事件](https://wipdeveloper.com)。