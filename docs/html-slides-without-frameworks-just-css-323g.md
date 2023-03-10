# 没有框架的 HTML 幻灯片，只有 CSS

> 原文：<https://dev.to/huijing/html-slides-without-frameworks-just-css-323g>

我是 reveal.js 的超级粉丝，我几乎在所有演讲中都使用它。这是一个全功能的演示框架，有很多漂亮的特性，比如演讲者注释、全屏模式、代码语法高亮等。

但有时，我只需要在当地聚会或办公室演示时，在屏幕上抛出一些信息。作为一名 web 开发人员，我用 CSS 控制和调整我的布局要比用点击式界面舒服得多。

我偶然发现了[ondřej·萨拉](http://ondras.zarovi.cz/)对[纯 CSS 幻灯片](http://ondras.zarovi.cz/demos/nojs/#)的实现，并深入研究了他的代码库，看看我能为自己淡化的 HTML 幻灯片使用什么。对于一个纯 CSS 实现来说，它的功能非常全面，无论如何都应该去看看。

## `:target`伪类

我想要的是一种从一张幻灯片过渡到另一张幻灯片的好方法，我注意到 Ondřej's 使用了`:target`伪类。它是一个选择器，匹配一个元素，该元素的 id 与当前 URL 的片段相匹配，该片段由符号`#`表示。

例如，如果您的站点有一个 id 为`dinosaur`的元素，您可以使用 URL`https://www.example.com/index.html#dinosaur`从地址栏导航到该元素。如果这是当前的 URL，下面的 CSS 规则将应用于`#dinosaur`元素:

```
#dinosaur:target {
  background-color: green;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![:target selector invoked when current URL fragment matches](img/3e507488c30187c73c39d5abb0bf672d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e6ssumMe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/blfjtfygpdj6tz857lz5.gif)

就这样，我们可以开始恶作剧了。

## 标记结构

HTML 结构可以相当简单。

```
<main>
  <section id="slide1"></section>
  <section id="slide2"></section>
  <section id="slide3"></section>
  <section id="slide4"></section>
  <section id="slide5"></section>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

每个幻灯片可以是占据视窗整个高度的一部分。我们可以使用视口单位相对容易地实现这一点。顾名思义，视口单位是相对于视口大小的 CSS 单位。

我们有`vw`、`vh`、`vmax`和`vmin`。为了制作一个 HTML 幻灯片，这里的相关单位是`vh`或视口高度。通过为每个部分设置一个高度`100vh`，它们将始终是视窗的高度，无论您如何调整浏览器的大小。

[![Slide is always full height of the viewport](img/2f892bf3103a6665d817b502486f33c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--daTbUVfr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cer0akxglzl1y3zzq4ev.gif)

每张幻灯片都有一个唯一的 id，可以作为目标，这将是幻灯片控制的机制。

[![Link to previous and next slide with URL fragments](img/66a02268c7ec8f1acf0b46e481c12522.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jBXASbRT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9u7qugp35krc2p22en73.gif)

那么`:target`伪类在哪里呢？等等，我们马上就来。我们之前做的是让浏览器跳转到每个部分的顶部，你仍然可以上下滚动，使你的演示像一个网页而不是幻灯片。

如果这是你想要它做的，很好。老实说，你今天已经下班了。但是假设我们不想激活滚动条。我们想要视窗中的活动幻灯片，就这样。然后就是这个时候`:target`伪类开始发挥作用了。

## 堆叠您的幻灯片

但是在进入之前，让我们想象一下幻灯片将如何运行。

<figcaption>每张幻灯片大小相同，并且上下叠放</figcaption>

[![Stacking the sections](img/a423bfd4eccad58a212c7862f660f70c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D-iSLBjv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/html-slides/stack.svg)

我们将设计各部分的样式，通过将它们堆叠在一起，在任何时候都只能看到一张幻灯片，然后利用 URL 片段来指示哪张幻灯片应该是活动的。

所以。沿着 z-index 堆叠嗯？我们现在有不止一个选择来实现这样的目标。让我们来看看位置和 z 索引这种久经考验的技术。`z-index`属性仅适用于定位框，即`position`值不是默认值`static`的框。

`position`现在有 4 个其他值，即`relative`、`absolute`、`fixed`和`sticky`，`sticky`是新增加的。[埃拉德·谢赫特尔](https://twitter.com/eladsc)写了一篇关于它如何工作的[精彩而深入的文章](https://medium.com/@elad/css-position-sticky-how-it-really-works-54cd01dc2d46)，所以一定要读一读。

根据[规格](https://www.w3.org/TR/CSS2/visuren.html#layers):

> 根元素形成了根堆栈上下文。其他堆叠上下文由任何定位的元素(包括相对定位的元素)生成，该元素具有除“auto”之外的计算值“z-index”。

所以一旦你将一个位置属性应用于一个盒子，你可以使用`z-index`属性来调整它的堆栈级别。在 box 上设置`position: absolute`会将其从正常流中移除，并导致其相对于其包含的块显式偏移。

绝对定位元素的内容不会围绕其他盒子流动，如果它们有更高的堆栈级别，它们只会覆盖它们下面的任何内容。

[![](img/600c2d7cbabfc9bd7236355bf8c7bbd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aKWSc0k8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/30vcb6ufrym9io8i2288.gif)

要将它们全部堆叠起来，在每个载玻片部分应用一个`position: absolute`和`width: 100%`。有些人可能会想，在一个绝对定位的元素上有一个`width: 100%`和将所有的偏移值都设置为`0`有什么区别？(例如`top: 0; right: 0; bottom: 0; left: 0`)还好别人也有一模一样的问题。

[Keith J. Grant](https://keithjgrant.com/) 写了一篇关于这两种方法之间的差异的分析，他发现如果你在元素周围使用额外的边距`width: 100%`,它将被移出其定位的祖先。

但是因为我希望我的幻灯片填满视窗，所以我根本没有使用边距，所以我宁愿使用单行而不是 4 行。🤷

下一件事是用我们可靠的`:target`选择器调整活动幻灯片的 z 索引，使其高于所有其他非活动幻灯片。

```
section {
  height: 100vh;
  width: 100%;
  position: absolute;
  z-index: 0;
}

section:target {
  z-index: 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

有些人处理 z 索引的方式不同，使用 10 或 100 的面值，但老实说，在两个元素之间，无论是 1 还是 1000，谁的整数值高谁就赢。

[![No overflow occurs](img/a1f41fa5b938695febb915da23027282.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4cglSHFX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/idtvt68sj4v6ebkv34ky.gif)

## 洒上一些动画

为了给这一努力增加一些专业的外表(我在骗谁呢？)，我觉得有些幻灯片过渡是有顺序的。CSS 也为此提供了一套可爱的动画选项。你可以旋转，缩放，滑入和滑出，所有的 CSS 变换。

如果你看了 Ondřej's 的幻灯片，他使用了旋转效果，让我们试试。为此有三种位置状态，*在进入视窗*之前，*在视窗*中活动，*离开视窗*。

<figcaption>In 'n out</figcaption>

[![Stacking the sections](img/daa8481aa768b1a15f4aa18aa91aed73.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vha4rIBr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/html-slides/rotate.svg)

默认的`transform-origin`是`50% 50%`，这意味着元素将围绕其中心旋转。要让一个角像图中那样旋转，将值设置为`0 0`。所有幻灯片从视窗开始顺时针旋转 90 度，而离开视窗的幻灯片将逆时针向上旋转 90 度。

我们可以利用同级选择器来定位活动幻灯片之后的幻灯片，并添加一些过渡值来使动画看起来更平滑:

```
section {
  height: 100vh;
  width: 100%;
  position: absolute;
  z-index: 0;
  transform: rotate(90deg);
  transform-origin: 0 0;
  transition: transform 1s, opacity 0.8s;
}

section:target {
  transform: rotate(0deg);
  z-index: 1;
}

section:target ~ section {
  opacity: 0;
  transform: rotate(-90deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

当幻灯片旋转到视窗之外时，它会触发滚动条。虽然我知道解决方案是在`body`元素中添加一个`overflow: hidden`,但是我还没有弄清楚这个问题的确切原因。我应该对此进行更多的研究，因为当我使用 translate 时不会发生这种情况。

## 包装完毕

还有一点需要注意的是，当你的幻灯片第一次加载时，不会触发任何 URL 片段，这意味着你的幻灯片都不会是活动的。要解决此问题，请向第一张幻灯片添加一个链接，它将在第一次加载时显示，请确保它也是一个定位元素。

[![End result](img/fbde1a2a7a744d2c127a87fd465dafce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pNS32CyI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ohtfo5xii2rhfzjo22y6.gif)

如果旋转不适合您的风格，请随意尝试其他 CSS 变换以获得更多幻灯片过渡。但是，如果您的幻灯片需求相当简单，为什么不尝试这样的东西呢？你甚至可以把它放到一些免费的静态网站主机上，无论你去哪里都可以访问它。😎

*如需现场实施，请查看[用于介绍演讲的幻灯片](https://singaporecss.github.io/talk.css)。CSS*