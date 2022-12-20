# 夜间模式🌚🌝使用混合混合模式:差异

> 原文：<https://dev.to/wgao19/night-mode-with-mix-blend-mode-difference-23lm>

我在准备我的演讲[这个世界混搭](https://speak.wgao19.cc/slides/1904-mixed-and-blended)的时候偶然想到了用 `mix-blend-mode: difference`实现夜晚模式的*(这些幻灯片需要彩色字体支持，即 FireFox / Safari)。最初的想法只是为了展示，而浏览器支持目前是有限的。*

尽管如此，我还是在我的 dev 博客上实现了它，并从中获得了很多乐趣。所以这就是这篇文章的内容。与此同时，当我想得更多的时候，我现在想到这个*可以是*一个合法的首选方式来完成夜间模式。让我也分享一下这背后的直觉。

请注意，这不是实现黑暗模式的标准方式。要了解人们通常是如何做到的，你可能想看看这里的[丹·阿布拉莫夫如何应对反应过度，或者](https://github.com/gaearon/overreacted.io/blob/master/src/html.js#L47)[这篇文章](https://dev.to/ananyaneogi/create-a-dark-light-mode-switch-with-css-variables-34l8)。

[![](img/2f1a359c0d6528a4bdaca35ad2343026.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d-toqjZt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/vzexItp.gif)

## 理解`mix-blend-mode: difference`

`mix-blend-mode: difference`是 CSS 支持的混合模式之一，在[合成和混合级别 1](https://www.w3.org/TR/compositing-1/) 中指定。[混合模式](https://www.w3.org/TR/compositing-1/#blending)反过来指定图形堆叠在一起时颜色如何混合。图形不限于图像。任何渲染的图形、div 内容、文本、表情符号🌚🌝SVG 可以参与混合。

我认为这是一个过程，每当浏览器要绘制新元素时，都会与我们进行核对:“嘿，我正在绘制的下一个像素似乎是红色的，但目前我在地面上看到的是蓝色，您是否希望我将这些颜色组合在一起，如果是这样，如何组合？”

混合模式允许我们创建一些非常有趣的效果，例如在图像上叠加文本，同时直接在浏览器中显示图形的纹理:

[![](img/aeb5839fa1aaa11e6cadf2fba9f318c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WGC16TDS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.w3.org/TR/compositing-1/examples/overlay_text.png)

来自[合成和混合级别 1](https://www.w3.org/TR/compositing-1/) 的混合模式示例

### 定义

`mix-blend-mode: difference`特别是一种混合模式，它被定义为取两种颜色之差的绝对值:

```
difference(A, B) = |A - B| 
```

Enter fullscreen mode Exit fullscreen mode

这是实际计算的抽象，对于每种颜色，我们分别用三个数字表示 R、G 和 B 通道。举个例子，

```
difference(
    rgb(255, 255, 255),
    rgb(0, 100, 200)
) = rgb(255, 155, 55) 
```

Enter fullscreen mode Exit fullscreen mode

同时，取绝对值使得运算*可交换*，意为`B(A, B) = B(B, A)`。换句话说，无论哪个图形放在另一个图形的上面，结果都是一样的。

### 直觉

混合模式*差异*到底在做什么？这个定义似乎很简单。然而，在真正看到它们之前，我们的大脑可能不太擅长感知最终颜色会发生什么。举个例子，*差(白，红)*本质上就是推导红色的补色，也就是蓝绿色。但是在你真正*看到*它之前，你怎么能“看到”它呢？

下面的钢笔是由白色和红色的交叉条纹混合而成的。

[https://codepen.io/wgao19/embed/GaKXZr?height=600&default-tab=result&embed-version=2](https://codepen.io/wgao19/embed/GaKXZr?height=600&default-tab=result&embed-version=2)

也许正是因为它难以察觉的效果，我们可以使用`mix-blend-mode: difference`来创造一些对比鲜明的视觉效果，在用户界面中引发惊喜，就像[斯文·沃尔弗曼](https://codepen.io/maddesigns/)的这个:

[https://codepen.io/maddesigns/embed/dpGVGw?height=600&default-tab=result&embed-version=2](https://codepen.io/maddesigns/embed/dpGVGw?height=600&default-tab=result&embed-version=2)

这是启发我考虑使用*差异*创建黑暗模式的设计。

## 使用`mix-blend-mode: difference`实现黑暗模式

### 它是如何工作的？

这个想法很简单:当夜间模式打开时，用一个带有`background: white`和`mix-blend-mode: difference`的全屏 div 覆盖你的站点，省去了重新定义所有颜色的麻烦😉

```
.dark-mode-screen {
  width: 100vw;
  height: 100vh;
  position: fixed;
  top: 0;
  left: 0;
  background: white;
  mix-blend-mode: difference;
} 
```

Enter fullscreen mode Exit fullscreen mode

为什么呢？

这个思考过程涉及到数学家们最喜欢的两个数字，0 和 1。

在单位空间中，0 表示*无*，1 表示*一切*。

在 RGB 颜色中，0 表示黑色，1 (100%或 255)表示白色。

现在让我们考虑一下我们的博客网站，假设目前我们有一个白色背景和一个黑色前景。如果我们把那一页和白色的不同，我们会得到什么？

*   背景:目前白色，白色和白色的区别是*无*，表示*黑*
*   前景:目前为黑色，表示*无*，白色和无的区别是*白色*

现在我们有了黑色背景和白色前景，看看这是什么？反转(黑暗)主题:)

此外，当用*白*取页面的*差*时，原始背景和前景之间的对比度会自动保留。这不仅适用于黑人和白人。事实上，我的原始主题为*德古拉*的代码块在反转时获得了漂亮的棕褐色色调。

[![](img/6a39b6466b21c26ddeed46ab561b2f02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c8cBjB3U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/i2lauxj.png)

[![](img/c704560b6b51151ca0be69ca93efe233.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P0MbuCta--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/4OY7tMn.png)

### 跃迁

**缩放一个分区**

我把一个开关固定在页面的底部。当夜间模式被打开时，我将这种切换放大到覆盖整个页面。这种效果有点炫耀原来的 UX 设计。

[![](img/2f1a359c0d6528a4bdaca35ad2343026.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d-toqjZt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/vzexItp.gif)

**不透明度**

如果你喜欢一个更微妙的切换，也许调整`opacity`是一个更好的选择，混合模式会工作得一样好。在过渡上有一个缓和的定时功能，这可能是大多数网站和博客共享的实现夜间模式的过渡 UI。

[![](img/45bcce111b0ca7d808308629a9eb63bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_xERc68C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/CsEehnx.gif)

### 隔离不想融合的元素

如前所述，`mix-blend-mode`将应用于*上的任何*图形。然而，当实现黑暗模式时，一些元素可能不打算混合。这样的例子包括特定颜色的标志、表情符号等。要指定这一点，您可以将`isolation: isolate` ( [引用](https://www.w3.org/TR/compositing-1/#isolation))放在那些元素上。请注意，这将在被隔离的元素上创建一个堆叠上下文。

```
/* twitter logo and emoji should not blend */
.twitter-logo,
.emoji {
  isolation: isolate; 
}

/* elements that create their a stacking context are automatically isolated */
.footer {
  z-index: 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 为你的深色模式选择一种颜色

如前所述，取页面与白色之间的差异就像直接取补色一样。您可能不必将颜色选择限制为白色。事实上，你可以选择任何颜色来区别。对于较暗主题的效果，如果您的站点最初是在浅色背景上，您可以考虑任何相对较浅的颜色:

[![](img/3e6f3caf4ba34ebf511229896e0f91f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iNGysWq6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/5exkPxU.gif)

这些是我遇到的一些技术细节。如果你正在尝试这样做，并在这里或那里遇到问题，请随时让我知道，如果我能帮上忙，我会很高兴。

要在代码中看到这个东西，请查看这个代码笔:

[https://codepen.io/wgao19/embed/vMzOgQ?height=600&default-tab=result&embed-version=2](https://codepen.io/wgao19/embed/vMzOgQ?height=600&default-tab=result&embed-version=2)

## 极限值

(除了令人讨厌之外😅)

*   你没有全系列的颜色。这省去了你指定更多颜色的麻烦，但是你会失去对深色的控制——它们会从你的浅色中推导出来。然而，这可能不是一种限制，而是一种设计选择。
*   浏览器对`mix-blend-mode`的支持仍然不是最佳的，[你只能使用非 IE 的相对较新版本的浏览器](https://caniuse.com/#search=mix-blend-mode)。
*   对于逐像素和逐通道计算，性能也是一个问题。不要用它做疯狂的动画(还不行)。

## 到下次🤞

混合模式是来自计算机图形学的公民。我记得几年前我第一次用 Photoshop 摆弄它们。现在，浏览器变得越来越强大，我们看到了浏览器呈现的复杂图形特性。这并不意味着我们应该在浏览器中实现一个完整的 photoshop，我们也不应该仅仅局限于此。浏览器和网页有它们自己的环境和目标，以及不同的限制。也许我们应该像欢迎新居民一样欢迎他们，并发现这个领域的本地用例。

灵感再一次归功于[这支笔](https://codepen.io/maddesigns/pen/dpGVGw)，一个第一眼就给人留下印象的可爱动画。你应该*悬停在它上面*🙈

[https://codepen.io/maddesigns/embed/dpGVGw?height=600&default-tab=result&embed-version=2](https://codepen.io/maddesigns/embed/dpGVGw?height=600&default-tab=result&embed-version=2)

我希望这是一个友好的关于混合模式的发现，我希望你和我一样开心。