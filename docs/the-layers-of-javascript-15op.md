# Javascript 的层次

> 原文：<https://dev.to/laurieontech/the-layers-of-javascript-15op>

最近我在写一个教案，教一个小组关于盖茨比中的数据显示。我的目标是分解主题，并回答参与者可能会提出的所有低级问题。在这个过程中，我注意到 javascript 的生态系统有很多层！弄清楚哪种技术负责每项功能对于调试和增长您的知识非常有帮助。所以事不宜迟，我给你 javascript 的层！

## Javascript...或者那是 ECMAScript？ES6？这是怎么回事？！？！

Javascript 是你的基础语言。然而，它是基于 ECMAScript 的，ECMAScript 是脚本语言的标准。ECMAScript 有版本，就像编程语言一样。理解 ECMAScript 规范改变时会发生什么是很重要的。

在某种程度上，ECMAScript 告诉你 Javascript *可以做什么。它实际做什么取决于解释它的 Javascript 引擎。浏览器中存在 Javascript 引擎。浏览器需要[支持](http://kangax.github.io/compat-table/es2016plus/)标准，这样你才能使用新功能。*

目前广泛支持的 ECMAScript 版本是 ES6。ES6 标志着标准的重大变化，引入了像`let`和`const`这样的概念。

```
character.map(function ( [first, last] ) {
    return first + last
}); 
```

Enter fullscreen mode Exit fullscreen mode

*析构赋值是 ES6* 中引入的东西之一

如果您希望使用新引入的特性，那么理解 ECMAScript 在 Javascript 生态系统中的位置尤其有帮助。

## Node -v 是我的 Javascript 版本吧？

前端开发的一个更令人困惑的方面与节点有关。当你在本地开发时，你可能有一个你的计算机指向的节点版本。Node 实际上是捆绑在一起的，包含了两种不同的东西。Node.js 运行时和 npm 包管理器。因此，您可能会使用一个、两个或两个都不使用。

您可以使用 npm 来安装外部项目依赖项。这并不一定意味着您正在使用 Node.js 运行时。

如前所述，Javascript 由浏览器解释。Node.js 运行时是一个解释器和环境的组合，它允许您将 Javascript 用作通用编程语言，即在浏览器之外。它实际上是基于 Chrome 使用的 Javascript 解释器。然而，这意味着如果您正在构建类似 React 应用程序的东西，Node.js 运行时不会有任何影响。你真正感兴趣的是不同的浏览器如何解释你的代码。

## JSX

所以解释代码的方式和支持的特性是有层次的。然而，这只是影响代码实际语法的方式之一。现代 Javascript 的复杂性之一是使用核心框架。React 是一个特别流行的框架。在反应你有能力使用 JSX。JSX 是一种 Javascript 语法，允许您在 React 组件中直接使用 HTML 标记。这意味着您使用的是 Javascript 语言和 HTML 文件中的标记的组合。

```
const IndexPage = () => (
    <Layout>
        <div>Your code is going to go here!</div>
    </Layout>
) 
```

Enter fullscreen mode Exit fullscreen mode

JSX 有不同于 Javascript 语言的语法规则和特性。关于函数作用域括号与括号的期望可能看起来不熟悉。认识到这是 JSX 强制执行的规则将有助于提醒您为什么它与您的 Javascript 知识相冲突。

## 框架

Javascript 的另一层是由于您使用的框架而特别可用的特性。例如，在 React 中，你可能会处理传球道具。道具是一个框架概念。

```
function Introduction(props) {
  return <h1>Welcome, {props.name}</h1>
} 
```

Enter fullscreen mode Exit fullscreen mode

## 框架...关于框架？！？！

现代 Javascript 的复杂性之一是使用核心框架...听起来熟悉吗？这意味着你有像 Gatsby 一样的基于 React 的框架。理解 Gatsby 在做什么和 React 提供的功能是一个挑战。虽然这不太可能影响你的语法，但它会影响你在各种情况下的行为。例如，Gatsby 将 GraphQL 查询的结果直接传递给组件 props。试着在 React 中这样做，你会想你哪里错了。

## 哇

使用现有的所有工具，编写一个 Javascript 应用程序非常容易。然而，当你接触到更复杂的特性，更深入地研究代码时，很容易迷失方向。了解堆栈的哪一层给你带来了问题，或者可能提供了更简单的解决方案，这是非常有帮助的。