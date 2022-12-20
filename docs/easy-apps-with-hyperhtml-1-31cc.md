# 使用 hyperHTML-1 的简单应用程序，连接/绑定

> 原文：<https://dev.to/pinguxx/easy-apps-with-hyperhtml-1-31cc>

## hyper 什么？介绍一个超快的轻量级 JS 库。

西班牙文版

[Chinese version](http://pinguxx.lofter.com/post/1f16161f_12aa45e3c)

1.  **介绍，焊线/绑定**
2.  [事件和组件](https://dev.to/pinguxx/easy-apps-with-hyperhtml-2-hbi)
3.  [关于组件和简单状态管理的 Moar】](https://dev.to/pinguxx/easy-apps-with-hyperhtml-3-1m3l)
4.  [电线类型和自定义定义(意图)](https://dev.to/pinguxx/easy-apps-with-hyperhtml-4-38k3)
5.  [用 hyper 定制元素](https://dev.to/pinguxx/easy-apps-with-hyperhtml-5-13hc)
6.  [定制我的定制元素](https://dev.to/pinguxx/easy-apps-with-hyperhtml-6-1e73)
7.  [测试！](https://dev.to/pinguxx/easy-apps-with-hyperhtml-7-5b9g)
8.  [异步加载、占位符和带超链接的提前输入](https://dev.to/pinguxx/easy-apps-with-hyperhtml-8-async-loading-5d96)
9.  [搬运路线](https://dev.to/pinguxx/easy-apps-with-hyperhtml-9-routing-59k0)
10.  [第三方库](https://dev.to/pinguxx/easy-apps-with-hyperhtml-10-3rd-party-libraries-2n7b)

第 1 部分作者

[![pinguxx image](img/b3fd6923e5679952858c6b2c43518de0.png)](/pinguxx)

## [伊凡](/pinguxx)

[standard geek](/pinguxx)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)pinguxx](https://twitter.com/pinguxx)[![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)pinguxx](https://github.com/pinguxx)

[![paritho image](img/31ca3c53383636df8ffd826c26771850.png)](/paritho)

## [保罗·汤普森](/paritho)

[lover of dogs and javascript. and coffee. and writing.](/paritho)

[![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)帕里托](https://github.com/paritho)

虽然每个人都在大肆宣传 React 和 Vue 等虚拟 DOM 框架，但我总觉得好像缺少了什么。我并不认为我们必须在内存中维护 DOM 的“副本”。我尝试了几个框架，然后找到了 Mithril——它有普通 JavaScript 函数的优点，而且不会碍事——但是我对任何一个框架都不完全满意。

然后找到了 [Andrea Giammarchi](https://medium.com/@WebReflection) 的作品。Andrea 曾经开发过像 vitamer 这样的工具，但是后来他突然发布了一个新的小框架，叫做 [hyperHTML](https://github.com/WebReflection/hyperHTML) 。看了他的博文[DOM 不慢，你的抽象是](http://webreflection.blogspot.com/2015/04/the-dom-is-not-slow-your-abstraction-is.html)，我很好奇。我开始明白使用起来是多么容易，并且喜欢一切都只是像 Mithril 这样的 JavaScript 函数——只需要学习 API 中的 2 个函数！再加上模板文字的简单性，你就有了一个可以快速呈现用户界面变化的库。

也没有虚拟 DOM。

让我们深入了解一些关于 hyperHTML 的基础知识，然后我们将使用一个简单的表格来应用我们的知识。要开始，你只需要知道基本的 html，并对 JavaScript 有很好的理解。

#### 基础知识—模板

hyperHTML 中的模板基于[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)。它们非常容易使用，只需在反斜线之间加一个字符串:

```
`some string` 
```

但是你也可以在其中放入 JavaScript，它会被求值:

```
`myvar value is ${myvar}` 
```

如果 myvar = 8；你会得到“myvar 值为 8”。这就是我们开始在 hyperHTML 中使用模板所需要知道的一切。

#### 基础知识—绑定

Bind()是你必须学习的两个函数之一。它将描述的模板呈现给所提供的 DOM 元素。Bind()与 wire()的不同之处在于，我们使用 bind()向现有的 DOM 节点添加内容。Bind 返回一个函数，您可以重用它来更新内容。例如:

`const render = bind(document.getElementById('app'));`

将 id 为“app”的元素绑定到该函数，这样每次我们用模板调用“render”时，它都会被更新。你可以在官方文件中了解更多信息。

[https://stackblitz.com/edit/basics-bind?embed=1&&](https://stackblitz.com/edit/basics-bind?embed=1&&)

hyperHTML 的一个很酷的特性是渲染速度非常快，当然它只会重新渲染需要更新的部分，让我们以一个简单的时钟为例。

[https://stackblitz.com/edit/basics-bind-2?embed=1&&](https://stackblitz.com/edit/basics-bind-2?embed=1&&)

如果你检查这个元素，你会看到只有 h2 随着时间每秒都在更新。

#### 基础知识—电线

您需要学习的另一个函数是 wire()。Wire()从提供的模板中返回 html。在需要创建新的 DOM 节点时，可以使用 wire()。您可以生成一个元素或元素数组。您还可以传递一个对象(或数组)，并作为第二个参数传递导线的类型。默认的 wire 方法是 html，但它也可以是 svg 或只是一个特定的 id，因此 hyperHTML 不会重新呈现它。在官方文件上阅读更多信息。

在这个简单的例子中，wire()返回标题的 h1:

[https://stackblitz.com/edit/hyperhtml-wire-ex1?embed=1&&](https://stackblitz.com/edit/hyperhtml-wire-ex1?embed=1&&)

对于下一个示例，第一个数组每次都被重新渲染(检查元素检查器)，但是请注意，当我们将一个对象传递给 wire-wire(obj)时，该对象并不是在每个时间点都被重新渲染。这是强大的东西。

[https://stackblitz.com/edit/hyperhtml-wire-ex2?embed=1&&](https://stackblitz.com/edit/hyperhtml-wire-ex2?embed=1&&)

*一个小问题…* 来自另一个框架，比如 Vue，你可能希望你的元素有部分属性。

```
<div class="myclass ${classvar}"></div> 
```

HyperHTML 不允许这样做，因为实际上这是不必要的。但是，您可以这样做:

```
<div class="${`myclass ${classvar}`}"></div> 
```

嵌套反斜线很好地解决了这个问题！关于为什么不支持部分属性的更多信息，请查看官方文档。

* * *

#### 简单表格— 1

让我们用我们目前所学的知识来写一个基于数组的简单表格。第一步是创建我们的基本 html。然后我们将创建绑定函数，最后是模板。我们将使用 map 来迭代我们的数组，以生成表的行/列…就这样！非常容易！在使用 hyperHTML 第 2 部分编辑简单应用程序时，我们将向表格中添加排序。

[https://stackblitz.com/edit/basics-table?embed=1&&](https://stackblitz.com/edit/basics-table?embed=1&&)