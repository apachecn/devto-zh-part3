# 原生 HTML:惰性

> 原文：<https://dev.to/link2twenty/native-html-inert-4o3h>

今天我们将看看`inert`房产，这是由 [@vinceumo](https://dev.to/vinceumo) 在我上一篇帖子的评论中提出的，谢谢文森特。`inert`在规范中，但尚未被任何主流浏览器采用，也就是说，它在 Blink (Chrome 和 Opera)的一个标志后面可用。

对于用键盘浏览你的网站的用户来说，这非常重要，但它也让页面对所有用户更加友好。

## `inert`地产

我们终于可以回到 Mozilla 网站来获得我们的定义了！虽然 [`inert`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement#Properties) 只是作为`HTMLElement`的一部分被提及，还没有自己的页面。

> [**`HTMLElement.inert`**](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/inert)
> 是一个 [`Boolean`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean) ，它指示用户代理是否必须在给定节点不存在的情况下工作，以便进行用户交互事件、页面内文本搜索(“在页面中查找”)和文本选择。

让我们分解一下，我们知道它是一个`boolean`属性，这意味着它要么是`inert`要么不是。这告诉我们所有我们需要的语法信息。

```
<!-- The main content is not inert -->
<main id="content">
  ...
</main>

<!-- The navigation panel is inert -->
<aside id="nav" inert>
  <nav>
    ...
  </nav>
</aside > 
```

Enter fullscreen mode Exit fullscreen mode

太好了，现在我们知道如何使用它了，让我们看看它实际上是做什么的。我们看到它指示浏览器“`act as though the given node is absent`”，这意味着我们不能点击、跳转或选择元素，而且它的文本在大多数浏览器的`find in page`功能中是隐藏的。但是，重要的是，它仍然可见。

## 但是...为什么？

我采用了和上面一样的代码结构，隐藏了我的菜单，把它移到了右边，就像滑动菜单一样。我写了一点 JS 来告诉我们哪个元素已经被选中了。试着跳过锚点，你也可以试着找到“菜单项”，你会看到有 4 个结果，但是浏览器不知道它们在哪里。

[https://codesandbox.io/embed/2zxp75oxr0](https://codesandbox.io/embed/2zxp75oxr0)

这只是一个例子，但是想想其他的东西，比如模态对话框，隐藏时的内容，或者模态打开时的其他东西，或者带按钮的传送带。

## 参考文献

有一些很棒的地方可以阅读，我会从关于它的第[页开始。还有几年前](https://wicg.github.io/inert/) [@robdodson](https://dev.to/robdodson) 的 a11ycast 视频。

[https://www.youtube.com/embed/fGLp_gfMMGU](https://www.youtube.com/embed/fGLp_gfMMGU)

## 注销

A11y 变得越来越重要，因为我们努力使网络成为每个人的地方，像这样的属性确实是朝着正确方向迈出的一步。

我真的希望你学到了一些东西，或者至少喜欢这本书。如果我做错了什么，请告诉我，这是我们学习的方式。请提出你希望我探索的任何未来帖子的想法。

感谢您的阅读。
🦄🦄❤🦄❤❤🦄🦄🦄❤❤