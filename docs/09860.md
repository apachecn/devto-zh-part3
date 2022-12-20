# 使用 CSS 变量进行主题化

> 原文：<https://dev.to/idoshamun/theming-with-css-variables-322f>

给你的 web 应用程序设置主题曾经非常麻烦，你必须使用 javascript 来完成改变元素视觉效果的繁重工作，或者根据当前主题为每个元素创建多个表示。这么多乱七八糟的东西只是为了主题。

如今，由于有了 [CSS 变量](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)，实现起来更加容易和简洁。

# CSS 变量是什么？

实际上，它们很像你从其他语言中知道的其他变量。您将它们定义为元素 CSS 的一部分，然后可以在元素本身及其子节点的 CSS 中使用它们。
你如下定义一个变量:

```
selector {
  --my-variable: value;
} 
```

该值可以是您喜欢的任何 CSS 值(甚至是另一个变量)。使用一个变量就像定义:
一样简单

```
selector {
  color: var(--my-variable);
} 
```

下面是一个活生生的例子:

[https://codepen.io/idoshamun/embed/rRKgrM?height=600&default-tab=css,result&embed-version=2](https://codepen.io/idoshamun/embed/rRKgrM?height=600&default-tab=css,result&embed-version=2)

# 如何用 CSS 变量轻松主题化？

首先，我们要明白我们的主题需要哪些变量。为了简单起见，假设我们的主题只有两个变量，原色和次色。在一个真实的用例中，可能会有更多的变量(例如阴影、字体等)。

其次，让我们为每个主题定义变量(我个人的偏好是在`html`元素级别定义，但是可以随意在任何地方使用)。我们将按类来划分主题。同样，假设我们只有两个主题，默认和蓝色主题。

```
html {
  --my-primary: red;
  --my-secondary: blue;
}

html.blue {
  --my-primary: blue;
  --my-secondary: red;
} 
```

让我们使用这些变量来设计我们的元素:

```
.child {
  color: var(--my-primary);
  background: var(--my-secondary);
} 
```

现在，如果我们在`html`元素中切换`blue`类，我们的`child`元素将毫不费力地改变它的视觉效果。非常干净和直观的代码，如果我们想添加一个新的类，就像给`html`添加另一个类一样简单。完整的工作示例:

[https://codepen.io/idoshamun/embed/moKZBN?height=600&default-tab=html,css&embed-version=2](https://codepen.io/idoshamun/embed/moKZBN?height=600&default-tab=html,css&embed-version=2)

让主题化开始吧！