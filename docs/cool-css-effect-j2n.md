# 酷 CSS 效果

> 原文：<https://dev.to/mornir/cool-css-effect-j2n>

你看过 [Dropbox](https://www.dropbox.com) 的首页吗？太美了！设计很棒，照片很壮观，并且利用了有趣的 CSS 属性。

一个我觉得很棒，但也让我感兴趣的效果是固定导航条的平滑过渡。你自己可以看到，navbar 的内容(logo + links)会根据背景颜色改变颜色。

[![Dropbox effect](img/2989e5ec0ae171a8fcb553873661ceca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a4Z6lKhw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9n661z8mzfme1lvlghuh.png)

起初，我认为肯定涉及到一些 JavaScript 或至少一些复杂的 CSS。但是没有，效果其实很简单。

然而，理解底层逻辑是相当困难的。我花了整整一个小时才明白发生了什么事。

这就是为什么我决定写博客，一步一步地解释如何实现这个很酷的 CSS 技巧。

## 第一步:样板文件

我们将使用[顺风](https://tailwindcss.com/docs/what-is-tailwind/)。类名应该是不言自明的，但是您可以在 Tailwind 文档中查看它们的作用。

我们有三个全高部分，有不同的背景颜色。请注意我们是如何重复每个部分的标题的。这是达到预期效果所必需的。

[https://codepen.io/mornir0/embed/NJrLEr?height=600&default-tab=result&embed-version=2](https://codepen.io/mornir0/embed/NJrLEr?height=600&default-tab=result&embed-version=2)

目前为止没什么特别的。

## 步骤二:固定导航条

我们希望我们的导航条在滚动时被固定。所以让我们制作导航条`position: fixed`。现在所有的 nav 将被堆叠在左上角，最后一个(蓝色背景)在上面。我们还向`<p>`标签添加了一些填充，以防止内容被导航条屏蔽。

我们希望每个导航条在我们滚动通过它的部分时消失。例如，当滚动到绿色部分时，红色部分的导航栏应该隐藏。目前情况并非如此，你可以在下面的钢笔中看到。我把前两个标题做得更长了，所以你可以更好地看到发生了什么。

[https://codepen.io/mornir0/embed/PLzyYP?height=600&default-tab=result&embed-version=2](https://codepen.io/mornir0/embed/PLzyYP?height=600&default-tab=result&embed-version=2)

## 第三步:堆叠上下文

我们需要为每个部分创建一个新的堆叠上下文。实现这一点有不同的方法。在这种情况下，我们将为每个部分添加一个位置`relative`。为什么这样？因此，我们可以一石二鸟:在下一步中，我们还需要我们的部分充当相对容器。
现在，每个导航栏在滚动过各自的部分时都会被隐藏。

[https://codepen.io/mornir0/embed/ywJRjq?height=600&default-tab=result&embed-version=2](https://codepen.io/mornir0/embed/ywJRjq?height=600&default-tab=result&embed-version=2)

## 第四步:裁剪区域

我们现在想隐藏导航栏，当它在它的部分之外的时候。为此，我们将使用 CSS `clip-path`属性。我们首先需要定义可视区域。在我们的例子中，这个区域是导航条所属的整个部分。在 CSS 中，这个区域是通过在每个部分(绝对定位并固定到所有边缘)上添加一个覆盖层来定义的。但是现在我们不能再与该部分的内容进行交互。我们可以通过简单地添加`pointer-events: none`到我们的覆盖图中来解决这个问题。
最后，我们需要指定 nav 仅在位于相应部分时才显示。这是通过添加`clip-path: inset(0px)`实现的。这将覆盖定义为我们的裁剪区域。

[https://codepen.io/mornir0/embed/MxeZQp?height=600&default-tab=result&embed-version=2](https://codepen.io/mornir0/embed/MxeZQp?height=600&default-tab=result&embed-version=2)

我不得不承认，我并不完全理解`clip-path`是如何工作的，尤其是当使用百分比而不是像素时。我鼓励你在 codepen 或者 MDN 上的[交互示例中使用这个属性。
例如，尝试将其值设置为`inset(150%)`。](https://developer.mozilla.org/en-US/docs/Web/CSS/clip-path)

⚠️:尽管`clip-path: inset(0px)`在 Chrome 上运行良好，我还是推荐使用 Firefox 来尝试不同的值。
当我们谈论浏览器支持时，IE 和 Edge 不支持 clip-path 属性。然而，对于本文中介绍的技巧，您仍然可以使用不推荐的`clip`属性:`clip: rect(auto, auto, auto, auto)`

### 一起学习

我远非 CSS 专家。如果我错过了什么或者你有更好的解决方案，请在下面的评论区与我们分享。
我真的很感激在 stackoverflow 上找到了这个精彩的[答案，这启发了我写这篇文章。](https://stackoverflow.com/questions/48896706/dropbox-website-navigation-bar-effect)