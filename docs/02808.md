# 辅助功能优先:对话框

> 原文：<https://dev.to/link2twenty/accessibility-first-dialog-2p49>

是时候写另一篇博文了，这次我会弹出一段对话。那些读过我早期文章的人可能知道我写了关于原生的`<dialog>`元素，但是支持仍然是不完整的，它只在基于`Blink`的浏览器上工作。

[![link2twenty](img/658801eb0300e3b6e98dde9a1fc0aa97.png)](/link2twenty) [## 本机 HTML:对话框

### 安德鲁·伯恩 1 月 3 日 193 分钟阅读

#html #css #a11y #design](/link2twenty/native-html-dialog-boxes-192b)

不起眼的对话框在 UI 和 UX 设计中已经存在很长时间了，每个人都有自己的实现方式。JQueryUI 是最常见的方法，我们这些上了年纪的人都知道，但是为什么要为一个特性加载整个库呢？

我将采用另一种方法，我将使用通用的`<details>`元素，当然，这并不意味着仍然没有 IE，但这是一个开始。这是成品。随着我们的进展，我会解释什么和为什么。
[https://jsfiddle.net/link2twenty/karjy53u//embedded/result//dark](https://jsfiddle.net/link2twenty/karjy53u//embedded/result//dark)

## 要求

一如既往，我们的目标是支持键盘用户和鼠标用户，让我们看看[规范](https://www.w3.org/TR/wai-aria-practices/examples/dialog-modal/dialog.html)，看看我们能看到什么。

### 键盘

*   **Tab 键**，跳至下一个可选项(浏览器默认处理)
*   **Shift + Tab 键**，跳至上一个可选项(浏览器默认处理)
*   **退出键**，隐藏对话

### 鼠标

对于鼠标，有两个简单的规则:在对话框内单击以与项目进行交互，就像在对话框外单击以关闭对话框一样。

## Markup

正如我之前说过的，我将使用 [`<details>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/details) 元素，它有一堆我想要内置的功能。
[https://jsfiddle.net/link2twenty/eo624Lyb//embedded/html,result//dark](https://jsfiddle.net/link2twenty/eo624Lyb//embedded/html,result//dark)

正如你所看到的，所有的骨架都在那里，但我们不能真正称之为对话。我们需要打破底部，但这就是风格的目的。

## 样式

我不想过多描述我的风格，这只是我选择的方式。如果你知道更好的方法或者想知道我为什么用特定的方法做某事，请在下面自由提问🙂
[https://jsfiddle.net/link2twenty/z9hcqa10//embedded/css,result//dark](https://jsfiddle.net/link2twenty/z9hcqa10//embedded/css,result//dark)

现在我们有了。看起来不错，我们走吧...哦，它可以打开，但是没办法关上。那就不理想了，是时候看看 JS 了。

## JavaScript

我使用一个 ES6 类来包含我的控件和监听器，你不必这样做，但是如果我们不支持 IE，为什么不呢？

`<details>`标签基于一个开放属性而改变，我们可以利用这个属性将我们的控件移动到 JS 中。按下`<summary>`“按钮”仍然可以工作，但是我们不需要依赖它。

此外，我们还为点击和退出键添加了监听器。一旦所有的主要构件都就位，Javascript 就非常适合添加您需要的所有小功能。
[https://jsfiddle.net/link2twenty/karjy53u//embedded/js,result//dark](https://jsfiddle.net/link2twenty/karjy53u//embedded/js,result//dark)

## 结束

另一篇帖子到此结束，感谢阅读。你可能注意到了，也可能没注意到，我没有像往常一样发那么多帖子。这是因为我找到了一份新工作🎉🎉。这很棒，也很令人兴奋，但在我小小的脑子里却没有为这样的事情留下多少空间。希望随着时间的推移，我会更有规律地发帖。

如果这篇文章中有任何你不明白或者我不明白的地方，请在下面自由发表。

再次感谢。
❤🦄🦄🧠❤🦄❤❤🦄