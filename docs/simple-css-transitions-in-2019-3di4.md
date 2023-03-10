# 2020 年的简单 CSS 过渡

> 原文：<https://dev.to/paulryan7/simple-css-transitions-in-2019-3di4>

CSS 过渡可以为我们的 web 应用程序注入活力，从平滑的向下滑动效果到淡出元素，如果没有这些，我们的应用程序可能看起来非常静态和乏味。

然而，即使 CSS 转换如此重要并得到支持，它们仍然被可悲地误解了，许多开发人员(我知道我曾经是)犯了复制和粘贴他们不理解的代码的错误。这可能导致不可管理的代码和行为不正确的转换。

这篇文章将纠正这一点，给你一个简明的指南，告诉你如何使用 CSS 转换来改进你的 web 应用程序。

## CSS 转场

创建 CSS 转换的代码非常简单:
转换:

```
height .25s ease-in 
```

这种写转场的方式是简写的，你也可以写:

```
transition-property: height,
transition-duration: 0.25,
transition-timing-function: ease-in 
```

我发现这种编写过渡的方式增加了很多臃肿，本质上使事情变得复杂。

这段代码的意思是，当高度改变时，我想以 250 毫秒的速度转换它，而淡入基本上是说我想让它慢慢开始。

我发现通过查看下面的内容来编写转换要容易得多，这些内容清楚地陈述了转换属性的顺序:

```
transition: <property> <duration> <timing-function> <delay> 
```

通常最容易被误解的部分是“缓入”，这并不奇怪，因为“缓入”的属性名是“过渡-计时-功能”，老实说，这并没有使它更容易理解。

然而这可以解释得很简单:
`Easing describes an animation's rate of change over time`

真的就这么简单！

我的转变应该快速开始吗？
起步慢？
开始慢，结束快？

以下是您可以传递给过渡计时函数的值:

```
transition-timing-function: linear;
transition-timing-function: ease;
transition-timing-function: ease-in;
transition-timing-function: ease-out;
transition-timing-function: ease-in-out; 
```

顺便提一下，您也可以使用我们将在示例中使用的`cubic-bezier(n,n,n,n)`。

我认为这是足够的理论，信不信由你，即使有了这些核心知识，你也能很好地理解转变。

学习 CSS 过渡的最好方法是看一个例子。我已经创建了一个非常简单的[代码笔，显示我们在这里讨论的所有内容。](https://codepen.io/PaulRyan17/pen/f4824ef6eec4782a8c555a5b0979b561)

正如你从 CodePen 中看到的，它非常简单，显示了一只蝙蝠在推一个球。(这可能看起来不太对，但如果你点击“在 codepen 上编辑”，你可以看到它的所有优点)

因此，让我们通过不同的部分来实现这一点:

## 1。蝙蝠

因此，有时最好考虑开始和结束位置，而不是过渡。所以基本上我们想要的是移动球棒一定的距离，让它够到球，实现这个的代码:

```
.baseballbat.move{
  transform: translateX(200px) rotate(50deg);
} 
```

这段代码非常简单，`translateX(200px)`会在应用类移动时将球棒`200px`向右移动(可以用 JavaScript 添加类)。

忽略旋转，我只是用它来弄平蝙蝠，因为它是从我得到的图像指向角落。

所以下一步是转换棒球棍。

下面的代码将完成这一任务。

```
.baseballbat{
  height: 250px;
  width: 200px;
  transform: rotate(50deg);
  margin-left: 30px;
  transition: transform 350ms ease-in;
} 
```

这里重点关注的主要部分是:

```
transition: transform 350ms ease-in; 
```

这是非常基本的，我们将一个过渡应用到`transform`属性，它将采用`350ms`并且它将有一个`transition-timing-function of ease-in`。

现在棒球运动更加流畅了。

## 2。球

所以现在是时候移动棒球了。
像之前一样，我们首先关注球的末端位置，非常类似于棒球球棒，我们添加了一个变换属性:

```
.ball.move{
  transform: translateX(300px);  
} 
```

当移动类应用于球时，它将向右移动`300px`。

到目前为止，一切顺利吗？

好了，就像之前一样(看看一切是多么相似)，我们现在给球的变换属性添加一个过渡。
代码看起来像这样:

```
.ball{
  height: 100px;
  margin-left: 250px;
  top: -50px;
  transition: transform 550ms cubic-bezier(.02,.63,.79,1.33) 350ms;
} 
```

好了，一切看起来都是一样的，我们在`transform`属性上应用一个过渡，过渡将采用`550ms`，最后一个值是延迟，注意延迟与棒球球棒的过渡长度相匹配，以确保球只在球棒停止时移动。

所以我们这里有了一个新的值:

```
cubic-bezier(.02,.63,.79,1.33) 
```

三次贝塞尔曲线允许我们实现更多真实的过渡，你可以看到使用这种三次贝塞尔曲线，球向前移动，然后返回，就像上面有一个下旋。

三次贝塞尔曲线看起来很复杂，但它不需要如此，使用 cubic-bezier.com，你可以很容易地创建这些很酷的过渡。只要在网站上做一点小小的尝试，你就会完全适应它，并且能够轻松地将它复制并粘贴到你的过渡中。

## 总结

信不信由你，这基本上是你使用转场需要知道的一切。令人难以置信的是，转换是如此简单，但仍有许多开发人员不理解它们。我希望通过这个指南，我已经让更多的开发人员学会了他们的工具，而不是复制和粘贴。

有任何疑问/问题，就在下面留言评论吧。

感谢阅读。