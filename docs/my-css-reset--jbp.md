# 我的 CSS 重置

> 原文：<https://dev.to/zellwk/my-css-reset--jbp>

许多前端开发人员开始用 Normalize 设计他们的网站。一些开发者有他们添加到 Normalize.css 的个人偏好，我也有我的偏好。

在这篇文章中，我想与你分享这些偏好。个人 CSS 重置(我除了 Normalize.css 之外还使用它)。

我将重置分为 8 类:

1.  [盒子尺寸](#box-sizing)
2.  [移除边距和填充](#removing-margins-and-paddings)
3.  [列表](#list)
4.  [表单和按钮](#forms-and-button)
5.  [图像和嵌入](#images-and-embed)
6.  [表格](#tables)
7.  [隐藏属性](#the-hidden-attribute)
8.  [Noscript](#noscript)

## 框尺寸

属性改变了 CSS 盒子模型的工作方式。它改变了`width`、`height`、`padding`、`border`和`margin`属性的计算方式。

`box-sizing`的默认设置是`content-box`。我更喜欢把这个改成`border-box`，因为对我来说设计`padding`和`width`更容易。

有关盒子尺寸的更多信息，您可能会对“[了解盒子尺寸](https://zellwk.com/blog/understanding-css-box-sizing/)”感兴趣。

```
html {
  box-sizing: border-box;
}

*,
*::before,
*::after {
  box-sizing: inherit;
} 
```

## 删除页边距和填充

浏览器为不同的元素设置不同的边距和填充。当我不知道的时候，这些默认设置让我很困惑。当我编码时，我喜欢自己设置所有的边距和填充。

```
/* Reset margins and paddings on most elements */
body,
h1,
h2,
h3,
h4,
h5,
h6,
ul,
ol,
li,
p,
pre,
blockquote,
figure,
hr {
  margin: 0;
  padding: 0;
} 
```

## 列表

我在许多情况下使用无序列表，并且在大多数情况下我不需要`disc`样式。在这里，我将`list-style`设置为 none。当我需要`disc`风格时，我会在特定的`<ul>`
上手动设置它

```
/* Removes discs from ul */
ul {
  list-style: none;
} 
```

## 表单和按钮

浏览器不会继承表单和按钮的版式。他们将`font`设置为`400 11px system-ui`。我觉得这令人难以置信和怪异。我总是不得不手动让它们继承祖先元素。

```
input,
textarea,
select,
button {
  color: inherit; 
  font: inherit; 
  letter-spacing: inherit; 
} 
```

不同的浏览器对表单元素和按钮的边框有不同的样式。我不喜欢这些默认样式，更愿意将它们设置为`1px solid gray`。

```
input,
textarea,
button {
  border: 1px solid gray; 
} 
```

我对按钮做了一些调整:

1.  将按钮填充设置为`0.75em`和`1em`，因为从我的经验来看，它们似乎是合理的默认值。
2.  移除了应用于按钮的默认`border-radius`。
3.  强制背景颜色为透明

```
button {
  border-radius: 0; 
  padding: 0.75em 1em;
  background-color: transparent;
} 
```

最后，我将`pointer-events: none`设置为按钮中的元素。这主要用于 JavaScript 交互。

(当用户点击按钮中的某个东西时，`event.target`是他们点击的东西，而不是按钮。如果按钮中有 HTML 元素，这种风格使得处理`click`事件更加容易。

```
button * {
  pointer-events: none;
} 
```

## 媒体元素

媒体元素包括图像、视频、对象、iframes 和 embed。我倾向于让这些元素符合其容器的宽度。

我还将这些元素设置为`display: block`，因为`inline-block`在元素底部创建了一个不需要的空白。

```
embed,
iframe,
img,
object,
video {
  display: block;
  max-width: 100%;
} 
```

## 表格

我很少使用表格，但有时可能会有用。下面是我将要开始的默认样式:

```
table {
  table-layout: fixed;
  width: 100%;
} 
```

## 隐藏属性

当一个元素有一个`hidden`属性时，它们应该隐藏起来。Normalize.css 已经帮我们做到了。

```
[hidden] {
  display: none;
} 
```

这种风格的问题是它的低特异性。

我经常将`hidden`添加到我用类设计的其他元素中。一个类的特异性高于一个属性，`display: none`属性不起作用。

这就是为什么我选择用`!important`提升`[hidden]`的特异性。

```
[hidden] {
  display: none !important;
} 
```

## Noscript

如果一个组件需要 JavaScript 才能工作，我将添加一个`<noscript>`标签让用户知道(如果他们已经禁用了 JavaScript)。

这为`<noscript>`标签创建了默认样式。

```
/* noscript styles */
noscript {
  display: block;
  margin-bottom: 1em;
  margin-top: 1em;
} 
```

## 包装完毕

每个人都以不同的方式开始他们的项目。请随意使用我提到的任何一种风格。这是我个人 CSS 重置的 Github 报告。

你有什么建议可以帮助改进这个 CSS 重置文件吗？如果有，请随时联系我，让我知道！

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。