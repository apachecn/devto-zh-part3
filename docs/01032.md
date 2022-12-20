# redux 在 12 行代码中重新实现

> 原文：<https://dev.to/daleljefferson/redux-reimplemented-in-12-lines-of-code-3fib>

我喜欢重新实现我使用的库，这让我更好地理解它们是如何工作的，下面是我对 Redux 的最小实现。

```
const createStore = reducer => {
  let onChange;
  let state = reducer(undefined, {});
  return {
    getState: () => state,
    subscribe: fn => (onChange = fn),
    dispatch: action => {
      state = reducer(state, action);
      onChange();
    }
  };
}; 
```

这适用于下面 Redux 主页上的例子。

```
const counter = (state = 0, action) => {
  switch (action.type) {
    case "INCREMENT":
      return state + 1;
    case "DECREMENT":
      return state - 1;
    default:
      return state;
  }
};

const store = createStore(counter);

store.subscribe(() => console.log(store.getState()));

store.dispatch({type: "INCREMENT"}); // 1
store.dispatch({type: "INCREMENT"}); // 2
store.dispatch({type: "DECREMENT"}); // 1 
```

显然这只是真正的 Redux API 的一小部分。