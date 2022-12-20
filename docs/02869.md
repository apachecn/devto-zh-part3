# 揭秘 JSX 如何为 React 初学者呈现

> 原文：<https://dev.to/guin/demystifying-how-jsx-renders-for-react-beginners-dbk>

初次接触 React 代码的开发人员可能会看到 JSX，并想为什么我的 JavaScript 中有 HTML？然而，JSX 实际上不是 HTML 或字符串。它是 JavaScript 的语法扩展。

这是什么意思？-任何你可以用 JSX 做的事情，你都可以用普通的 JavaScript 来做。尽管您将在大多数情况下使用 JSX，但这澄清了一个常见的误解，即在使用 React 时必须使用 JSX。因为我们一直在使用它，所以看看这个抽象到底在做什么是很有用的。

那么，当我们创建一个呈现一些 JSX 的 React 组件时会发生什么呢？它将被转换为使用 React.createElement API，该 API 返回给定类型的新 React 元素。React 元素描述了您希望在用户界面中看到的内容，它只是一个普通的对象。该类型可以是字符串形式的任何标记名(例如:“div”、“h2”、“span”)、React 组件或 React 片段。React.createElement 也接受道具和子参数。React DOM 负责更新 DOM 以匹配 React 元素。

```
React.createElement(
  type,
  [props],
  [...children]
) 
```

## 让我们看一个例子

我们将创建一个组件`Cat`,它接受一个名称属性，并呈现一个带有字符串的标题，告诉我们猫的名称。

### 利用 JSX:

```
import React from "react";
import ReactDOM from "react-dom";

class Cat extends React.Component {
  render() {
    return <h1>My name is {this.props.name}</h1>
  }
}

ReactDOM.render( <Cat name="Fishguts" />,
  document.getElementById("app")
); 
```

现在让我们看看如何使用 React.createElement 在没有 JSX 的情况下重写它。在 ReactDOM.render 函数中，我们创建并传递我们的 Cat 组件和根 DOM 节点“app”。我们使用 createElement，将 Cat 组件作为类型，将一个名为 property 的对象作为道具。在 Cat 组件本身中，我们传入一个 h1 标签作为字符串，一个[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)作为子元素。

### 使用 React.createElement:

```
import React from "react";
import ReactDOM from "react-dom";

class Cat extends React.Component {
  render() {
    return React.createElement("h1", null, `My name is ${this.props.name}`);
  }
}

ReactDOM.render(
  React.createElement(Cat, { name: "Fishguts" }, null),
  document.getElementById("app")
); 
```

希望你已经对在 React 应用中编写 JSX 时会发生什么有了更好的理解！

如果您有任何问题、意见或反馈，请告诉我。关注每周关于 JavaScript、React、Python 和 Django 的新帖子！