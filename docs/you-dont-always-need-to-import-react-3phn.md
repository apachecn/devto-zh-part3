# 您并不总是需要导入 React

> 原文：<https://dev.to/dance2die/you-dont-always-need-to-import-react-3phn>

有一个周末读书会，这是一个“书友会”式的活动，我们每个周末都会读点东西。

[上周的话题](https://www.reddit.com/r/reactjs/comments/9zw638/weekend_reads_react_docs_on_jsx_in_depth/)是 [JSX 深度](https://reactjs.org/docs/jsx-in-depth.html)，我想分享一些一直困扰我但了解到原因的事情。

问题是当“React”在你的组件代码中的任何地方都没有使用时，“你为什么要导入 React”？

## 🎶介绍

当您开始学习 react 时，您可能会被告知总是在组件文件中导入 React，`import React from "React"`。

***但这并不总是必要的。*T3】**

要理解为什么，让我们看看 JSX 是什么。

## 🤔JSX？

《深入 JSX》的副标题将 JSX 描述为

> 从根本上说，JSX 只是为`React.createElement(component, props, ...children)`函数提供了语法糖

你可以使用 JSX 合成糖🍬创建组件，或者如果您没有编译源代码，直接使用`React.createElement`。

## 🙄那你不应该到处导入 React 吗？

不，除非你为你的组件使用 React 对象。

您可以创建一个 [**【函数】**组件](https://reactjs.org/docs/components-and-props.html#function-and-class-components)，它返回一个简单的 JavaScript 原语的值，比如字符串或数字。