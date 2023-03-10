# 参考指南:构建移动友好网站

> 原文：<https://dev.to/eroberts/reference-guide-building-a-mobile-friendly-website-2015>

[![](img/550bcc9352736b01e84a7dcbff705bf7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P9yc6AGK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nndbkjpgob84vdn17y8w.jpg)

如果你是一个新的网络开发人员，你可能已经制作了一些小的网络应用程序或网站，你想向你的朋友和家人炫耀。然而，这些人中的大多数可能会在他们的移动设备上查看你漂亮的网络应用。如果你在桌面上设计你的项目，你可能没有考虑过它在移动设备上的外观或工作方式。如果是这样的话，你可能会从那些与你分享你的项目的人那里收到大量的反馈，他们要么使用移动设备，要么使用 1080p 以下的显示器来查看你的新项目。

现在你正在探索如何让你的项目**移动友好**，我将*向你介绍*实现这一目标所需的工具。所有这些工具都有助于使你的网站*反应灵敏*或*适应*，但是没有确定的方法来使用它们使你的项目在各种尺寸的设备上看起来都很棒。这完全取决于你的网站是如何使用的，你需要展示什么。

这篇文章的目的是不要用大量的信息淹没你，而是给出每个工具的简要概述，并提供更深入信息的链接，这样你就可以跳到你想要实现的部分，或者只是在你需要的时候得到一个可用的想法。

# Viewport

```
<meta name="viewport" content="width=device-width, initial-scale=1.0"> 
```

> width 属性控制视口的大小。它可以设置为特定的像素数，如 width=600，或者设置为特殊值 device-width，即 100%比例下以 CSS 像素为单位的屏幕宽度。
> 
> initial-scale 属性控制页面首次加载时的缩放级别。最大比例、最小比例和用户可缩放属性控制允许用户放大或缩小页面的方式。
> -MDN

“像素不是像素。”具有更高 DPI(每英寸点数)或 PPI(每英寸像素)的设备可能会使为台式机设计的设计看起来不怎么样。普通桌面显示器的分辨率从大约 80dpi 到大约 200ppi 不等，而移动设备的分辨率通常是这个数字的两倍以上。

[MDN:视口元标签](https://developer.mozilla.org/en-US/docs/Mozilla/Mobile/Viewport_meta_tag)

* * *

# @viewport

```
@viewport {
  width: device-width;
}
@-ms-viewport {
  width: device-width;
} 
```

> @viewport CSS at-rule 允许您配置用来查看文档的视窗。它主要用于移动设备，但也用于支持“贴齐边缘”等功能的桌面浏览器(如 Microsoft Edge)。
> -MDN

[MDN: @Viewport](https://developer.mozilla.org/en-US/docs/Web/CSS/@viewport)

* * *

# 视口单位！(vw、vh、vmin、vmax)

```
div {
  width: 50vw;
  height: 50vh;
} 
```

> 新单位(vw、vh、vmin 和 vmax)的工作方式与现有的长度单位(如 px 或 em)类似，但表示当前浏览器视口的百分比。
> - [米丽娅姆·苏珊娜](https://css-tricks.com/author/miriam/) @ CSS-Tricks

> *   1vw:视窗宽度的 1%
> *   1vh:视口高度的 1%
> *   1vmin: 1vw 或 1vh，取最小者
> *   1vmax: 1vw 或 1vh，以最大者为准
> 
> - [克里斯·米尔斯](https://dev.opera.com/authors/chris-mills/) @ Dev。歌剧

CSS-Tricks:有趣的视口单元
[Dev。Opera: CSS 视口单位:vw，vh，vmin 和 vmax](https://dev.opera.com/articles/css-viewport-units/)

* * *

# @媒体查询

```
@media screen and (max-width: 900px) {
  div {
    width: 5vw;
    height: 5vw;
  }
} 
```

> 当您想要根据设备的一般类型(如打印与屏幕)或特定特征和参数(如屏幕分辨率或浏览器视窗宽度)来修改您的站点或应用程序时，媒体查询非常有用。
> -MDN

[CSS-Tricks: CSS 媒体查询](https://css-tricks.com/css-media-queries/)
[MDN:使用媒体查询](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries)

另一个选择是创建根据屏幕大小加载的完整样式表，也称为“自适应”模型
[CSS-Tricks:分辨率特定的样式表](https://css-tricks.com/resolution-specific-stylesheets/)

* * *

# CSS 网格

```
.container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr;
  grid-column-gap: 5px;
  grid-row-gap: 5px;
}
// If you had 9 divs inside a div with the classs .container 
// this will create a 3 x 3 grid of those 9 inner divs with 5px between them 
```

> CSS 网格布局是 CSS 中最强大的布局系统。它是一个二维系统，这意味着它可以处理列和行，不像 flexbox 主要是一维系统。
> 
> 通过将 CSS 规则应用于父元素(成为网格容器)和该元素的子元素(成为网格项目)，可以使用网格布局。
> -CSS-招数

**fr** 单元是一个用于定义 CSS 网格的灵活单元。

> 在定义网格时，可以使用 fr 单位(一个“分数”)，就像任何其他 CSS 长度一样，例如%，px 或 em。
> - [罗宾·伦德尔](https://css-tricks.com/author/robinrendle/) @ CSS-Tricks

[CSS-Tricks:CSS 网格入门](https://css-tricks.com/getting-started-css-grid/)
[CSS-Tricks:CSS 网格完全指南](https://css-tricks.com/snippets/css/complete-guide-grid/)
[CSS-Tricks:fr CSS 单元简介](https://css-tricks.com/introduction-fr-css-unit/)

* * *

# Flexbox

```
.container {
  display: flex; /* or inline-flex */
} 
```

> flex 容器扩展项目以填充可用空间，或者收缩项目以防止溢出。
> 
> 注意:Flexbox 布局最适合应用程序的组件和小规模布局，而网格布局则适用于大规模布局。

[CSS-Tricks:Flexbox 完全指南](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

* * *

# 响应式字体:em 和 rem

```
h1 {
  font-size: 2rem
} 
```

> em 是相对于它的直接或最近的父字体大小的，而 rem 只是相对于 html(根)字体大小的。
> - [克里斯·科伊尔](https://css-tricks.com/author/chriscoyier/)

[REM vs EM——大辩论](https://zellwk.com/blog/rem-vs-em/)

* * *

# calc()

```
.content {
  /* Subtract the header size */
  height: calc(100% - 50px);
  overflow: auto;
} 
```

> calc()是一种原生的 CSS 方法，可以代替任何长度值(或几乎任何数值)进行简单的数学运算。它有四个简单的数学运算符:加(+)、减(-)、乘(*)和除(/)。能够用代码来做数学是很好的，并且对于一门相当重数字的语言来说是一个受欢迎的补充。
> 
> calc()最有用的能力是混合单位的能力，比如百分比和像素。
> - [克里斯·科伊尔](https://css-tricks.com/author/chriscoyier/) @ CSS-Tricks

`calc()`真的可以在你有严格规则的时候拯救你的屁股，但是也需要响应式的设计。

[硬核 CSS Calc()](https://medium.com/buildit/hardcore-css-calc-bdfb0162993c)
[Calc()](https://css-tricks.com/a-couple-of-use-cases-for-calc/)的几个用例

* * *

# 适应性 vs 反应性

*   **Adaptive** 采用您当前的结构，并将其扩展为多种尺寸。
*   **响应式**根据用户代理和设备类型提供不同的模板。

[dev.to:自适应+响应式模型-Mattia Astorino](https://dev.to/equinusocio/the-adaptive--responsive-model-45mn)
[CSS-Tricks:响应式和自适应式设计的区别](https://css-tricks.com/the-difference-between-responsive-and-adaptive-design/)

# 其他工具:

[Chrome DevTools 中的设备模式](https://developers.google.com/web/tools/chrome-devtools/device-mode/)
[检测设备视口大小](https://www.mydevice.io/)