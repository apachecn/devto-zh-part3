# red hooks:React 应用程序的可预测状态容器。

> 原文：<https://dev.to/antonio_pangall/redhooks-predictable-state-container-for-react-applications-1i7k>

[![Redhooks Repository](img/abf8d1ca96057e158b9a9fd415f2df66.png)](https://github.com/iusehooks/redhooks)

# 什么是 REDHOOKS？

Redhooks 是一个微型库，用于在 React 应用程序中保存一个可预测的状态容器。受 [Reduxjs](https://redux.js.org/) 的启发，它使用实验性的钩子 API 和上下文 API 重新实现了 Redux 的 API。它支持使用像 redux-thunk、redux-saga 这样的中间件，或者符合中间件 API 的定制中间件。

让我们开始使用 redhooks 编写第一个简单的应用程序。

### 减速器

缩减器是一个纯粹的函数，它接受前一个状态和一个普通对象的动作，然后返回下一个状态。

`./reducer.js`

```
import { combineReducers } from "redhooks";

const greeting = (state = "good morning", { type, payload }) => {
  switch (type) {
    case "GREET":
      state = payload;
      return state;
    default:
      return state;
  }
};

const counter = (state = 0, { type, payload }) => {
  switch (type) {
    case "INCREMENT":
      return state + 1;
    case "DECREMENT":
      return state - 1;
    default:
      return state;
  }
};

const rootReducer = combineReducers({ greeting, counter });
export default rootReducer; 
```

Enter fullscreen mode Exit fullscreen mode

### 商店

存储保存了应用程序的整个状态树。存储区内的状态是只读的，改变状态的唯一方法是调度一个动作。为了创建一个商店，我们需要将我们的根归约函数传递给`createStore(reducer, [opts])`。

`./store.js`

```
import { createStore } from "redhooks";
import rootReducer from "./reducers";

const opts = {
  preloadedState: { counter: 1 },
  initialAction: { type: "INCREMENT" }
};
const store = createStore(rootReducer, opts);

export default store; 
```

Enter fullscreen mode Exit fullscreen mode

### 反函数组件

在功能组件中，为了访问商店，我们可以使用`useStore()` redhooks API。这将返回一个带有属性的对象，这些属性是状态对象和调度函数。

`./components/Counter.js`

```
import React from "react";
import { useStore } from "redhooks";
const Counter = () => {
  const { state, dispatch } = useStore();
  const { counter } = state;
  return (
    <div>
      <h1>{counter}</h1>
      <button onClick={() => dispatch({ type: "INCREMENT" })}> + </button>
      <button onClick={() => dispatch({ type: "DECREMENT" })}> - </button>
    </div>
  );
};
export default Counter; 
```

Enter fullscreen mode Exit fullscreen mode

### 迎宾员级组件

在类组件中，由于 React 钩子是不允许的，我们需要使用`connect` redhooks API，它将类或函数组件连接到 redhooks 存储

`./components/Greeter.js`

```
import React, { Component } from "react";
import { connect } from "redhooks";
class Greeter extends Component {
  render() {
    const { greeting, dispatch } = this.props;
    return (
      <div>
        <h1>{greeting}</h1>
        <button onClick={() => dispatch({ type: "GREET", payload: "HELLO" })}>
          say hello
        </button>
        <button onClick={() => dispatch({ type: "GREET", payload: "GOODBYE" })}>
          say goodbye
        </button>
      </div>
    );
  }
}
const mapStateToProps = state => ({ greeting: state.greeting });
export default connect(mapStateToProps)(Greeter); 
```

Enter fullscreen mode Exit fullscreen mode

使用 **mapStateToProps** 方法，我们可以向 redhooks 商店订阅任何组件。任何时候商店被更新， **mapStateToProps** 将被调用，它的结果，必须是一个普通的对象，将被合并到组件的道具中。在上面的例子中，注入了两个道具，问候和派遣。

> 我们本可以避免对欢迎类组件使用`connect`。它只是用来展示它是如何工作的。你可以在 [redhooks docs](https://github.com/iusehooks/redhooks/#connect) 获得更多关于为什么以及何时应该使用它的细节

现在让我们把所有的放在一起，呈现我们的小应用程序。

### App 组件

`./components/App.js`

```
import React from "react";
import Counter from "./Counter";
import Greeter from "./Greeter";

const App = () => (
  <div>
    <Counter />
    <Greeter />
  </div> );

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

### 渲染应用程序

`./index.js`

```
import React from "react";
import { render } from "react-dom";
import Provider from "redhooks";

import store from "./store";
import App from "./components/App";

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

我们完了！一个真实的 codesandbox 示例供您使用！
[https://codesandbox.io/embed/jnxvow5wyw](https://codesandbox.io/embed/jnxvow5wyw)

### 其他沙盒示例

以下几个用 redux 实现的开源项目已经迁移到了 redhooks:

购物车:[code sandbox](https://codesandbox.io/s/5yn1258y4l)
todom VC:[code sandbox](https://codesandbox.io/s/7jyq991p90)T5】树形视图:[code sandbox](https://codesandbox.io/s/rmw98onnlp)
Saga-中间件: [CodeSandbox](https://codesandbox.io/s/48pomo7rx7)

## 结论

希望你喜欢阅读这篇文章。如果你有，请检查红钩回购，甚至更好地贡献给红钩。谢了。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[iuse hooks](https://github.com/iusehooks)/[red hooks](https://github.com/iusehooks/redhooks)

### 使用钩子编写的 React 应用程序的可预测状态容器

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![Redhooks Logo](img/abf8d1ca96057e158b9a9fd415f2df66.png)](https://raw.githubusercontent.com/iusehooks/redhooks/master/logo/logo.png)

Redhooks 是一个微小的 React 实用程序库，用于在 React 应用程序中保存可预测的状态容器，其灵感来自于 [Redux](https://redux.js.org) ，它通过使用 React 的新钩子和上下文 API 重新实现了 Redux 状态管理范式，React 团队已经[正式发布了这些新钩子和上下文 API](https://reactjs.org/docs/hooks-reference.html)。

*   [动机](https://raw.githubusercontent.com/iusehooks/redhooks/master/#motivation)
*   [基本示例](https://raw.githubusercontent.com/iusehooks/redhooks/master/#basic-example)
*   [应用中间件](https://raw.githubusercontent.com/iusehooks/redhooks/master/#apply-middleware)
*   [使用 React 路由器](https://raw.githubusercontent.com/iusehooks/redhooks/master/#usage-with-react-router)
*   [隔离 Redhooks 子应用](https://raw.githubusercontent.com/iusehooks/redhooks/master/#isolating-redhooks-sub-apps)
*   [Redhooks API 引用](https://raw.githubusercontent.com/iusehooks/redhooks/master/#redhooks-api-reference)
*   [CodeSandbox 示例](https://raw.githubusercontent.com/iusehooks/redhooks/master/#codesandbox-examples)
*   [执照](https://raw.githubusercontent.com/iusehooks/redhooks/master/#license)

[![Build Status](img/ea75b15633fd2645323dfae353ba7224.png)](https://travis-ci.org/iusehooks/redhooks)[![Package size](img/776a5b36443f692274cf49abe74f33ac.png)](https://bundlephobia.com/result?p=redhooks)[![Coverage Status](img/2140f91e65d3ea5e3667e3eda4a3c1f1.png)](https://coveralls.io/github/iusehooks/redhooks?branch=master)[![License](img/1119f4c25b9b1ca13d61d4608d1f674d.png)](https://camo.githubusercontent.com/9f78a8c06ab43baf8742b57bc40ac0dc9404738b77bb1525b6ebfb8332256ba2/68747470733a2f2f696d672e736869656c64732e696f2f6e706d2f6c2f726564686f6f6b732e7376673f7374796c653d666c6174)[![Tweet](img/3aedcf72e57e3736cd6ea05d11dfd7db.png)](https://twitter.com/intent/tweet?text=Predictable%20state%20container%20for%20React%20apps%20written%20using%20Hooks&url=https://github.com/iusehooks/redhooks&hashtags=reactjs,webdev,javascript)

# 装置

```
npm install --save redhooks
```

Enter fullscreen mode Exit fullscreen mode

# 动机

在 [Reactjs 文档](https://reactjs.org/docs/hooks-custom.html)中，一个标题为 *useYourImagination()* 的漂亮段落建议考虑钩子提供的功能的不同可能用法，这基本上是 Redhooks 试图做的。这个包不使用任何第三方库，只依赖于钩子和上下文 API。你不需要安装`react-redux`来将你的组件连接到商店，因为你可以通过利用`useStore` Redhooks API 直接从你的任何功能组件访问它。…

</article>

[View on GitHub](https://github.com/iusehooks/redhooks)