# 在下拉菜单转换时

> 原文：<https://dev.to/trezy/on-dropdown-menu-transitions-2bp2>

**本文最初发表于 2014 年 3 月 1 日的[Trezy.com](https://trezy.com/blog/on-dropdown-menu-transitions)。**

CSS 过渡属性允许在你的设计中有很多非常酷的效果，但是它确实让我有几次把头撞到桌子上。以下是一些没有很好记录的关于转换属性的细节。

*免责声明:*在撰写本文时，过渡仍然需要浏览器前缀才能适用于 Firefox ( `-moz-`)、Opera ( `-o-`)和 Webkit ( `-webkit-`)。为了可读性，我在这里的代码示例中省略了它们，但是如果您打算使用这些技术，它们将是必需的。

## 其长其短

转换可以被分解成几个不同的属性，或者以它的简写形式使用。你甚至可以根据自己的喜好进行混搭。现有的不同转换属性有:

*   [T2`transition`](https://developer.mozilla.org/en-US/docs/CSS/transition)
*   [T2`transition-property`](https://developer.mozilla.org/en-US/docs/CSS/transition-property)
*   [T2`transition-duration`](https://developer.mozilla.org/en-US/docs/CSS/transition-duration)
*   [T2`transition-timing-function`](https://developer.mozilla.org/en-US/docs/CSS/transition-timing-function)
*   [T2`transition-delay`](https://developer.mozilla.org/en-US/docs/CSS/transition-delay)

每个手写属性也有自己的简写形式:

```
div {
 background-color: #2f2;
 color: #090;
 transition-property: background-color, color;
 transition-duration: 1s, .5s;
 transition-timing-function: ease, steps(4,start);
 transition-delay: 0s, .5s;
}

div:hover {
 background-color: #fff;
 color: #000;
} 
```

Enter fullscreen mode Exit fullscreen mode

请记住，每个属性不必有多个值。如果我们将`transition-duration: 1s, .5s;`改为`transition-duration: 1s;`，那么两个过渡将使用相同的持续时间值。就我个人而言，我更喜欢速记属性，因为它看起来更干净，需要的代码更少:

```
transition: <transition-property> | <transition-duration> | <transition-timing-function> | <transition-delay>; 
```

Enter fullscreen mode Exit fullscreen mode

在速记版本中,`timing-function`和`delay`都是可选的。这里有一个例子:

```
div {
 background-color: #2f2;
 color: #090;
 transition-property:
   background-color 1s,
   color .5s;
}

div:hover {
 background-color: #fff;
 color: #000;
} 
```

Enter fullscreen mode Exit fullscreen mode

这两个代码块将创建相同的功能，所以这完全是个人喜好的问题。对于`transition-property`需要注意的最后一点是，如果除了您想要动画化的属性之外，您不改变状态之间的任何东西，您可以使用`all`来代替属性列表。

## 转场双向摇摆

直到我读了路易斯·拉撒路的这篇文章，我才意识到我可以为不同的状态使用不同的过渡。如果一个转换只应用于一个对象的一个状态，就像上面的代码示例一样，它将在`:hover`(或者你选择的任何状态)激活，并在状态变回来时反转。但是，如果每个状态都有不同的转换属性，它将应用于即将到来的转换。例如:

```
div:not(.menu):before {
 background-color: #ddd;
 border-radius: 10px;
 color: #333;
 content: 'Hover me to see the menu';
 padding: 10px;
}

div .menu {
 background: #88b;
 max-height: 0;
 -webkit-transition:
   max-height .5s ease .5s,
   max-width .5s ease 0s;
 max-width: 10px;
 overflow: hidden;
}

div .menu a {
 color: #fff;
 text-decoration: none;
}

div:hover .menu {
 max-height: 999px;
 -webkit-transition:
   max-height .5s ease 0s,
   max-width .5s ease .5s;
 max-width: 999px;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/trezy/embed/mvaLt?height=600&default-tab=result&embed-version=2](https://codepen.io/trezy/embed/mvaLt?height=600&default-tab=result&embed-version=2)

在这个例子中，当您将鼠标放在`.menu`的父 div 上时，它的第一个转换将来自于`div:hover .menu`规则。首先，它会垂直生长，然后它会水平生长。当你将鼠标从父 div(或菜单)上移开时，`.menu`将从`div .menu`规则开始转换，首先水平收缩，然后垂直增长。有点迷惑但是超级爽！

## 一切都在时机

flash 开发人员和视频编辑人员会很熟悉这些按钮，他们可以定制过渡、定时和其他效果，但对于大多数网页设计人员来说，它们可能是一个陌生的概念。这些函数在 [MDN](https://developer.mozilla.org/en-US/docs/CSS/timing-function) 和 [W3C 规范](http://dev.w3.org/csswg/css-transitions/#transition-timing-function)中已经有了很好的描述，所以我在这里只简单描述一下。

### `ease`

这是`timing-function`的默认设置，所以除非你使用带有多个属性的手写`transition-timing-function`，否则没有必要声明它。它将缓和动画的入点和出点，入点的曲线比出点的曲线稍微尖锐一些。基本上是中间比开头或者结尾快，开头加速比结尾快一点。

### `ease-in`

这与`ease`相似，但它有一个较长的入点曲线(较慢的加速度)和一个急停。

### `ease-out`

与`ease-in`相反，这有一个更长的出点曲线(慢…慢？).基本上它是突然开始的，当它接近尾声时会慢下来。

### `ease-in-out`

我认为这个函数的存在很奇怪，因为我们已经有了基本的`ease`函数。但是，有一点小小的不同。`ease-in-out`实际上有一个相等的内外曲线，而`ease`有一个稍微尖锐的内曲线。实际的动画将有一个同样缓慢的开始和结束。

### `linear`

这个函数从头到尾都是一条直线。这意味着它会有一个突然开始和停止不变的速度。

### [T3`step-start`/`step-end`](#-raw-stepstart-endraw-raw-stepend-endraw-)

这两个函数的目的是什么，我不知道。我确信它们在某个地方有它们的用处。使用`step-start`，它将在持续时间开始时切换过渡，然后在持续时间结束前什么也不做。`step-end`函数则相反，等待持续时间结束，然后突然应用更改。这些根本不是平稳的过渡，而是带有内置延迟的变化。

### `steps(number_of_steps, direction)`

与前面的阶跃函数相比，`steps()`更有用，它允许您在过渡期间将动画分布在几个突然的变化上。这里有一个例子:

```
a {
 transition: steps(4, start) 1s;
 width: 100px;
}

a:hover {
 width: 200px;
} 
```

Enter fullscreen mode Exit fullscreen mode

当悬停在此链接上时，它会立即从 100 像素递增到 200 像素，而不是平滑过渡。这些是字面上的立即转换，类似于根本没有转换。`direction`(本例中为`start`)与`step-start`和`step-end`的后缀相同，决定了变化是从每一步的开始还是结束开始。`direction`的默认值是`end`，但该参数不是可选的，因此`steps(2)`将不起作用。

### `cubic-bezier(x1, y1, x2, y2)`

这是所有`timing-function`之母，让你最终控制你转变的时机。除了`steps()`之外的所有其他功能都可以使用`cubic-bezier()`来定义。然而，这可能非常复杂，所以我将让您自行处理。我强烈推荐查看关于它的 [MDN](https://developer.mozilla.org/en-US/docs/CSS/timing-function#The_cubic-bezier%28%29_class_of_timing-functions) 文章，里面有非常精彩的解释。

## 结论

谈传播信息，这些狗都是熟的！我说的狗是指手指。不是说我有巨大的手指什么的，只是…你是在说我胖吗？任何人，如果你有任何问题或者你想出了一些超级棒的过渡组合，请留下评论。