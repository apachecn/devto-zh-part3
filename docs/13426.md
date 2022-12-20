# 用于 React 的基于简单钩子的状态管理

> 原文：<https://dev.to/yamalight/outstated---simple-hooks-based-state-management-for-react-1ji1>

如果您曾经构建过 React 应用程序，您就会知道状态管理可能是一件痛苦的事情。Redux 很棒，但对于小型(甚至是一些中型)项目来说往往太多了。
[未说明的](https://github.com/jamiebuilds/unstated)使用上下文和自定义类简化了小型项目的状态管理。这是相当可怕的，但并不真正与新的闪亮反应钩工作。
所以，我想知道我是否可以建立一个类似于《无状态》的东西，但是用所有的钩子把它变得更好。

## 介绍未完成的

是 React 的一个简单的基于钩子的状态管理解决方案。
它使用普通的 React 钩子，并允许你共享它们以获得全局状态。
哦，我有没有提到它只有 474 字节的 minzipped？

## 举例

```
import React, {useState} from 'react';
import ReactDOM from 'react-dom';
import {Provider, useStore} from 'outstated';

const store = () => {
  const [count, setCount] = useState(0);

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);
  const reset = () => setCount(0);

  return {count, increment, decrement, reset};
};

function Counter() {
  const {count, increment, decrement, reset} = useStore(store);

  return (
    <div>
      <button onClick={decrement}>-</button>
      <span>{count}</span>
      <button onClick={increment}>+</button>
      <button onClick={reset}>reset</button>
    </div>
  );
}

ReactDOM.render(
  <Provider stores={[store]}>
    <Counter />
  </Provider>,
  document.getElementById('root')
); 
```

## 它是如何工作的？

Outstated 建立在 React 钩子和上下文之上。它只是一个简单的钩子上下文包装器，只需实例化它们一次，并允许在需要的地方访问它们。

Outstated 有三个核心部分:

#### 商店

这是存储状态和更新状态的逻辑的地方。商店本质上是 React 挂钩(这意味着你可以重用它们，在外围使用它们，甚至在内部使用其他挂钩。

这里有一个非常简单的商店的例子:

```
import {useState} from 'React';

const store = () => {
  const [state, setState] = useState('hello..');

  const greet = val => setState(`hello ${val}!`);

  return {state, greet};
}; 
```

注意，它使用 React 中的`useState`钩子来管理状态。
这意味着 Outstated 不必手动管理任何与状态变化相关的事情——React 将为我们处理这些！
你必须小心不要直接改变`state`，否则你的组件将无法重新渲染。

#### `useStore`

下一件是一个`useStore`钩子。
它允许我们从树中的任何地方访问实例化的商店。
`useStore` hook 允许我们通过使用特定的存储构造函数来获取存储实例，例如:

```
function Counter() {
  const {count, decrement, increment} = useStore(counterStore);

  return (
    <div>
      <span>{count}</span>
      <button onClick={decrement}>-</button>
      <button onClick={increment}>+</button>
    </div>
  );
} 
```

#### `<Provider>`

最后一块突出的是`<Provider>`组件。
它负责给定存储的全局实例的初始化(这是必需的，因为 React 希望钩子的数量在重新渲染时保持一致，所以我们不能根据需要动态初始化它们)。
然后，它使用上下文将初始化的存储实例传递给树中的所有组件。

```
render(
  <Provider stores={[counterStore]}>
    <Counter />
  </Provider>
); 
```

### 测试

因为外围存储只是挂钩，所以我们可以在
测试中构建它们，并非常容易地断言关于它们的不同事情。
这里有一个使用 awesome `react-testing-library` :
的基本例子

```
import {act, testHook} from 'react-testing-library';

test('counter', async () => {
  let count, increment, decrement;
  testHook(() => ({count, increment, decrement} = counterStore()));

  expect(count).toBe(0);

  act(() => increment());
  expect(count).toBe(1);

  act(() => decrement());
  expect(count).toBe(0);
}); 
```

## 链接

你可以在 GitHub 上找到[。
感谢任何反馈:)](https://github.com/yamalight/outstated/)