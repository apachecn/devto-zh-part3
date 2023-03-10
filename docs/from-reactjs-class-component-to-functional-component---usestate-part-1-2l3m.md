# 从 ReactJS 类组件到功能组件——使用状态(第 1 部分)

> 原文：<https://dev.to/zeyadetman/from-reactjs-class-component-to-functional-component---usestate-part-1-2l3m>

大家好，在这篇文章(两部分)中，我将把 reactjs 代码从类组件转换成功能组件。关注`useState`和`useEffect`挂钩。让我们从第一部分`useState`开始吧。

## 状态

### 在类组件中

在类组件中，我们在`constructor`函数中初始化`this`类的`state`对象，表示该状态属于该组件，其值在`constuctor`中初始化。我们可以通过调用`setState()`传递新的对象来改变状态。
代码会是这样的

```
import React, { Component } from "react";
import ReactDOM from "react-dom";

export default class App extends Component {
  constructor() {
    super();
    this.state = {
      count: 0
    };
  }
  render() {
    return (
      <div className="App">
        <h1>Current count: {this.state.count}</h1>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Increment
        </button>
      </div>
    );
  }
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

使用`setState()`的特性之一是回调，是的，你可以传递第二个参数作为回调。这是一个很棒的特性。
这方面的例子

```
<button
  onClick={() =>
    this.setState({ count: this.state.count + 1 }, () => {
      console.log(this.state.count);
     })
  }
> 
```

### [功能组件中的](#in-functional-component)

在一个功能组件中，没有`constructor()`没有`this`关键字没有`render()`功能。首先我们必须从`react`进口`useState`。
`import React, { useState } from "react";`
`useState`只接受一个参数，即初始值，并返回一个有状态值和一个函数来改变它，我们像这样对它进行析构:
`const [state, setState] = useState(initialValue);`
要使用它，我们可以像类示例一样简单地使用它。

```
const [state, setState] = useState({
  count: 0
});

console.log(state.count); // 0 
```

并且更新成这样

```
setState({ count: state.count + 1 }); 
```

#### 但是，如果我们有多个值呢？

这里我们有两个选择:

*   首先，像上面那样简单地使用它。

```
const [state, setState] = useState({
  count: 0,
  barcode: "1A"
}); 
```

但是，当你改变它时，不要忘记传递当前状态，只改变你想要的，就像这样:

```
setState({
  ...state,
  barcode: "2B"
}); 
```

如果你写这个`setState({barcode: "2B"});`，你只是改变了状态，并删除了`count`键。

*   第二，可以在同一个组件中多次使用`useState`，一次用于`count`，一次用于`barcode`，如下所示:

```
const [count, setCount] = useState(0);
const [barcode, setBarcode] = useState("1A"); 
```

要更新它，只需传递值:

```
setBarcode(value);
setCount(count + 1); 
```

不幸的是`useState`不接受第二个参数，所以我们必须使用`useEffect`钩子来处理组件上的变化。
在您离开之前，请检查两个代码沙箱，尝试一下您在本文中学到的东西。

*   [使用功能组件中的状态](https://codesandbox.io/s/kkm2o57ky3)

*   [类组件中的状态](https://codesandbox.io/s/l49v8r45xq)

在第 2 部分——我们将扩展我们的代码以包含`useEffect` hook。

*[最初发布于博客](https://zeyadetman.github.io/blog/posts/from-reactjs-class-component-to-functional-component-usestate-part-1/)和[在推特上关注我](https://twitter.com/zeyadetman)*