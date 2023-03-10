# CSS 的未来:滚动条

> 原文：<https://dev.to/link2twenty/future-of-css-scrollbars-2ihd>

在我开始之前，我要说这真的不适合任何项目，这是第一个公开的工作草案，几乎没有浏览器支持。你可能想知道为什么我要把它变成一篇博客文章，因为越多的人对此感兴趣，我们就能越快在浏览器中看到它。

> 作为**第一份公开工作草案**出版并不意味着 W3C 成员的认可。这是一个草稿文档，可能会随时被其他文档更新、替换或废弃。引用本文档作为正在进行的工作之外的内容是不合适的。

已经有 2 个新的 CSS 属性被提出 [`scrollbar-color`](https://www.w3.org/TR/css-scrollbars-1/#scrollbar-color) 和 [`scrollbar-width`](https://www.w3.org/TR/css-scrollbars-1/#scrollbar-width) 。它们都在 2018 年 9 月发布的 [CSS 滚动条模块级别 1](https://www.w3.org/TR/css-scrollbars-1/) 文档中，但是，当然，我们将浏览 Mozilla 的开发网站以获得更好的理解。

## 滚动条-颜色

让我们先来看看 [`scrollbar-color`](https://developer.mozilla.org/en-US/docs/Web/CSS/scrollbar-color) 页面，看看 Mozilla 是怎么说的。

> CSS 属性 **`scrollbar-color`** 设置滚动条轨道和滚动块的颜色。
> 
> **轨迹**是指滚动条的背景，无论滚动位置如何，一般都是固定的。
> 
> **拇指**指的是滚动条的移动部分，它通常浮动在轨道的顶部。

`scrollbar-color`接受 4 个输入`auto`、`dark`、`light`和`<color> <color>`，描述如下。

> **`auto`**
> 默认平台呈现为滚动条的跟踪部分，没有任何其他相关的滚动条颜色属性。
> 
> **`dark`**
> 显示深色滚动条，可以是平台提供的深色滚动条，也可以是自定义的深色滚动条。
> 
> **`light`**
> 显示浅色滚动条，可以是平台提供的浅色滚动条，也可以是自定义的浅色滚动条。
> 
> **`<color> <color>`**
> 将第一种颜色应用于滚动条滑块，第二种颜色应用于滚动条轨道。

```
:root {
  scrollbar-color: #111 #333;
  height: 200vh;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/5yq5q18vjx](https://codesandbox.io/embed/5yq5q18vjx)

如果你碰巧在 Windows 或 Linux 上使用火狐 64 版本，那对你来说可能是有用的，如果不是，这里有一个截图。

[![Dark Scrollbar](img/eb870cd54a0ffa8db616116194f762de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BDuzwA7y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/14mfewuv88ltyqccvdvi.png)

目前，自定义颜色可以工作，但是`auto`、`dark`和`light`都保留滚动条为默认。

## 滚动条-宽度

现在我们继续讨论 [`scrollbar-width`](https://developer.mozilla.org/en-US/docs/Web/CSS/scrollbar-width)

> 属性允许作者设置元素滚动条显示时的最大厚度。

这次我们只得到 3 个可能的输入`auto`、`thin`和`none`。他们被描述成这样

> **`auto`**
> 为平台默认滚动条宽度。
> 
> **`thin`**
> 提供该选项的平台上的瘦滚动条宽度变体，或者比默认平台滚动条宽度更瘦的滚动条。
> 
> **`none`**
> 不显示滚动条，但是元素仍然可以滚动。

```
:root {
  scrollbar-width: thin;
  height: 200vh;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/rnr1ol2k4](https://codesandbox.io/embed/rnr1ol2k4)

同样，FireFox 是必需的，所以下面是它的样子。

[![Thin Scrollbar](img/41d51e28382ba1fd99630f8d3617d2cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6Ix6LeNZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/exk8iwgeqq438v48ly0j.png)

## 两者一起

```
:root {
  scrollbar-color: #111 #333;
  scrollbar-width: thin;
  height: 200vh;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/k1mxon4r15](https://codesandbox.io/embed/k1mxon4r15)

[![Dark and Thin scrollbar](img/cabc64cfe589e10ba93f4c24aaa2ec03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7ORtqgFu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cv34ejlxi1grbxriys12.png)

## 包装完毕

我要重申的是，这还没有为生产做好准备，但看看我们的小平台将走向何方是很有趣的。如果你喜欢这种形式，请让我知道，如果你对我下一步可以涵盖的内容有任何想法，我很想听听。

感谢你阅读❤🦄❤❤🦄🦄🦄❤