# 仅 CSS 黑暗模式

> 原文：<https://dev.to/kleinfreund/css-only-dark-mode-3eg8>

最近，我无意中发现了慕安池的一个很酷的小网站，并受到启发重新制作了自己的网站。但是首先，我对她整洁的无 JavaScript 黑暗模式实现做了一些查看。让我们看一看，我指的是详细的分析。从那里，我们将逐步实现我们自己的黑暗模式。

(本文于 2019 年 4 月 8 日首次发表于我的博客:[纯 CSS 黑暗模式](https://kleinfreund.de/css-only-dark-mode/)。)

她网站顶部的复选框启用了黑暗模式。本质上，它交换了一些颜色，这样在启用深色模式后，浅色文本位于深色背景上，而之前正文文本是深色的，背景是浅色的。

一点 JavaScript 用于通过 [`localStorage` API](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) 存储用户的选择。这只是渐进增强的一种形式，并不是切换到黑暗模式的功能所必需的。

我们在本文中构建的仅支持 CSS 的黑暗模式可以在 CodePen 上获得。

### 界面设计

从界面设计的角度考虑交互元素的选择。目的是在两种状态之间切换，黑暗模式禁用和黑暗模式启用。也可以跨会话保存该状态。适用于此类任务的可用组件有复选框、开关和开关。三者都允许正确的交互形式，其中初始状态是“黑暗模式，未启用”，并且通过与控件交互，状态被改变为“黑暗模式，已启用”。

更准确地说，对于复选框或开关，初始状态是“黑暗模式，未选中”，对于切换按钮，初始状态是“黑暗模式，未按下”。支持相关功能的屏幕阅读器(如`role="switch"`或`aria-pressed`)会相应地宣布控件的类型。关于切换按钮和开关主题的更多信息[可以在 Heydon Pickering 关于包容性组件的精彩资源中找到。](https://inclusive-components.design/toggle-button/)

请注意，没有一个控件处理光模式的概念。这意味着如果控件没有被交互，则黑暗模式被禁用；因此，当前模式必须是灯光模式。当然，任何比黑暗模式更亮的模式都符合要求。

在不同的上下文中，许多控件是更复杂的小部件(如设置页面)的一部分，还必须考虑用户在与这些控件交互时的期望。特别是，由于启用黑暗模式的选择应该被保存，我们必须询问*这样的交互在什么时候被保存。需要点击“保存”按钮来确认更改后的设置吗，还是会自动保存？*

zobijl 最近提供了一个关于我们讨论的三个控件何时保存它们的状态的快速概要。他们还负责[前一节中链接文本](https://twitter.com/ZoeBijl/status/1114095351626178560)的选择👍。

### 设计目标&约束条件

我假设 Mu-An 选择复选框只有一个原因:开关和切换都需要 JavaScript 才能工作；因此，不可能为禁用 JavaScript 的用户提供黑暗模式。然而，对于纯 CSS 实现，有必要观察用户在样式表中与页面的交互。让我们来看看一些选项。

有一系列 CSS 伪类选择器允许您对用户交互做出反应。例如，`:hover`伪类选择器在一个元素被悬停时被触发。然而，如果黑暗模式只在用户悬停在某个元素上时才激活，那么它就没有多大用处。对用户与 CSS 的交互做出反应的一种更持久的方式是 [`:target`选择器](https://developer.mozilla.org/en-US/docs/Web/CSS/:target)。然而，这个特性只观察与包含片段标识符的 URL 的页面导航相关的交互。启用黑暗模式只有在不使用同一窗口中打开的其他链接时才会持续。不实用。

唯一合理的选择是 [`:checked`伪类选择器](https://developer.mozilla.org/en-US/docs/Web/CSS/:checked)，它允许根据复选框(或单选按钮)的选中状态来设计其样式。复选框碰巧也是适合我们目的的可能的交互组件之一。因此，使用复选框和`:checked`选择器进行主题化是一个合适的选择，而*不是*是一个黑客。

### 级联和分支跳跃

在我们研究 web 页面的实际主题化之前，我将后退一步，详细说明 CSS 及其选择器的工作方式对 web 开发人员的一些技术限制。

还记得 CSS 中的“C”是如何代表“层叠”的吗？下面是对它的一种描述:CSS 规则集的某些方面会影响它所应用到的 DOM 节点**，并可能影响该节点**的所有后代 DOM 节点。以下面的标记为例:

```
<label for="coolbox">
  <input id="coolbox" type="checkbox">
  Subscribe to <i>very</i> important newsletter
</label> 
```

设置标签样式可能会影响其后代:

```
label {
  margin: 1.5rem;
  color: tomato;
} 
```

如果您已经编写了一段时间的 CSS，您可能会在这里做出一个重要的区分:您希望`color`属性应用于整个标签*及其所有后代*，但是您希望`margin`属性应用于标签*及其所有后代*。特别是，你不会期望`i`元素突然在它的边上有一个空白边，当然它必须被着色`tomato`。那是瀑布。一些 CSS 属性由元素从它们的祖先元素继承而来(例如，`i`元素从`label`元素继承了`color`属性。)而有些不是(比如`margin`属性)。

这意味着级联允许应用于一个元素的 CSS 规则影响它的后代元素。继承只能在 DOM 树中进行。元素的同级或祖先元素不能从该元素继承属性。但是仍然会有某种形式的依赖。

输入一般(`~`)和相邻(`+`)兄弟组合子。

```
input ~ i {
  color: cornflowerblue;
} 
```

以前，使用上面的 CSS，整个标签将被着色`tomato`。现在，任何`input`元素之后的所有`i`元素都将被着色为`cornflowerblue`。这里的“Following”意味着它们在 DOM 中处于同一级别。DOM 片段目前看起来像这样(省略了一些只有空白的节点):

*   `label`
    *   `input`
    *   #正文:`Subscribe to`
    *   `i`
        *   #正文:`very`
    *   #正文:`important newsletter`

这里，带有`i`元素的节点和三个文本节点中的两个“跟随”了`input`元素。包含字符串“very”的第三个文本节点与其他节点不在同一级别上；因此，它不是下一个(或后续)节点。

还记得我们如何满足于复选框，因为有一个`:checked`伪类选择器吗？当复选框被选中时，`input:checked`选择器可用于设置特定于选中状态的样式。同样，`input:not(:checked)`也可以用来设计一个没有被选中的复选框。让我们将一般的兄弟组合符与`:checked`伪类选择器结合起来:

```
input:checked ~ i {
  color: cornflowerblue;
  font-weight: bold;
} 
```

我不知道你怎么想，但我认为这非常酷。选中该复选框会更改不是后代而是同级的 DOM 节点的属性🤯！这个事实就是慕安的纯 CSS 黑暗模式的基础。

### 基本标记

我们需要一个复选框和该复选框的*后续兄弟*元素，以便我们可以通过`:checked`伪类选择器改变兄弟元素。

```
<input class="dark-mode-checkbox" id="dark-mode" type="checkbox">

<div class="theme-container grow">
  <label class="dark-mode-label" for="dark-mode">
    Dark mode
  </label>

  Put all your content in here.
</div> 
```

首先，奇怪的部分。不可否认，让标签既不是`input`元素的父元素也不是它的兄弟元素让人感觉很尴尬。我从未有过这样做的用例。重要的是标签通过`for`属性与控件相关联。请始终这样做，以确保辅助技术可以用标签文本通知控件。即使你把`input`放在`label`元素中，也不能保证所有的辅助技术都能生成正确的标签。

顾名思义，`div.theme-container`将用于切换黑暗模式的颜色。因为标签是它的一部分，所以我们不需要为此目的明确地指向它。这就是我将标签放在主题容器中的原因。如果这让你感到困扰，你可以把它移动到`input`元素之后。

### 用自定义属性进行主题化

没错，CSS 自定义属性🤗。我们将利用级联和所有自定义属性都被继承的事实。下面的 CSS 展示了如何基于复选框的选中状态，覆盖一组负责颜色主题的自定义属性。

```
:root {
  /* Light theme */
  --c-light-text: #333;
  --c-light-background: #fff;
  --c-light-focus: deepskyblue;
  --c-light-interactive: mediumvioletred;

  /* Dark theme */
  --c-dark-text: #fff;
  --c-dark-background: #333;
  --c-dark-focus: deeppink;
  --c-dark-interactive: palegreen;
}

.theme-container {
  /* Make the light theme the default */
  --c-text: var(--c-light-text);
  --c-background: var(--c-light-background);
  --c-focus: var(--c-light-focus);
  --c-interactive: var(--c-light-interactive);

  color: var(--c-text);
  background-color: var(--c-background);
}

.dark-mode-checkbox:checked ~ .theme-container {
  /* Override the default theme */
  --c-text: var(--c-dark-text);
  --c-background: var(--c-dark-background);
  --c-focus: var(--c-dark-focus);
  --c-interactive: var(--c-dark-interactive);
}

:focus,
.dark-mode-checkbox:focus ~ .theme-container .dark-mode-label {
  outline: 2px solid var(--c-focus);
}

a {
  color: var(--c-interactive);
} 
```

请注意，主题特定的定制属性仅在使用`:checked`伪类选择器切换主题时使用。通过将它们分配给一组不特定于主题的通用自定义属性*，我们避免了大量 CSS 的重复。如果没有自定义属性，我们将需要编写两次涉及主题化的每个属性声明。*

计算机科学家可能会称这种技术为依赖注入。我想。也许吧。不就是这样吗？

链接的规则集用一个通用自定义属性设置了`color`属性。那个自定义属性的值本身就是一个自定义属性，一个特定于主题的属性，而那个属性的值是在选中复选框时被改变的。我觉得那很美。

### 将复选框移动到暗面

目前，复选框和它的标签在视觉上没有关联。此外，复选框不受我们的主题影响。对于仅使用 CSS 的黑暗模式来说，这是一个不幸的折衷。我们将在标签旁边提供一个替代框，并在视觉上隐藏原件。

```
<input class="dark-mode-checkbox visually-hidden" id="dark-mode" type="checkbox">

<!-- … --> 
```

```
.dark-mode-label::before {
  content: "\2610";
}

.dark-mode-checkbox:checked ~ .theme-container .dark-mode-label::before {
  content: "\2611";
}

/*
visibility-hidden utility class

Source: https://github.com/h5bp/html5-boilerplate

Hide only visually, but have it available for screen readers:
https://snook.ca/archives/html_and_css/hiding-content-for-accessibility

1\. For long content, line feeds are not interpreted as spaces
   and small width causes content to wrap 1 word per line:
   https://medium.com/@jessebeach/beware-smushed-off-screen-accessible-text-5952a4c2cbfe
*/
.visually-hidden {
  position: absolute;
  overflow: hidden;
  clip: rect(0 0 0 0);
  width: 1px;
  height: 1px;
  margin: -1px;
  padding: 0;
  border: 0;
  white-space: nowrap; /* 1\. */
} 
```

**注意**:我们不想对辅助技术隐藏黑暗模式切换器。一些用户可能使用屏幕阅读器，但实际上可能是盲人；其他人可能只是部分失明或根本不失明。

### 覆盖整个视口

您可能会注意到深色主题没有用深色背景色覆盖整个视口。在我们使用 flexbox 之前，这个问题很难解决。现在，搜索“CSS 粘性页脚”应该会给你多种方法来解决这个问题。这里有一个:

```
<!-- … -->

<div class="theme-container grow">
  <!-- … -->
</div> 
```

```
/*
1\. Allows the body’s children
   to grow to 100%
   of the viewport’s height.
*/
html,
body {
  height: 100%; /* 1\. */
}

/*
1\. Allows the content area
   to grow to the viewport height.
*/
body {
  display: flex; /* 1\. */
  flex-direction: column; /* 1\. */
}

/*
1\. Grows the content area
   to take up all the remaining height
   inside the body element.
*/
.grow {
  flex-grow: 1; /* 1\. */
} 
```

### 存储用户的偏好

只需要几行 JavaScript 就可以在用户浏览器中保存当前模式。当他们再次访问该页面时，将使用他们上次访问时选择的模式。

```
document.addEventListener('DOMContentLoaded', function () {
  const checkbox = document.querySelector('.dark-mode-checkbox');

  checkbox.checked = localStorage.getItem('darkMode') === 'true';

  checkbox.addEventListener('change', function (event) {
    localStorage.setItem('darkMode', event.currentTarget.checked);
  });
}); 
```

## 另辟蹊径:`prefers-color-scheme`

CSS 有一个名为 [`prefers-color-scheme`](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme) 的新媒体查询特性，它允许你检测用户是偏好浅色主题还是深色主题。该偏好通过操作系统传递给浏览器。例如，从 macOS Mojave 开始，有一个启用操作系统黑暗模式的设置。我使用的是 Ubuntu 18.10，你可以通过创建或编辑文件`~/.config/gtk-3.0/settings.ini`并添加以下内容来配置你的偏好:

```
[Settings]
gtk-application-prefer-dark-theme=1 
```

不过火狐 67 似乎并不挑这个设置。它报告说用户更喜欢一个轻松的主题。为了能够继续尝试，你可以覆盖 Firefox 的`about:config`页面上的首选项。在那里，创建一个名为`ui.systemUsesDarkTheme`的新整数首选项(右键单击→新建→整数)，并将其值设置为`1`。现在，Firefox 报告了对黑暗主题的偏好。

如果用户有一个支持`prefers-color-scheme`功能的浏览器，并选择了操作系统设置，他们可以得到一个深色的配色方案，而不需要首先与一个明亮的页面交互。我做了一个 codepen 演示,它会告诉你你的浏览器是否支持这个特性，以及浏览器是否会暴露你的偏好。

现在，当需要深色主题时，您只需切换特定于主题的自定义属性，而不是做上面所有的事情。

```
:root {
  /* Light mode colors */
  --c-light-text: hsl(227, 100%, 20%);
  --c-light-background: #fff;
  --c-light-focus: hsl(32, 100%, 55%);
  --c-light-interactive: hsl(327, 100%, 55%);

  /* Dark mode colors */
  --c-dark-text: #eee;
  --c-dark-background: #121212;
  --c-dark-focus: hsl(32, 100%, 55%);
  --c-dark-interactive: hsl(150, 100%, 38%);
}

/* Set default theme to light theme */
:root {
  --c-text: var(--c-light-text);
  --c-background: var(--c-light-background);
  --c-focus: var(--c-light-focus);
  --c-interactive: var(--c-light-interactive);
}

/* Override default theme with dark theme */
@media (prefers-color-scheme: dark) {
  :root {
    --c-text: var(--c-dark-text);
    --c-background: var(--c-dark-background);
    --c-focus: var(--c-dark-focus);
    --c-interactive: var(--c-dark-interactive);
  }
} 
```

* * *

就是这样。因此，我的网站的下一次重新设计可能会包括一个黑暗模式复选框。

CodePen 上提供了[纯 CSS 黑暗模式](https://codepen.io/kleinfreund/pen/bJwqpB)的完整实现。