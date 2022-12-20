# 关于高级方框阴影

> 原文：<https://dev.to/trezy/on-advanced-box-shadows-51cf>

**本文最初发表于 2013 年 12 月 3 日的[Trezy.com](https://trezy.com/blog/on-advanced-box-shadows)。**

可以说是 CSS3 中最酷的东西之一。然而，我多年来一直在 Adobe Fireworks 和 Photoshop 中创建阴影，所以我觉得我太受 CSS3 的`box-shadow`实现的约束了。为了努力拓展我四四方方的视野，我开始寻求创造一些更酷的阴影。

*免责声明:*在撰写本文时，过滤器和转换仍然需要浏览器前缀才能工作，尽管过滤器只能在 Webkit 浏览器中工作。为了可读性，我在这里的代码示例中省略了这些前缀，但是如果您打算使用这些技术，它们将是必需的。

## 用方框阴影摇出

我第一次尝试使我的阴影更加动态，仍然使用`box-shadow`，但是它扩展了它的用途，创造了一个新的效果。我的主要目标是避免使用任何额外的元素，所以我们只需要一个 div，我们将利用伪元素来完成剩下的工作。

[https://codepen.io/trezy/embed/Jrhpc?height=600&default-tab=result&embed-version=2](https://codepen.io/trezy/embed/Jrhpc?height=600&default-tab=result&embed-version=2)

我们所做的是在主元素后面创建两个透明的伪元素。然后这两个伪元素应用了`box-shadow`。这样做是为了让我们可以使用 2D 变换来改变它们的角度，为它们的父元素创建一个 3D 外观的`box-shadow`。

我们还使用了`top`、`bottom`、`left`和`right`属性来防止阴影从父元素的侧面溢出。

## 框影…不带框影？

我对之前的想法很满意，但它看起来没有我希望的那么棒。我的下一个想法是用渐变来创造同样的效果。

[https://codepen.io/trezy/embed/aybiL?height=600&default-tab=result&embed-version=2](https://codepen.io/trezy/embed/aybiL?height=600&default-tab=result&embed-version=2)

这一次，我们仍然使用我们的伪元素，但不是给它们`box-shadows`，而是给它们渐变。我们还使用了一个名为`filter`的新 CSS3 属性。请务必在这里查看它能做的所有漂亮的事情。我们只是用模糊来柔化边缘，让它看起来更像一个阴影。

## 哦……那更漂亮。

当我在做这个项目时，我偶然发现了一个更酷的投影风格的网站，它指向内部而不是外部:

[因果报应](http://demo.truethemes.net/Karma-Wordpress/)

我真正要做的就是改变我最后的想法，然后我们就可以开始了！

[https://codepen.io/trezy/embed/zEtgJ?height=600&default-tab=result&embed-version=2](https://codepen.io/trezy/embed/zEtgJ?height=600&default-tab=result&embed-version=2)

## 结论

如果你使用这些阴影，请在评论中发布链接！此外，如果你提出任何替代的想法或应该注意的问题，让我知道。