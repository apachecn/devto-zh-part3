# 了解 React 组件

> 原文：<https://dev.to/elimerrell/making-sense-of-react-components-3mbb>

React，我们都知道并且热爱的 JavaScript 库(可能？)，围绕着通过用户交互改变的互连组件的使用。

React 组件架构允许开发人员将用户界面分解成更容易操作和调试的小型独立单元。React 库为开发人员提供了几个编写组件的选项，我经常发现很难决定给定的代码块使用哪种组件格式。为了揭开 React 中各种组件的神秘面纱，我将尝试比较和对比每种类型的结构，并描述它们的最佳用例。

因为 React 只是一个用 JavaScript 编写的库，所以可以将 React 组件编写成普通的旧 JavaScript 函数。在最基本的层面上，React 组件是接受属性(props)作为参数，并使用或操纵这些属性向 DOM 呈现内容的函数。如果您习惯于编写 JavaScript 函数，那么编写函数式 React 组件应该不会有问题。

尽管 React 应该是 JS 程序员最熟悉的，但还是有一个难题。React 使用 JSX (JavaScript XML)，这违背了 JS 和 XML 的典型分离。使用 JSX 编写代码时，组件包括一个呈现函数，该函数将类似 HTML 的元素返回到页面: