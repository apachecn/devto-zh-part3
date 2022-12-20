# 原生 HTML:手风琴

> 原文：<https://dev.to/link2twenty/native-html-accordion-2fma>

如果你回想 10 年前 JQuery 为王的时候，你可能会记得 JQuery UI 曾经是多么的无处不在，这意味着网站突然拥有了更多的功能和元素。其中一个元素就是不起眼的[手风琴](https://jqueryui.com/accordion/)。

[![JQuery Accordion](img/35bfed85d53121cfd0fe3c9bd3525caa.png)](https://jqueryui.com/accordion/)

但那是十年前的事了，我们不再需要 javascript 库来完成我们的重担，平台已经走过了漫长的道路。所以今天我们将学习`<details>`和`<summary>`，它们相当于现代的手风琴。实际上，只有一个值得注意的顽固分子得到了大力支持。

[![Details Support](img/b5a38e8a3b9b04343a85d139a82eac83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xYD_gufO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oxef0yka39f44l4qkwxe.png)

## 细节元素

像往常一样，让我们先看看 Mozilla 在他们的 [`<details>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/details) 页面上说了些什么。

> **HTML Details 元素(`<details>` )** 创建一个 disclosure 小部件，其中的信息只有在小部件切换到“打开”状态时才可见。可以使用 [`<summary>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/summary) 元素提供摘要或标签。
> 
> 显示 widget 通常在屏幕上显示，使用一个旋转(或扭曲)的小三角形来指示打开/关闭状态，三角形旁边有一个标签。如果`<details>`元素的第一个子元素是一个`<summary>`，那么`<summary>`元素的内容将被用作 disclosure 小部件的标签。

读到这里，我们了解到`<details>`元素有两种状态，`open`和`closed`，并且它期望一个`<summary>`元素作为它的第一个子元素，但是并不需要一个。

## 汇总元素

现在我们可以看看对 [`<summary>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/summary) 元素的描述了。

> 元素为一个 [`<details>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/details) 元素的显示框指定一个摘要、标题或图例。单击`<summary>`元素切换父`<details>`元素的打开和关闭状态。

我们在这里并没有学到太多额外的东西，这是因为这两个元素是如此紧密地联系在一起，而不是点击摘要文本来切换细节。

## 使用它

让我们来看看如何使用它。

```
<details>
  <summary>Read more</summary>
  Some text to be hidden. 
</details> 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/210wx5y71r](https://codesandbox.io/embed/210wx5y71r)

我们有它，它不是标准形式中最好看的元素，但它确实起作用了。

## 造型吧

我在这篇文章的开头回顾了 JQuery UI 的手风琴，因此我们将`<details>`块设计成看起来相似似乎是合适的。

```
details {
  overflow: hidden;
  margin-top: 0.125em;
  border: 1px solid #dddddd;
  background: #ffffff;
  color: #333333;
  border-radius: 3px;
}

details summary {
  display: block;
  cursor: pointer;
  padding: .5em .5em .5em .7em;
  background: #ededed;
  color: #2b2b2b;
  border-radius: 3px 3px 0 0;
}

details:not([open]) summary:hover,
details:not([open]) summary:focus {      
  background: #f6f6f6;
  color: #454545;
}

details[open] summary {
  border: 1px solid #003eff;
  background: #007fff;
  color: #ffffff;
}

details main {
  padding: 1em 2.2em;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<details>
  <summary>Summary</summary>
  <main>Text to hide away.</main>
</details> 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/206xlm397j](https://codesandbox.io/embed/206xlm397j)

尽管有一些警告；

> 不幸的是，目前还没有内置的方法来制作打开和关闭之间的转换动画。

此外，手风琴做了一些不同的事情，一次只能打开一个项目，这个限制我们可以用 JS 来解决，键盘导航更类似于一个单选按钮组，而不是 tab 键浏览。

## 包装完毕

因此，我们有了另一个原生 HTML 元素和另一个使用较少 JavaScript 的机会。

像往常一样，如果你不同意我说的任何事情，让我知道为什么，我很乐意听到你的不同意见。此外，请随意建议我应该看看的其他元素。

感谢你阅读❤❤🦄❤🦄❤❤