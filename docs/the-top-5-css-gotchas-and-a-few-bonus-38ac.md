# 前 5 个 CSS 陷阱和一些额外奖励...

> 原文：<https://dev.to/sargalias/the-top-5-css-gotchas-and-a-few-bonus-38ac>

这个帖子([前 5 个 CSS 陷阱，还有一些奖励...](https://sargalias.com/blog/top-5-css-gotchas/))最初发表在 Sargalias 上。

* * *

CSS，我们都爱它对不对？说实话，我们欠 CSS 很多。没有它，网站看起来还是像 Word 文档。总而言之，CSS 是互联网的一个根深蒂固的组成部分，它会一直存在，它造就了今天的互联网。

那么这篇文章是关于什么的呢？

## CSS 极其不可预测。

CSS 的问题在于，有太多的事情**根本不像你所期望的那样**工作。你在期待一件事，但不，它恰好以完全相反的方式发生，除非你特别了解它，否则你永远不会知道它。仅仅通过你可能知道的其他信息是不可能搞清楚的，因为它根本不遵循这个模式。

在我的 web 开发之旅中，CSS 是迄今为止最难学习和使用的东西。HTML 非常简单，可以很快学好。JavaScript 更难学，但是当你学会了它的基础，你就可以开始了。有些人可能会说它有一些愚蠢的东西，比如对 NaN 求值，但实际上你永远不会在真正的编码中遇到这种情况。除了那些无关紧要的事情，除了*上下文*(可能需要一段时间才能理解)，JavaScript 是完全可以预测的。它有自己的规则，并遵循这些规则。

然后是 CSS。当然，CSS 的基础很容易学。不幸的是，你会经常遇到事情不按你期望的方式进行。你将无法克服它们，你不知道任何解决方法，它们将使你沮丧。即使你找到了类似于[栈溢出](https://stackoverflow.com)的解决方案，你仍然不知道它是如何工作的，除非你进一步研究。

使用 CSS，事情肯定是不可预测的。所以这篇文章的目的是向你介绍 5 大 CSS 陷阱，否则它们会在你的职业生涯中不断地挫败你。它也是一个很好的清单，你真的应该学习掌握 CSS。

## CSS 抓到你了

### 塌边

如果你从事 CSS 已经有相当长的时间了，你肯定会遇到这个概念。

*边缘塌陷*涉及边缘接触/相邻/碰撞……**，但仅垂直**。发生的情况是，这些页边没有被加在一起，而是合并成一个页边。**产生的页边距是两个页边距中最大的一个，而不是两个页边距的总和。**

例如，请注意下面的笔:

[https://codepen.io/sargalias/embed/majdxr?height=600&default-tab=result&embed-version=2](https://codepen.io/sargalias/embed/majdxr?height=600&default-tab=result&embed-version=2)

红框有一个`50px`的`margin-bottom`。

蓝框有一个`30px`的`margin-top`。

你会认为它们之间的差额是两者之和。

但相反，他们之间的总差额是 **`50px`** 。

这种行为被称为*利润崩溃*。两个边距相互合并，只留下两个边距中最大的一个。

更糟糕的是，这种情况只会发生在页边距垂直相交的时候。横向边距不会出现这种情况。当两个边距都为负(垂直)时也会发生这种情况，但当一个边距为负而另一个边距为正时不会发生这种情况。

利润率崩溃是你绝对需要知道的事情，因为如果你不知道，这将是你职业生涯中的一个障碍。以下是一些可以了解更多信息的好资源:

*   [母带边缘折叠(MDN)](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)
*   [关于折叠页边距(CSS-Tricks)你应该知道的事情](https://css-tricks.com/what-you-should-know-about-collapsing-margins/)

### 阻止格式化上下文(一个短暂的插曲)

另外*边距折叠*只影响同一个*块格式上下文*中的内容。简而言之，一个*块格式上下文*基本上是一个完全包含某些内容的容器。因此，它包含的内容不能与块格式上下文之外的任何内容进行交互。它实际上太远了，无法与外界有任何联系。

有几种方法可以创建*块格式化上下文*，更多信息请参见文章[块格式化上下文(MDN)](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Block_formatting_context) 。

### 包含块，绝对定位

CSS 中的几乎所有内容都包含在*内容框*中。

有*绝对定位*的东西除外。

例如，考虑下面的笔:

[https://codepen.io/sargalias/embed/bOjGZL?height=600&default-tab=result&embed-version=2](https://codepen.io/sargalias/embed/bOjGZL?height=600&default-tab=result&embed-version=2)

*静态定位块*包含在父块的*内容框*中。

*绝对定位块*包含在父块的*填充框*中。

所以只要记住所有*绝对定位的块*都包含在*填充框*中，其他的都包含在*内容框*中。

有关更多详细信息，请参见文章[包含块(MDN)](https://developer.mozilla.org/en-US/docs/Web/CSS/Containing_block) 。

## 包含块，绝对定位，部分 2

包含块是什么？通常情况下，它是最接近的祖先，其`position`属性的值不是`static`。但这不是唯一的条件。

如果祖先具有以下特征，则会达到相同的效果:

*   值不是 none 的转换属性。
*   值不是 none 的筛选器属性值。
*   具有非 none 值的 will-change 属性值。

考虑下面的笔:

[https://codepen.io/sargalias/embed/NeBPJV?height=600&default-tab=result&embed-version=2](https://codepen.io/sargalias/embed/NeBPJV?height=600&default-tab=result&embed-version=2)

注意，包含块的*没有`position`属性，而是有一个`transform`属性。*

有关其工作原理的更多详细信息，请参见文章[包含块(MDN)](https://developer.mozilla.org/en-US/docs/Web/CSS/Containing_block) 。

### 垂直对齐:居中；和在 CSS 中居中

当你知道如何在 CSS 中很好地将事情居中时，你就能真正感觉到你的技能正在与 CSS 融合在一起。

谢天谢地，现在我们有了 *flexbox* ，这就容易多了。

请注意，很有可能，`vertical-align: middle;`不会做您认为它会做的事情。它仅适用于表格或`inline` / `inline-block`内容。**您不能使用它来垂直对齐块。**

为了省去你很多麻烦，看看文章[在 CSS 中居中:完全指南(CSS-Tricks)](https://css-tricks.com/centering-css-complete-guide/) 。

### Flexbox

你对 *flexbox* 了解多少？ *Flexbox* 非常神奇，对每个开发者来说都非常有用，它可以帮助几乎任何布局。

问题是，如果你不知道 flexbox 到底是如何工作的，那么弄清楚为什么事情就是不工作，并且不像你所期望的那样精确，这可能是相当令人哭笑不得的。

下面是对`flex`属性- `flex-basis`、`flex-grow`和`flex-shrink`的一个小解释。

#### 弹性基础

`flex-basis`类似于`width`属性。它规定了元素在任何收缩或增长发生之前应该具有的`width`。如果`flex-grow`和`flex-shrink`被设置为`0`，该元素的`width`将等于`flex-basis`。

例如:

`flex: 0 0 200px;`会使元素总是有一个`width`的`200px`。如果*视窗*的宽度改变，它也不会缩小或增大。

在下面的钢笔中尝试一下:

[https://codepen.io/sargalias/embed/MZqYvR?height=600&default-tab=result&embed-version=2](https://codepen.io/sargalias/embed/MZqYvR?height=600&default-tab=result&embed-version=2)

#### 伸缩增长

如果有比元素的`flex-basis`更多的空间，那么它有增长的能力。`flex-grow`生效。因此，如果有*自由空间*，它将在`flex-grow`值高于`0`、**的元素之间按比例分配**。

例如，如果您有 3 个元素，在对所有元素应用了`flex-basis`之后，每个元素都有*的`flex-grow: 1;`和`300px`自由空间*，那么每个元素将获得`100px`额外宽度。

总的来说，如果你明白 flex-grow 只有在`flex-basis`确定后才生效，那么一切都按预期工作。

注意在下面的例子中，如果有可用的空间，项目是如何增长的，但是它们不会缩小到每个都小于`300px`宽(因为`flex-shrink`被设置为`0`，而`flex-basis`是`300px`)。

[https://codepen.io/sargalias/embed/gZdbXo?height=600&default-tab=result&embed-version=2](https://codepen.io/sargalias/embed/gZdbXo?height=600&default-tab=result&embed-version=2)

#### 伸缩

`flex-shrink`比较棘手。类似于`flex-grow`只在 `flex-basis`被分配后**才适用，`flex-shrink`也是如此。**

但是`flex-shrink` **并不按比例减少空间**，无论如何不是在所有情况下。

相反，`flex-shrink`收缩每个元素的空间，因此所有元素几乎同时达到它们的*最小内容*宽度(如果它们变得更小，它们的内容将溢出的宽度)。

例如，考虑下面的笔:

[https://codepen.io/sargalias/embed/JwadVZ?height=600&default-tab=result&embed-version=2](https://codepen.io/sargalias/embed/JwadVZ?height=600&default-tab=result&embed-version=2)

请注意，如果您调整*视窗*，`block-1`的收缩速度比`block-2`快得多，即使它们的`flex-shrink`值都是`1`。

它们不会像 flex-grow 那样按比例缩小，相反，有更多空间缩小的项目会更快地缩小尺寸**，这样所有项目都会稍微均匀地达到它们的 ***最小内容*宽度**。**

 **当然，改变它们的`flex-shrink`值确实会改变行为，但是请注意，事情并不完全成比例地缩小。

#### flexbox 中的边距

还要注意，在 *flexbox* 中使用`margin: auto;`可以很好地将项目居中。不仅如此，`margin-top`和`margin-bottom`就像`margin-left`和`margin-right`在 CSS 中的其他地方一样工作。相比之下，在正常的盒子模型中，顶部和底部的`margin: auto;`变为 0。

考虑下面的笔:

[https://codepen.io/sargalias/embed/ZVMGgJ?height=600&default-tab=result&embed-version=2](https://codepen.io/sargalias/embed/ZVMGgJ?height=600&default-tab=result&embed-version=2)

注意，仅仅通过使用*块*上的`margin: auto;`，它就完美地位于*柔性盒*的中心。

关于 *flexbox* 的更多信息，请参见 [MDN](https://developer.mozilla.org/) 上的指南。第一部分是[flexbox(MDN)](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Basic_Concepts_of_Flexbox)的基本概念，然后看侧边栏了解其余部分。

## 值得一提

### 边框不接受百分比值

正如标题所说，不能用带边框的百分比。这不仅适用于边框，也适用于方框阴影。

这可能会引起混淆，因为您可以使用百分比来表示内容大小(宽度和高度)、填充和边距，但不能使用边框。

记住就好。

### 响应性内嵌框架(和其他嵌入内容)保持它们的纵横比

不幸的是，现在这需要一个黑客用 CSS 来完成。

要了解如何做到这一点，见[纵横比框(CSS-Tricks)。](https://css-tricks.com/aspect-ratio-boxes/)

### Z 向指数

如果你不完全了解它的工作原理，事情可能会很复杂。

你应该知道的第一件事是元素的默认堆叠顺序。

1.  非定位块(静态定位)。
2.  浮动块。
3.  定位块(静态定位以外的任何内容)。

了解以上内容，`z-index`可以帮助我们进一步控制单个元素出现的位置。它是这样工作的:

首先，注意`z-index`只对*定位的*元素起作用。如果一个元素没有被*定位*，`z-index`将不起作用。

其次，`z-index`仅相对于其最近的*祖先*起作用，这形成了一个*堆栈上下文*。换句话说，一个元素永远不会低于它的*堆栈上下文*。

例如，根元素(`html`)总是形成一个堆栈上下文。一个元素永远不会堆叠在*根*元素之下，不管它的`z-index`。如果另一个元素形成了一个*堆栈上下文*，那么一个*子元素*将永远不会在它后面堆栈。

形成*堆栈上下文*的方式有很多种，最常见的方式可能是给一个元素一个`absolute`的`position`或`relative`以及一个`z-index`。

最后，只有`z-index`和*的元素如果共享同一个*堆栈上下文*，就会相互竞争*。

这可能很难理解第一轮。更详细的解释请参见[了解 CSS Z-index (MDN)](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Positioning/Understanding_z_index) 。

### 变换中的百分比与父容器无关

使用*盒模型*，百分比被赋予基于父的*实际值*。

例如，如果父级的宽度是 100 像素，而直接子级的宽度是父级的 10%，则它的宽度将是 10 像素。如果那个孩子相对于`left: 100%;`被定位，它将会在父节点的右边，在父节点的外面。

但是当使用*变换*时，百分比并不以同样的方式工作。**使用*转换*，百分比相对于元素本身，而不是其父元素。**

考虑下面的笔:

[https://codepen.io/sargalias/embed/XoBJLN?height=600&default-tab=result&embed-version=2](https://codepen.io/sargalias/embed/XoBJLN?height=600&default-tab=result&embed-version=2)

请注意，带有`transform: translateX(100%);`的元素不会在其父元素之外结束。这是因为本例中的`100%`是相对于元素本身的。它只向右移动其宽度的 100%，而不是其父级宽度的 100%。相比之下，相对定位的元素会像您所期望的那样工作，并在父元素之外结束。

有关转换的更多信息，请参见指南[使用 CSS 转换(MDN)](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transforms/Using_CSS_transforms)

### 继承

这是一个非常小的点，也是一个相当基本的概念，但是我想我应该在这里为我们当中的初学者提一下。简而言之，有些 CSS 属性是*继承*的，有些不是。

*继承属性*包括`font-family`和`color`这样的东西。这实际上意味着您可以在`body`元素上设置`font-family`，页面中的所有元素将*继承*并拥有相同的`font-family`。

其他属性大多不是*继承的*，比如`margin`和`padding`。它们必须在元素上单独设置。

理解这个概念是值得的，这样你就知道你是否必须显式地写一个声明或者你可以指望它被*继承*。

还有一些特殊的关键字，允许你显式地继承那些没有被继承的属性，也可以防止继承等等。

更多信息，请参见[级联和继承(MDN)](https://developer.mozilla.org/en-US/docs/Learn/CSS/Introduction_to_CSS/Cascade_and_inheritance) 。

## 何去何从

CSS 是一头狡猾的野兽。一方面它非常容易学习和使用，另一方面它有许多意想不到的情况和惊喜。

这篇文章提到了最重要和最常见的问题，也是你在职业生涯中最有可能反复遇到的问题。

当你在学习 CSS 的时候，我建议用这些作为一个清单来衡量你真正了解多少。要想成为最好的专业人士，你最好了解所有这些案例。

*旁注*:如果你知道为什么有些情况是这样的，特别是一些更意想不到的情况，比如利润崩溃和伸缩，那么请在评论中告诉我们！

祝你好运，好好学习！**