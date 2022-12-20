# 带有背景图像的语音气泡

> 原文：<https://dev.to/peiche/speech-bubbles-with-background-images-4781>

我在几年前写了这篇文章，但是我又把它拖回了光天化日之下，因为我最近看到的另一篇[言论泡沫式的文章](https://dev.to/flippedcoding/how-to-put-arrows-at-the-bottom-of-a-div-4hnn)。

我想有一个自定义背景图像的演讲气泡一样的元素。这种方法很粗糙，但很有效，而且它没有有限的浏览器支持，不像 [`clip-path`](https://caniuse.com/#feat=css-clip-path) 。

典型的方法很简单——使用容器的:before 或:after 伪元素创建一个 CSS 三角形，并将其附加到适当的边上。

像这样:

[https://codepen.io/peiche/embed/bVLbEg?height=600&default-tab=result&embed-version=2](https://codepen.io/peiche/embed/bVLbEg?height=600&default-tab=result&embed-version=2)

你看到问题了，不是吗？箭头悬挂在泡沫的边缘，这是肯定的，但它看起来与纯色格格不入。用一些诡计，它可以看起来比这更好。

典型的语音气泡箭头效果是通过添加一个元素来创建的；在我们前面的例子中，一个带有边框的伪元素。

我们要做的也是边境黑客。但是我们不是增加一个元素，而是创造一个拿走一些东西的假象。

包含元素和内容包装器与第一个示例中的相同。但是背景不是全幅的；我们将把它拉得更远—在这个例子中是 10 个像素，但是你想把它拉得正好是你的箭头的宽度。

这里的技巧是使用背景元素的:before 和:after 伪元素来覆盖扩展区域。我们基本上要雕刻出箭头的形状。

这里有一个例子，每个边框都有不同的颜色，这让你可以看到我们要做的形状。

[https://codepen.io/peiche/embed/NGyrJm?height=600&default-tab=result&embed-version=2](https://codepen.io/peiche/embed/NGyrJm?height=600&default-tab=result&embed-version=2)

我们将使左边、顶部和底部的边框颜色透明，创建箭头的轮廓，右边框需要匹配包含语音气泡的元素的背景。那很重要，不然效果就没了。

这是最终的结果:

[https://codepen.io/peiche/embed/KdQPwv?height=600&default-tab=result&embed-version=2](https://codepen.io/peiche/embed/KdQPwv?height=600&default-tab=result&embed-version=2)

* * *

*这篇文章最初发表在[我的博客](https://eichefam.net/2015/10/23/speech-bubbles-with-background-images/)上。*