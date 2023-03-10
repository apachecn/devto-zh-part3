# 使用 React 挂钩构建一个类似 Redux 的全局存储

> 原文：<https://dev.to/ramsay/build-a-redux-like-global-store-using-react-hooks-4a7n>

我们将假装我为这篇文章写了一篇有趣的序言，这样我们就可以跳到精彩的部分。简而言之，我们将使用`useReducer`和`useContext`创建一个定制的 React 挂钩，提供对全局存储的访问，类似于 redux。

> 我并不是说这个解决方案与 redux 具有同等的功能，因为我肯定它没有。通过“redux-like”，我的意思是您将通过*分派动作*来更新存储，这些动作决定变异并返回变异状态的新副本。如果你没用过 redux，就假装没看过这个。

在开始之前，我创建了一个代码沙箱，里面有完整的实现，如果你想玩代码的话。

[https://codesandbox.io/embed/xpjm9p052z](https://codesandbox.io/embed/xpjm9p052z)

## 钩子

让我们从创建包含我们的`state`对象和`dispatch`函数的`Context`开始。我们还将调用`useStore`函数作为我们的钩子。

```
// store/useStore.js

import React, { createContext, useReducer, useContext } from "react";

// we'll leave this empty for now
const initialState = {}

const StoreContext = createContext(initialState);

// useStore will be used in React components to fetch and mutate state
export const useStore = store => {
  const { state, dispatch } = useContext(StoreContext);
  return { state, dispatch };
}; 
```

Enter fullscreen mode Exit fullscreen mode

由于一切都存储在 [React 上下文](https://reactjs.org/docs/context.html)中，我们将需要创建一个[提供者](https://reactjs.org/docs/context.html#contextprovider)，为我们提供`state`对象和`dispatch`函数。提供商是我们将使用`useReducer`的地方。

```
// store/useStore.js

...
const StoreContext = createContext(initialState);

export const StoreProvider = ({ children }) => {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <StoreContext.Provider value={{ state, dispatch }}>
      {children}
    </StoreContext.Provider>
  );
};
... 
```

Enter fullscreen mode Exit fullscreen mode

我们用`useReducer`来得到`state`，和`dispatch`，因为[就是`useReducer`做](https://reactjs.org/docs/hooks-reference.html#usereducer)的事情。我们将`state`和`dispatch`传递给提供者。我们可以用`<Provider/>`包装任何我们想要的 React 组件，然后该组件可以使用`useStore`与状态交互。

我们还没有创建`reducer`，这是下一步。

```
// store/useStore.js
...
const StoreContext = createContext(initialState);

// this will act as a map of actions that will trigger state mutations 
const Actions = {};

// the reducer is called whenever a dispatch action is made.
// the action.type is a string which maps to a function in Actions.
// We apply the update to existing state, and return a new copy of state.
const reducer = (state, action) => {
  const act = Actions[action.type];
  const update = act(state);
  return { ...state, ...update };
};
... 
```

Enter fullscreen mode Exit fullscreen mode

我非常喜欢将动作/状态分成逻辑组。例如，您可能希望跟踪计数器的状态(这是经典的计数器示例)。同时，您可能还想跟踪当前的用户状态，比如用户是否登录，以及他们的偏好是什么。在某些组件中，您可能需要访问这两种不同的“状态”，因此将它们保存在一个全局存储中是有意义的。但是我们可以将动作分成逻辑组，比如一个`userActions`和一个`countActions`，这将使管理它们更加容易。

让我们在`store`目录下创建一个`countActions.js`和`userActions.js`文件。

```
// store/countActions.js

export const countInitialState = {
  count: 0
};

export const countActions = {
  increment: state => ({ count: state.count + 1 }),
  decrement: state => ({ count: state.count - 1 })
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
// store/userActions.js

export const userInitialState = {
  user: {
    loggedIn: false
  }
};

export const userActions = {
  login: state => {
    return { user: { loggedIn: true } };
  },
  logout: state => {
    return { user: { loggedIn: false } };
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这两个文件中，我们导出了`initialState`,因为我们想将`useStore.js`中的这些合并成一个`initialState`对象。

我们还导出了一个 Actions 对象，它提供了改变状态的功能。请注意，我们没有返回状态的新副本，因为我们是在实际的`reducer`和`useStore.js`中返回的。

让我们将这些导入`useStore.js`以获得完整的图片。

```
// store/useStore.js

import React, { createContext, useReducer, useContext } from "react";

import { countInitialState, countActions } from "./countActions";
import { userInitialState, userActions } from "./userActions";

// combine initial states
const initialState = {
  ...countInitialState,
  ...userInitialState
};

const StoreContext = createContext(initialState);

// combine actions
const Actions = {
  ...userActions,
  ...countActions
};

const reducer = (state, action) => {
  const act = Actions[action.type];
  const update = act(state);
  return { ...state, ...update };
};

export const StoreProvider = ({ children }) => {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <StoreContext.Provider value={{ state, dispatch }}>
      {children}
    </StoreContext.Provider>
  );
};

export const useStore = store => {
  const { state, dispatch } = useContext(StoreContext);
  return { state, dispatch };
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们做到了！胜利一圈，然后回来，我们看看如何在组件中使用它。

欢迎回来。我希望你的一圈是胜利的。让我们来看看`useStore`的行动。

首先，我们可以将初始的`App`组件包装在`<StoreProvider/>`中。

```
// App.js

import React from "react";
import ReactDOM from "react-dom";
import { StoreProvider } from "./store/useStore";
import App from "./App";

function Main() {
  return (
    <StoreProvider>
      <App />
    </StoreProvider>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<Main />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

我们将`App`包装在`StoreProvider`中，这样子组件将可以访问提供者中的值，它既是`state`又是`dispatch`。

现在，假设我们有一个`AppHeader`组件，它有一个登录/注销按钮。

```
// AppHeader.jsx

import React, {useCallback} from "react";
import { useStore } from "./store/useStore";

const AppHeader = props => {
  const { state, dispatch } = useStore();
  const login = useCallback(() => dispatch({ type: "login" }), [dispatch]);
  const logout = useCallback(() => dispatch({ type: "logout" }), [dispatch]);

  const handleClick = () => {
    loggedIn ? logout() : login();
  }

  return (
    <div>
      <button onClick={handleClick}> {loggedIn ? "Logout" : "Login"}</button>
      <span>{state.user.loggedIn ? "logged in" : "logged out"}</span>
      <span>Counter: {state.count}</span>
    </div>
  );
};

export default AppHeader; 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个代码沙盒将完全实现！

[https://codesandbox.io/embed/xpjm9p052z](https://codesandbox.io/embed/xpjm9p052z)