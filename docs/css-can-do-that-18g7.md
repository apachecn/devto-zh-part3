# CSS 能做到？

> 原文：<https://dev.to/ananyaneogi/css-can-do-that-18g7>

CSS 能做的令人惊奇的事情的列表！

*编辑:由于支持的原因，其中一些属性在某些浏览器中无法使用。我们可以使用`@supports`来检查浏览器支持，并相应地添加回退样式。请使用 Chrome 查看示例🙂*

## 1。`box-decoration-break`

这个 CSS 属性指定了当一个元素的片段被分成多行、多列或多页时应该如何呈现。
[https://codepen.io/ananyaneogi/embed/GeRoRO?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/GeRoRO?height=600&default-tab=result&embed-version=2)

* * *

## 2。`attr()`

我们可以通过使用`attr()`在 CSS 中检索所选元素的值。这个方法对于可访问性非常有帮助。
[https://codepen.io/ananyaneogi/embed/gEpeda?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/gEpeda?height=600&default-tab=result&embed-version=2)

* * *

## 3。`backface-visibility`

这个 CSS 属性设置一个元素的背面在面对用户时是否可见。正在考虑卡片翻转 UI？

[https://codepen.io/ananyaneogi/embed/Ezmyeb?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/Ezmyeb?height=600&default-tab=result&embed-version=2)

* * *

## 4。`conic-gradient`

渐变是一个奇妙的东西。你可能已经习惯了使用线性渐变来设计背景，但是你知道吗，在`conic-gradient`的帮助下，我们可以使用纯 css 来创建饼状图！

[https://codepen.io/ananyaneogi/embed/mYmrMJ?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/mYmrMJ?height=600&default-tab=result&embed-version=2)

为了更好地理解`conic-gradients`，请参考上面的 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/CSS/conic-gradient)

* * *

## 5。`filter`

有了 CSS 滤镜，谁还需要 photoshop 滤镜效果。🙃
过滤功能将图形变化应用于输入图像的外观。我们能达到的效果有:`blur`、`brightness`、`contrast`、`grayscale`、`hue-rotate`、`opacity`、`invert`、`sepia`、`saturate`、`drop-shadow`。

[https://codepen.io/ananyaneogi/embed/YbVpyG?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/YbVpyG?height=600&default-tab=result&embed-version=2)

`drop-shadow`滤镜相当神奇。它允许您将投影应用到输入图像。
[https://codepen.io/ananyaneogi/embed/PvmbeR?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/PvmbeR?height=600&default-tab=result&embed-version=2)

* * *

## ⑥。`mix-blend-mode`

此 CSS 属性设置元素内容如何与元素背景或其父元素的内容混合。

[https://codepen.io/ananyaneogi/embed/rgwNLx?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/rgwNLx?height=600&default-tab=result&embed-version=2)
通过将混合模式和滤镜与图像和文本混合，可以实现令人惊叹的事情。了解有关 [MDN 文档的更多信息。](https://developer.mozilla.org/en-US/docs/Web/CSS/mix-blend-mode)

* * *

## 7。`first-letter`

书籍和杂志中我最喜欢的东西之一是漂亮的首字下沉。我们可以用`first-letter`伪元素创建 drop。
[https://codepen.io/ananyaneogi/embed/oRWBBE?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/oRWBBE?height=600&default-tab=result&embed-version=2)

* * *

## 8。`shape-outside`

这个 CSS 属性提供了一种方式来定制复杂对象周围的相邻内联内容，而不是简单的矩形框。

[https://codepen.io/ananyaneogi/embed/ZNKepv?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/ZNKepv?height=600&default-tab=result&embed-version=2)
*在不同的选项卡中打开示例，尝试更改窗口的宽度，并注意文本如何环绕图像。*

* * *

## 9。`writing-mode`

这个 CSS 属性设置文本行是水平还是垂直排列。我们可以拥有这些价值观-

*   `horizontal-tb` -内容从左到右水平流动，从上到下垂直流动。
*   `vertical-lr` -内容从左到右水平流动，从上到下垂直流动。
*   `vertical-rl` -内容从右向左水平流动，从上到下垂直流动。

检查这个[这个例子](https://ananyaneogi.github.io/css-experiments/quote.html)来看看它的作用。

* * *

## 10。给文本添加渐变

这是通过组合`-webkit-background-clip: text`和`-webkit-text-fill-color: transparent` CSS 属性实现的。

[https://codepen.io/ananyaneogi/embed/pmPPpY?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/pmPPpY?height=600&default-tab=result&embed-version=2)

* * *

## 11。平滑滚动捕捉

CSS 属性设置如何在滚动容器上应用对齐点。

[https://codepen.io/ananyaneogi/embed/mYmodx?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/mYmodx?height=600&default-tab=result&embed-version=2)

这个例子显示了值为`mandatory`的垂直(`y`)滚动。 [MDN docs](https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-snap-type) 很好地解释了如何使用其他值，比如`proximity`和水平滚动(`x`)。

* * *

这只是 CSS 能做的几件大事。可能性是无穷的！🥳