# 对::before 和::after 伪元素的冷静使用

> 原文：<https://dev.to/kball/cool-uses-of-the-before-and-after-pseudoelements-2l0b>

我有一个坦白:我有点喜欢`::before`和`::after`伪元素。

他们就像是任何时尚冒险中的一对追随者。

[![Wolfpack as .wolf with ::before and ::after](img/e457d9ed83c6109431dd403928ef1a18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EzcXlEJ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zendev.com/assets/img/posts/before-and-after/henchmen.jpg) 喜欢永远不必独自面对世界——因为以前到处都是 1，现在却是 3。

3 倍的功率。3 倍的可造型件数。三倍于你所能创造的惊人视觉效果。

## 什么是伪元素？

现在，如果你以前从未遇到过`::before`和`::after`，你可能会问“你说的这是什么::before 伪元素？”

以下是 30 秒概述:

伪元素是“假装”元素，可以通过使用 CSS 将其插入到文档中。例如，如果我要写一个非常简单的 CSS:

```
p::before {
  content: "Hello!"
} 
```

突然之间，我页面上的每个元素都会在其内容前插入单词“Hello”。`::after`伪元素是类似的，但是它是针对内容的。

属性设置它们在里面显示什么，但是这些元素完全可以像其他元素一样进行样式化，所以你实际上不需要在里面有文本内容。事实上，许多最有趣的用途完全是可视化的。

如果你想要更全面的介绍，我推荐 SmashingMagazine 上的这篇文章。在这篇文章中，我将较少地关注伪元素是如何工作的，而更多地关注一系列令人惊叹的例子。

也就是说，我想强调一个小问题，因为它已经困扰了我很多次。我们将探索的许多用途涉及图形，但没有实际的文字内容。这很好，但是您*必须*设置两个`content`属性，如果您的内容为空，则`display`属性或伪元素将不会显示。我已经被这个咬了很多次了…所以永远记得从设置内容开始。如果你的使用完全是可视化的，只需设置:

```
content: ""
display: inline-block 
```

## 简单用法为::before 和::after

先说一下`::before`和`::after`的一些简单用法。

### 自定义图标

::before 的一个简单、常见但非常有用的用法是向具有特定类的元素添加图标。例如， [FontAwesome](https://fontawesome.com/) 使用这种技术来实现它的图标。你可以用同样的方法来定制你自己的图标。

现在，FontAwesome 通过一个自定义字体和`content`属性来实现这一点，它的好处是可以根据它所在的元素自动调整大小。如果我们想使用一个图像，我们将需要显式地调整它的大小，否则 seudoelement 的大小将为 0 并且不可见。

例如，如果我想让页面上带有`.kball`类的每个元素显示一个 30x30 的圆圈，并带有我的个人资料图片，我可以这样做:

参见[码笔](https://codepen.io)上凯文·鲍尔( [@kball](https://codepen.io/kball) )的笔 [qvQJNG](https://codepen.io/kball/pen/qvQJNG/) 。

### 引号

`::before`和`::after`的另一个重要用途是在引号周围添加引号。例如，我们可以创建一个在前面和后面应用双引号的“quote”类，甚至可以创建一个前置 m-dash 的属性类:

参见[码笔](https://codepen.io)上凯文·鲍尔( [@kball](https://codepen.io/kball) )在前后使用的笔[引号。](https://codepen.io/kball/pen/ywrNvg/)

我们还可以讨论很多其他的常见用途，但是我想继续讨论一些更激动人心的东西。如果你想要更多这样的，看看这篇文章。

## 前后的高级用法

### 制作形状

使用 HTML 和 CSS 的挑战之一是你必须处理的“基本”形状数量有限。

本质上，每个元素都是一个矩形……然后通过巧妙使用边框半径和边框，你可以将矩形变成椭圆形或圆形，倾斜各个角的角度，或者[用它创建一个三角形](https://css-tricks.com/snippets/css/css-triangle/)。你当然可以在使用完`transform`后旋转它，但仅此而已…

但是现在有了`::before`和`::after`，你就有 3 倍多的“积木”可以玩了。每一个都可以变换、单独定位和旋转。这导致了你可以用纯 CSS 创建的形状的惊人选择。

CSS Tricks 的 Chris Coyier 整理了一个很棒的可能形状列表[这里](https://css-tricks.com/the-shapes-of-css/)，我用 ZenDev.com 的配色方案在下面复制了我最喜欢的形状。信不信由你，这只是 1 个 HTML 元素！

在 [CodePen](https://codepen.io) 上看到凯文·鲍尔( [@kball](https://codepen.io/kball) )的钢笔[亚历山大·福特科夫《蓝色阴阳》](https://codepen.io/kball/pen/eXoNay/)。

### 显示属性值

::before 或::after 伪元素的另一个超级酷的用法是显示属性的内容。

您可以通过将`content`设置为包含`attr([attributename])`来做到这一点

例如，如果您正在使用一个 JavaScript 驱动的小部件，它将某种值存储为属性，那么您实际上可以自动显示该属性，而不必使用任何 JavaScript 来查看它！

我以前用过的一个地方是 ZURB 基金会滑块。滑块的工作原理是改变 hiddeninput 中的值…但是如果我们想让当前值在滑块本身上可见呢？被操作的句柄只是一个 span…但是它获得了当前值`aria-valuenow`的 aria 属性。通过在带有`content: attr(aria-valuenow)`的伪元素中引用它，我们可以在句柄上显示它。

在 [CodePen](https://codepen.io) 上看到凯文·鲍尔( [@kball](https://codepen.io/kball) )用::before 显示滑块值的笔[。](https://codepen.io/kball/pen/bZPLZy/)

### 多级动画

我见过的最有趣的应用之一是创建多阶段动画。

这些又一次利用了伪元素为您提供了在模拟中操作的全部附加元素这一事实。您可以在一个方向上设置核心元素的动画，然后单独设置每个伪元素的动画。

例如，[这篇文章](https://cssanimation.rocks/pseudo-elements/)展示了如何在悬停的按钮上创建一个奇特的“光泽”效果(如下面的钢笔所示)。

请看凯文·鲍尔( [@kball](https://codepen.io/kball) )在 [CodePen](https://codepen.io) 上的笔[光泽按钮效果](https://codepen.io/kball/pen/wOLmKV/)。

我们可以用它做各种有趣的事情。来创造超酷的微互动。另一个例子是在视觉上隐藏单词内容，并在点击或悬停时旋转。

见[码笔](https://codepen.io)上凯文·鲍尔( [@kball](https://codepen.io/kball) )按钮效果中的笔[字。](https://codepen.io/kball/pen/MxMVeb/)

## 包装完毕

这些伪元素能做的事情太多了，很难面面俱到。这些是我觉得特别有趣的一些例子——你可能也会喜欢[这个关于社交技巧的综述](https://css-tricks.com/pseudo-element-roundup/)——如果你有任何让你真正兴奋的例子，请在下面的评论中给我链接！

* * *

如果你关心前端开发，你可能也会对我的每周时事通讯《星期五前端》感兴趣。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:[https://zendev.com/friday-frontend.html](https://zendev.com/friday-frontend.html)