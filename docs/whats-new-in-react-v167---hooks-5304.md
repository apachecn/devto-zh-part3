# React v16.7 中的新特性-钩子

> 原文：<https://dev.to/lauragift21/whats-new-in-react-v167---hooks-5304>

[![img](img/d96851955eaddef2dfb0c1d3a64e8bd0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X0TflA9V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1543931797/1_Ra-gkqfPqbWVhgP3tR-0Cg_a7gxdm.png)
**[React v 16 . 7 . 0](https://reactjs.org/docs/hooks-intro.html)在 React Conf 2018 上推出，[丹·阿布拉莫夫](https://twitter.com/dan_abramov)在推出 [React Hooks 时泄露了秘密。](https://reactjs.org/docs/hooks-intro.html)我们刚刚发布了 React v16.6.0: lazy、memo 和 contextType，现在 Hooks 难道你不喜欢 React 社区吗？**

## 什么是钩子？

钩子是 React 中的一个新特性，它让你不用写类就可以使用状态和其他 React 特性。这个新特性允许您直接挂钩到 React 状态，并允许您只使用功能组件编写复杂的逻辑，而不需要任何类组件。

*该提案背后的想法源于以下概念:*

*   了解和管理复杂的组件变得令人疲惫不堪。
*   类混淆了人和机器。
*   在组件之间重用有状态逻辑很累人。

## 钩子在行动

在下面的演示中，我将说明使用传统 React 组件和新 React 挂钩之间的区别。

## 类构件同状态

```
 import React, { Component } from "react";
import ReactDOM from "react-dom";

class App extends Component {
  state = {
    count: 0
  };
  setCount = () => {
    this.setState({ count: this.state.count + 1 });
  };
  render() {
    return (
      <>
        <h1>{this.state.count}</h1>
        <button onClick={this.setCount}>Click me</button>
      </>
      );
  }
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

[观看演示](https://codesandbox.io/s/r726kwvm2m)

## 用 React 钩子使用状态

```
 import React, { useState } from "react";
import ReactDOM from "react-dom";

function Counter() {
// similar to this.state and this.setState
  const [count, setCount] = useState(0);
  const increment = () => setCount(count + 1);
  return (
    <>
      <h1>{count}</h1>
      <button onClick={increment}>Click me</button>
    </>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<Counter />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

[观看演示](https://codesandbox.io/s/r726kwvm2m)

## 使用状态挂钩

这就是钩子的厉害之处。它引入了新的基本钩子，我们可以使用现成的状态钩子和效果钩子。你也可以创建定制的钩子来钩住一个非常酷的功能组件。上面展示的演示使用了 State 钩子，一般来说，这是用来管理状态的，就像我们在类组件中使用 this.state 一样。useState()接受一个默认为初始状态的参数，可以是字符串或数字。在演示中，我们将 useState(0)作为计数的初始状态。useState()还接受两个变量，第一个值是当前状态，类似于类组件中的 this.state，第二个值是我们希望当前状态更新为类似于类组件中的 this.setState 的值。

创建状态挂钩背后的想法是，我们现在可以在一个功能组件中管理状态，这消除了当我们实际上应该使用无状态组件而不是有状态组件时的困惑。

## 用钩子使用多个状态

我们也可以用 useState 钩子拥有多个状态，你所要做的就是在函数中声明多个 useState 钩子。

```
 import React, { useState } from 'react';

function multipleStates() {
  // Declare multiple state variables!
  const [count, setCount] = useState(0);
  const [cars, setCars] = useState('Lamborghini');
  const [menu, setMenu] = useState([{ menu: 'Scrambled Eggs' }]);
} 
```

Enter fullscreen mode Exit fullscreen mode

使用多个状态变量很好，但我们也可以使用单个状态变量。状态变量可以将对象和数组放在一起，所以我们仍然可以将相关的数据组合在一起。然而，与此相反，在类组件中更新状态变量总是替换它而不是合并它。

## 效果挂钩

当一个过程在其作用域之外改变一个变量时，就会产生副作用。一个典型的例子是手动更改 DOM 或从不同的源获取数据。

效果钩子允许我们处理函数组件中的副作用。如果您熟悉 React 类生命周期方法，您可以将 useEffect 挂钩看作 componentDidMount、componentDidUpdate 和 componentWillUnmount 的组合。这个例子描述了 useEffect 钩子是如何工作的:

对于类组件，我们使用生命周期方法跟踪副作用，如 componentDidMount、componentDidUpdate。如何在类组件中管理副作用的一个例子是这样的:

```
 class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }
  componentDidMount() {
    document.title = `You clicked ${count} times`;
  }
  render() {
    return (
      <div>
        <p>You clicked {count} times</p>
        <button onClick={() => setCount(count + 1)}>Click me</button>
      </div>
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

```
 import React, { useState, useEffect } from "react";
import ReactDOM from "react-dom";

function Example() {
  const [count, setCount] = useState(0);
  /* handles lifecycle event just like
  componentDidMount and componentDidUpdate */
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<Example />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

[观看演示](https://codesandbox.io/s/r726kwvm2m)

使用 React 钩子同样可以使用 useEffect 钩子，每当组件挂载时，效果钩子将在渲染后运行，包括第一次渲染。上面演示的代码所做的是在每次组件呈现时更新文档标题的计数。

## 反应钩子的规则

为了有效地使用钩子，我们需要遵守两条重要的规则，React 还提供了一个 [linter 插件](https://www.npmjs.com/package/eslint-plugin-react-hooks)来自动执行这些规则。

### 规则 1:只调用顶层的钩子

文档建议我们不要在循环、条件或嵌套函数中调用钩子:

*   遵循这条规则，你可以确保每次组件渲染时钩子都以相同的顺序被调用。这使得 React 能够在多个 useState 和 useEffect 调用之间正确地保留钩子的状态。——[反应过来](https://reactjs.org/docs/hooks-rules.html#only-call-hooks-at-the-top-level) [勾规则](https://reactjs.org/docs/hooks-rules.html#only-call-hooks-at-the-top-level)

### 规则二:只从 React 函数调用钩子:

*文档继续指出我们不应该从常规的 JavaScript 函数中调用钩子:*

*   遵循这条规则，你可以确保组件中所有的有状态逻辑从它的源代码中清晰可见。[-反应过来](https://reactjs.org/docs/hooks-rules.html#only-call-hooks-from-react-functions) [勾规则](https://reactjs.org/docs/hooks-rules.html#only-call-hooks-from-react-functions)

*为了在使用 React 钩子时自动添加 ESLint 插件来执行这些规则，我们需要按照以下步骤来安装插件:*

```
$ npm install eslint-plugin-react-hooks@next 
```

Enter fullscreen mode Exit fullscreen mode

```
 // Your ESLint configuration
{
  "plugins": [
    // ...
    "react-hooks"
  ],
  "rules": {
    // ...
    "react-hooks/rules-of-hooks": "error"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   将来，该插件将默认安装到 Create React App 和类似的工具包中。

## 结论

我们已经了解了 Hooks 是什么，以及 React Hooks 是如何带来之前函数组件无法实现的所有可能性的，我们现在可以在 React 的函数组件中管理状态，这样就不需要为我们的应用程序编写类组件了。现在，这并不意味着类组件不再有用，但是为了降低类组件的复杂性，我们可以将它们分解成利用 React 钩子的功能组件。我们还可以为不同的功能编写定制的钩子，这极大地扩展了 React 钩子的用例。