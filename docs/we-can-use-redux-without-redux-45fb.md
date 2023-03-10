# 我们可以不用 Redux 就用 Redux。

> 原文：<https://dev.to/sosukesuzuki/we-can-use-redux-without-redux-45fb>

我们可以在没有 Redux 的情况下，通过添加新特性“钩子”来管理 Redux 这样的状态。

## useReducer + Context

*   首先像 Redux 一样定义减速器。
*   其次，创建管理状态和调度的上下文。
*   第三，用`useReducer`创建状态和减速器，并将它们传递给`StoreContext.Provider`。
*   最后，用`useContext`(或，`StoreContext.Consumer`)进入状态或调度。

```
import * as React from "react";
import * as ReactDOM from "react-dom";

type State = { count: number };
type Action = { type: "increment" | "decrement" | "reset" };
const initialState: State = { count: 0 };

function reducer(state: CounterState, action: Action): State {
  case "reset": {
    return initialState;
  }
  case "increment": {
    return { count: state.count + 1 };
  }
  case "decrement": {
    return { count: state.count - 1 };
  }
  default: {
    return state;
  }
}

const StoreContext = React.createContext<{
  state: State,
  dispatch: React.Dispatch<Action>
}>({state: null as any, dispatch: null as any});

function App() {
  const [state, dispatch] = React.useReducer(reducer, initialState);
  return (
    <StoreContext value={{ state, dispatch }}>
      <Counter />
    </StoreContext>
  )
}

function Counter() {
  const { state, dispatch } = React.useContext(StoreContext);
  return (
    <>
      <p>{state.count}</p>
      <button onClick={() => dispatch({ type: "reset" })}>reset</button>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
    </>
  )
}

ReactDOM.render(<App />, document.querySelector("#root")); 
```

我喜欢这个图案！这对于小型项目很有用。只有当我们想使用中间件时，才应该使用 Redux。

## 减速器-上下文-挂钩

我发布了一个名为`reducer-context-hook`(GitHub:[https://github.com/sosukesuzuki/reducer-context-hook](https://github.com/sosukesuzuki/reducer-context-hook)的库。这个库包装了上面的模式。

`reducer-context-hook`导出一个名为`create()`的函数。`create()`函数返回一个包含`StoreContextProvider`、`useMappedState`和`useDispatch`的对象。

```
import create from "reducer-context-hook";

export const { StoreContextProvider, useDispatch, useMappedState } = create<
  State,
  Action
>(); 
```

### StoreContextProvider

`StoreContextProvider`与上述模式中的`StoreContext.Provider`作用相同。请使用如下:

```
function App() {
  return (
    <StoreContextProvider reducer={reducer} initialState={initialState}>
      <Counter />
    </StoreContextProvider>
  );
} 
```

### useMappedState

`useMappedState`与`react-redux`中的`connect`角色相同。(仅状态)。它接受两个参数。首先是`mapState`功能。第二个是`memoizationArray`。`memoizationArray`用来像`useCallback`一样记忆。(`useCallback`用于`useMappedState`记忆功能。)请使用如下:

```
function Counter() {
  const { count } = useMappedState(
    state => ({
      count: state.count
    }),
    []
  );
  return <p>{count}</p>;
} 
```

### 使用 Dispatch

`useDispatch`返回`dispatch`。我们可以用行动来解决它。请使用如下:

```
function Increment() {
  const dispatch = useDispatch();
  const increment = React.useCallback(
    () => dispatch({ type: "increment" }),
    []
  );
  return <button onClick={increment}>+</button>;
} 
```