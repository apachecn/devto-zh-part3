# lit-html 简介

> 原文：<https://dev.to/julcasans/a-gentle-introduction-to-lit-html-3d74>

> 📣**更新！**📣
> *现在 lit-html 和 LitElement 统一在 [Lit](https://lit.dev/) 下。*
> *我正在写关于**点燃**的新帖子，同时你可以:*
> 
> *   *阅读这篇文章，因为原理是一样的*
> *   *用这个[指南](https://lit.dev/docs/releases/upgrade/)* 升级你的代码
> *   *访问 *Lit* 网站了解最新动态*
> 
> *你也可以使用 [lit-html 单机](https://lit.dev/docs/libraries/standalone-templates/)T3】*

你好！我以一系列`LitElement`条目开始我的博客。我将从最基本的概念开始，然后我将深入这个库的使用细节。由于`LitElement`是基于`lit-html`的，第一步将是对`lit-html`的研究。希望你喜欢，觉得有用。

`lit-html`是 Polymer 团队制作的 HTML 模板库。这个库非常简单，非常小，在速度和资源使用方面非常有效，这一切都是因为它是基于 web 标准的。

需要注意的非常重要的一点是,`lit-html`没有定义任何组件模型，因此可以用在任何带有其他框架或库的项目中。

## 那么什么是模板呢？

想象一张付款收据，它是一张写有单词和空格的纸。它有一个总是相同的文本部分和其他可以变化的部分。可能是这样的:

```
Date: ___/___/___
Received from ___________, the amount of $__________
for _____________________________________________
Received by: __________________ 
```

Enter fullscreen mode Exit fullscreen mode

该模板可以打印并多次使用，在空白处填入每次付款的数据。

## 什么是 HTML 模板？

想象一段 HTML 代码，其中有一些部分需要填充，这些部分的内容可以改变，但其余部分保持不变，因此 HTML 代码有静态部分和动态部分(需要填充的间隙)。

如果上面的收据模板是用 HTML 编写的，它可能是:

```
<div>
  <p>Date: <span> ______</span></p>
  <p>Received from <span> ______ </span>, 
     the amount of <span> $ ______ </span> </p>
  <p> for <span> _______ </span></p>
  <p> Received by: <span> _______ </span></p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

`lit-html`让你定义一个类似上面的 HTML 模板，这样它就可以在动态部件中使用不同的值进行渲染和重用。这并不是什么新东西，还有其他像 MoustacheJS 或 HandlebarJS 这样的库也在做同样的事情，但是`lit-html`很特别，因为它以一种非常非常有效的方式做这件事。为什么这么高效？通常的方法是解析模板，然后在 DOM 节点中呈现 HTML，在变化的部分使用新值。每次动态部分改变时，都需要一个解析和呈现过程。重新呈现 DOM 的成本很高，因为它用新节点替换了旧节点。`lit-html`是高效的，因为它不解析模板，它只在 DOM 中呈现 HTML 一次，只是第一次。它还记得动态部分在哪里，以便当这些部分的值改变时`lit-html`只需直接访问该部分并更新 DOM 中的值(通常它只是文本内容，尽管它也可以是节点)。

非常高效，因为它使用了在浏览器中本地实现的 web 平台的特性。这些特征是:

*   JavaScript 的标记模板文字。
*   HTML 模板标签。

这些特性的使用使得库变得小而快，因为它不需要实现字符串模板的解析。它是由 JavaScript 原生完成的。

由模板表示的 DOM 的创建也是高效的，因为它是通过`<template>`标签克隆其内容来实现的。
因此，使用该平台提供的功能，`lit-html`不必求助于使用像 JSX 这样需要额外处理的特殊语言，也不必创建像虚拟 DOM 这样的复杂解决方案。

## 如何定义模板？

到此为止的话太多了，我们来看看`lit-html`里一个模板是怎么定义的。

```
html`
  <div>
    <p>Date: <span>${data.date}</span></p>
    <p>Received from <span>${data.title}  ${data.name}  ${data.surname} </span>, the amount of <span> ${data.amount}</span> </p>
    <p> for <span> ${data.concept} </span></p>
    <p> Received by: <span> ${data.receiver} </span></p>
  </div>` 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个模板文字，用一个名为`html`的函数标记。这是`lit-html`提供的两个功能之一。
正如我之前说过的，`lit-html`使用带标签的模板文字来定义模板。这些字符串包含普通的 HTML 代码，没有什么特别的，除了标签函数和带`${}`的标记。

包含在`${}`中的是 JavaScript 表达式，当被插值时，它们被求值的结果所替代。此外，当 JavaScript 找到一个带标记的模板文字时，它会处理字符串(一种解析任务)并将其传递给 tag 函数。如你所见，这都是纯 JavaScript 的工作，不需要任何其他东西(我的意思是，不像 JSX)。

这就是`lit-html`中模板的样子。为了使它有用，我们可以定义一个函数，它获取将在模板文本中应用的数据，并返回一个`ResultTemplate`对象(这是`html`标签函数产生的，但我们现在不需要详细介绍，我将在以后的帖子中讨论)。

```
const receiptTemplate = (data) => html`
<div>
  <p>Date: <span>${data.date}</span></p>
  <p>Received from <span>${data.title}  ${data.name}  ${data.surname} </span>, the amount of <span> ${data.amount}</span> </p>
  <p> for <span> ${data.concept} </span></p>
  <p> Received by: <span> ${data.receiver} </span></p>
</div>` 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了模板定义，我们可以用不同的值多次使用它。

```
receiptTemplate(myData);
receiptTemplate(otherData); 
```

Enter fullscreen mode Exit fullscreen mode

## 渲染模板呢？

我们已经看到了如何定义一个模板，现在我们来看看如何渲染它。这是最简单的部分。为此，我们将使用由`lit-html`提供的另一个函数:`render`函数。

`render`需要两个强制参数:第一个是要呈现的`TemplateResult`对象，第二个是放置模板的 DOM 节点。第三个参数是可选的，它是一个包含额外选项的对象(现在您不必知道这一点)。

```
const templateResult = receiptTemplate(myData);
render(templateResult, document.getElementById('mainContent')); 
```

Enter fullscreen mode Exit fullscreen mode

## 重述

*   是一个简单、现代、安全、小巧、快速的 JavaScript HTML 模板库。
*   它可以用于任何项目。
*   它基于网络标准。
*   它是框架不可知的。
*   它提供两个功能:`html`和`render`。
*   `html`用作定义 HTML 模板的模板文字前面的标签。
*   `html`返回一个`TemplateResult`对象。
*   `render`是用来渲染 DOM 中模板的函数。
*   `render`接收一个`TemplateResult`对象和一个放置模板的节点。

这是你使用这个现代图书馆应该知道的所有基本的、最低限度的和基本的东西。在下一篇文章中，我将向你展示如何安装`lit-html`，并详细解释如何编写复杂的模板。下次见！