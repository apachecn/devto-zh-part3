# 使用 CSS 变量如何帮助我减少 JavaScript

> 原文：<https://dev.to/bholmesdev/how-using-css-variables-cut-down-on-my-javascript-dc4>

***注:**我的帖子灵感来自[这篇](https://css-tricks.com/keep-math-in-the-css/)来自才华横溢的克里斯·科伊尔关于 CSS 的力量`calc()`。绝对值得一读！*

如果你从 2018 年开始关注 web 开发，你可能会遇到 [CSS 自定义属性/变量](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)。它们已经成为应用程序中的新热点，甚至不仅仅是使用原始 CSS，因为它们提供了范围和级联，即使是像[风格的组件](https://www.styled-components.com/docs/basics)这样的新的 CSS-in-JS 解决方案也不能直接复制。

当我第一次了解 CSS 自定义属性时，我并没有给予它们公平的待遇，因为我已经成为了一个 CSS-in-JS 的狂热爱好者(请不要批评我😛)，但是我最近的项目要求我回到传统的样式表，因为使用了奇异的框架， [SvelteJS](https://svelte.dev) 。

起初，简单地声明样式是必要的，没有任何形式的变量似乎是可以管理的；每当需要基于代码进行自定义时，我可以快速生成一串内联 CSS 并将其添加到我的元素中，而不用太担心优化问题。然而，尽管网站看起来仍然很快，我的代码库却越来越不可读。回车:CSS 变量！

***旁注:**曾经有一段时间人们会在屋顶上大喊“他们不叫**变量**；它们是**自定义属性！**“谢天谢地，相关的 MDN 文档和通用术语已经可以称它们为变量了。所以两者都可以，但是“变量”在我看来更清晰一些*😊

## 那么 CSS 变量是如何工作的呢？

对于那些不熟悉的人来说，你可以在你选择的任何元素中声明一个变量，或者在`:root`选择器中声明一个变量，使它可以被全局访问。只要使用`--[property-name]`语法，CSS 就会把它作为一个变量……

```
:root {
    --global-var: 50px;
}
.some-class {
    --scoped-var: 4%;
} 
```

Enter fullscreen mode Exit fullscreen mode

…然后使用`var(--[property-name])`在子元素(或全局属性的任何元素)中使用这些变量。

```
.child-of .some-class {
    margin: var(--scoped-var);
} 
```

Enter fullscreen mode Exit fullscreen mode

这类似于 [CSS 属性](https://developer.mozilla.org/en-US/docs/Web/CSS/Attribute_selectors)的工作方式，除了 CSS 变量可以采用你选择的任何度量单位，然后用于定义任何 CSS 属性。这意味着您可以获得预处理器中的变量多年来一直提供的同样的灵活性，尽管有一个公认的笨拙的语法(嘿，那是为您准备的 CSS3🤷‍♀).

不太为人所知的是 CSS 变量是如何**无单位的。**这起初看起来没什么大不了的，但是它提供了一个很大的优势:与`calc()`结合，CSS 变量可以用来按设定的数量缩放属性。这对于重构自己的代码来说是非常宝贵的，因为这意味着我只需要几行 CSS 计算就可以用 JavaScript 重写我的 CSS 字符串构造。

## 我们来看一个例子

为了展示自定义属性的作用，我将从我构建的投资组合概念中提取一个逻辑片段。

目标很简单:我想要一个强调条在一个循环中循环通过一组渐变，从一个渐变到下一个渐变。这对于单个动画关键帧来说是可能的，尽管我有一个警告:页面上的其他元素使用了 CSS 无法复制的逻辑循环间隔，为了一致性，我想在我的强调条中使用相同的间隔。当然，这个时间间隔是使用`setInterval(...)`在 JavaScript 中定义的。每当回调函数被点击，一些 CSS 需要改变。这个间隔在父组件中设置，并在我的重音栏组件中访问(是的，我使用的是基于组件的框架)。

在深入这个例子之前，请注意这个项目是建立在 Svelte 之上的。这不会严重影响代码的可读性；只要接受较小的细节涉及一些神奇的✨

[![](img/1cdb22f899c5a5e88c6d374efe14ec5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EQU3SAX5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dlxut0dvc69sx06dzuzp.gif) 
*最终目标*

## 我的老办法

最初，我通过创建一个隐藏溢出的宽背景图像来循环渐变，然后在间隔的每个刻度移动背景位置。这给了背景变换颜色的错觉，尽管它实际上是通过一个大的渐变移动的。然而，这个背景位置需要大量的计算。

为了使跨几个组件的所有间隔跟踪简单，我跟踪了一个作为道具传递的`gradientIndex`变量。这个索引对应于我正在循环的渐变颜色列表，名为`GRADIENTS`。

然而，这意味着需要一些额外的逻辑来更新 CSS:每当`gradientIndex`改变时，需要构造一个新的 CSS 字符串来作为内联样式应用。因此，当`gradientIndex`属性改变时，我们需要找出一个生命周期方法来构造我们的字符串。在 Svelte 中，这是使用`afterUpdate`回调函数:
完成的

```
...
afterUpdate(() => {
  backgroundPosition = `${(100 / (GRADIENTS.length - 1)) * gradientIndex}%`;
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要通过从`GRADIENTS.length` :
中获取一个百分比来计算溢出的背景大小

```
const backgroundSize = `${GRADIENTS.length * 200}% 100%`; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们把它放在我们构建的线性渐变背景旁边的内嵌样式中:

```
<span
  class="bar"
  style="background-image: {backgroundImage};
  background-position: {backgroundPosition};
  background-size: {backgroundSize}"
></span> 
```

Enter fullscreen mode Exit fullscreen mode

所以是的，最终结果运行得很好，没有任何性能问题...在我的功率过大的 MacBook 上😛然而，我们增加了相当多的复杂性，随着我们的扩展，这只会变得更糟。我们添加了一个生命周期方法来处理我们的内联 CSS 构造，并且我们在 JavaScript 中添加了一些变量，这些变量最好保持在它们所属的样式中。如果有一种方法只用 CSS 来计算就好了！

## 更具可读性的新解决方案

那么我们如何使用 CSS 变量来解决这个问题呢？嗯，看看 JS 中构造的背景位置字符串，我们看到计算需要知道有多少个梯度(`GRADIENTS.length`)和当前索引来计算位置(`gradientIndex`)。那么，为什么不直接制作这些 CSS 变量呢？

幸运的是，CSS 变量可以像其他 CSS 属性一样使用内联样式进行设置(SASS 中的变量就不一样了！).所以，假设我们前面提到的两个变量都是组件状态的一部分。我们可以使用下面的内联样式使它们对 CSS 可见:

```
<span
  class="bar"
  style="background-image: {backgroundImage};
  --index: {gradientIndex};
  --length: {gradientLength}"
></span> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以通过使用`calc()` :
来确定我们的背景尺寸和在 CSS 中的位置

```
.bar {
  --index: 0;
  --length: 0;
  background-size: calc(var(--length) * 200%) 100%;
  background-position: calc((100 / (var(--length) - 1)) * var(
  --index) * 1%);
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有几样东西要打开。首先，为了完整起见，我们将每个变量设置为某个初始值。尽管初始化 CSS 变量是一个需要养成的好习惯，但这并不是必须的，因为内联样式应该总是被应用。接下来，我们设置类似于 JS 的背景位置，有一个显著的不同:我们将`--index`乘以一个百分比，而不是直接在变量后面写百分号。这是因为`calc()`将变量视为数学中的常数，因此它必须乘以某个值才能成为适用的度量单位。

哦，这是我们新的 JS 片段:
…等等，已经没有了！🎉

### 我们还能更深入吗？

这个例子没有利用的是变量级联。这对于基于组件的开发非常有用，因为您可以将许多古怪的 CSS 计算合并到父组件中。然后，子组件可以从级联中更高的位置访问 CSS 变量。在我们的例子中，我们可以让`gradientIndex`成为包围颜色条的父对象中的一个 CSS 变量，并避免将其完全作为道具传递！

当然，这可能会对可读性产生负面影响，几个级别以上的变量会向下级联，而开发人员不会意识到这一点。这暴露了级联思维和基于组件的思维之间由来已久的冲突，所以要谨慎使用这种技术。

## 包装完毕

至此，应该很清楚，自定义属性可以非常强大地将 JavaScript 逻辑转换到样式表中。此外，现在 CSS 变量与大多数现代浏览器兼容(当然 IE 除外😢)，即使在生产代码中进行试验也应该是相当安全的。因此，向前迈进，开始造型！

## 学点小东西？

太好了。如果你错过了，我发布了一个[我的“网络魔法”时事通讯](https://tinyletter.com/bholmesdev)来探索更多像这样的知识金块！

这个东西解决了 web 开发的[【首要原则】](https://www.swyx.io/first-principles-approach/)。换句话说，是什么让我们所有的 web 项目运转起来的所有 janky 浏览器 API、弯曲的 CSS 规则和半可访问的 HTML？如果你正在寻找超越框架的*，这是给你亲爱的网络巫师的🔮*

 *[立即在此订阅](https://tinyletter.com/bholmesdev)。我保证永远教，永远不会垃圾邮件❤️*