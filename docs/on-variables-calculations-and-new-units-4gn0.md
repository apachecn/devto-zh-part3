# 关于变量、计算和新单位

> 原文：<https://dev.to/trezy/on-variables-calculations-and-new-units-4gn0>

**本文最初发表于 2012 年 12 月 15 日的[Trezy.com](https://trezy.com/blog/on-variables-calculations-and-new-units)。**

*免责声明:*好吧，别太激动。这东西根本不是向后兼容的*。然而，有后备的解决方案，而且它实际上被规范了，这真的很酷。*

 *在 [LESS](https://lesscss.org) 和 [SASS](https://sass-lang.org) 出现之前，网页设计师多年来一直在乞求 CSS 添加变量和计算，让我们能够随心所欲地使用这些闪亮的新玩具。然而，SASS 和 LESS output 有时会使你的 CSS 页面膨胀，虽然玩起来很有趣，但每次修改后都要重新编译 CSS 是很痛苦的。幸运的是，W3C 已经像众所周知的保姆一样进来了，开始给他们一点颜色看看，让他们焕然一新。

## 变量

Mmmmmmmmm，变量。我们几乎在每一种语言中都使用过它们，除了 CSS……直到现在。诚然，CSS 变量还有很长的路要走。它们仍然在 W3C 级模块中，所以你暂时不能使用它们，除非你想进入 [Webkit 夜间构建](https://nightly.webkit.org)。尽管如此，想想一年后我们将和他们一起做的所有令人惊奇的事情还是很有趣的…

下面是 CSS3 中变量的样子。请记住，因为它仍然是第 1 级，它可能会发生变化。要声明一个变量，您将在一个元素中创建它。所有的例子都在`:root{}`中使用前缀`var`声明它们，就像这样:

```
:root {
 var-mainColor: #0c0;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以使用`var()`语法:
在代码中调用这些变量

```
p {
 background-color: var(mainColor);
} 
```

Enter fullscreen mode Exit fullscreen mode

我在 CSS 中的主要用途是处理品牌颜色。我可以在样式表的顶部设置所有的品牌颜色，并在整个代码中使用这些变量，就像这样:

```
:root {
 var-primaryColor: #0d56a6;
 var-secondaryColor: #4186d3;
 var-tertiaryColor: #689ad3;
}
header {
 background-color: var(primaryColor);
}
header > nav {
 background-color: var(secondaryColor);
}
article {
 background-color: var(tertiaryColor);
}
button {
 background-image:
   linear-gradient(
     bottom,
     var(primaryColor) 0,
     var(secondaryColor) 50%,
     var(tertiaryColor) 100%
   );
} 
```

Enter fullscreen mode Exit fullscreen mode

从现在开始，如果我想改变配色方案，我只需改变我的`:root`元素中的颜色，它们就会改变整个网站！然而，我确实希望在规范发布之前，我们能够创建像 SASS mixins 一样工作的完整属性集变量。这样做的能力将是惊人的:

```
var gradient (color1, color2, color3) {
 background-image:
   linear-gradient(
     bottom,
     var(color1) 0,
     var(color2) 50%,
     var(color3) 100%
   );
}
article {
 gradient(#0d56a6, #4186d3, #689ad3);
 border: 1px solid #000;
} 
```

Enter fullscreen mode Exit fullscreen mode

[W3C 模块](http://dev.w3.org/csswg/css-variables/)

## 计算

计算是在 W3C Level 3 模块中进行的，这意味着它们非常接近于成为 CSS3 的完整规范。如果一切顺利，明年六月我们就可以认为它们完全可以安全使用了。然而，我们已经可以在最新版本的 Chrome、Firefox、Opera 和 Safari 以及 IE9/10 中使用它们。

那么，为什么计算如此令人兴奋呢？这个水平居中怎么样:

```
body {
 margin-left: calc(960px / -2);
 width: 960px;
} 
```

Enter fullscreen mode Exit fullscreen mode

[MDN 页面](https://developer.mozilla.org/en-US/docs/CSS/calc) | [W3C 模块](https://developer.mozilla.org/en-US/docs/CSS/calc)

## 单位

CSS3 中有一些非常时髦的新单位，但我最感兴趣的是`rem`、`vw`、`vh`、`vmin`和`vmax`。

### 雷姆

我们都知道`em`，我们被鼓励使用它来放大页面浏览。然而，我很长一段时间都避免使用它们，因为试图计算它们是一件很麻烦的事情。我不喜欢计算我的 em 值是基于它的父母计算的值，基于它的父母计算的值，基于它的父母计算的值，这是…这太糟糕了。然而，`rem`就是来拯救世界的！除了总是与页面根的`em`值相关之外，`rem`与`em`类似。取此代码:

```
:root {
 font-size: 20px;
}
.box1 {
 font-size: 2em;
}
.box1 > .box2 {
 font-size: 2em;
}
.box3 {
 font-size: 3rem;
}
.box3 > .box4 {
 font-size: 1rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`.box2`将具有 80px 的字体大小(20px 的 2em = 40px，然后 20px 的 2em 的 2em = 80px)，但是`.box4`将具有 20px 的字体大小(如果 20px 的 1em = 20px，则 box3 将是 20px 的 3em，`.box4`)。实际上比这还要复杂，但这是解决所有问题的一般方法。毫无疑问，s 比`em` s 更容易使用。

### vw 和 vh

更棒的是基于视口大小计算的单位！`vw`和`vh`分别是视口宽度和高度的百分比。每个`vw` / `vh`是视口大小的 1%，所以想想这个:

```
nav {
 width: 100vw;
}
nav a {
 min-width: 100px;
 width: 20vw;
} 
```

Enter fullscreen mode Exit fullscreen mode

这样你就可以创建一个包含 5 个元素的导航条，它的大小总是和视窗的大小一样。此外,`min-width`还确保链接不会太小，这样你就可以把它们堆叠起来，或者把它们变成超级小屏幕的垂直菜单。

### vmin 和 vmax

这些家伙总是相对于`vh`和`vw`之间的较大或较小的尺寸。换句话说，如果视窗的宽度小于高度(可能是纵向模式的智能手机)，那么`vmin`将与宽度相关，而`vmax`将与高度相关。

[W3C 模块](http://www.w3.org/TR/css3-values/)*