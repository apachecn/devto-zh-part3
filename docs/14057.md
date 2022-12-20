# Laco hooks api 和代码沙盒示例

> 原文：<https://dev.to/deam/laco-hooks-api-and-code-sandbox-examples-2b1l>

Laco 是 React 的一个简单而强大的状态管理解决方案。由 Redux 和 stated 的想法驱动。关于 Laco 的简短介绍，请查看我之前的文章。

React 刚刚发布了一个“次要”版本( **v16.8.0** )，其中包含了期待已久的 hooks api。我个人认为 hooks api 写起来要干净得多，我建议深入研究一下。这里是官方的[更新博客](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html)来做一个很好的简要概述。

使用带有渲染道具的 Laco 方法(旧方法):

```
import { Store } from 'laco'
import { Subscribe } from 'laco-react'

// Creating a new store with an initial state { count: 0 }
const CounterStore = new Store({ count: 0 })

// Implementing some actions to update the store
const increment = () => CounterStore.set((state) => ({ count: state.count + 1 }))
const decrement = () => CounterStore.set((state) => ({ count: state.count - 1 }))

const Counter = () => (
  <Subscribe to={[CounterStore]}>
    {(state) => (
      <div>
        <button onClick={decrement}>-</button>
        <span>{state.count}</span>
        <button onClick={increment}>+</button>
      </div>
    )}
  </Subscribe>
) 
```

使用 Laco 和闪亮的新钩子 api:

```
import { Store } from 'laco'
import { useStore } from 'laco-react'

// Creating a new store with an initial state { count: 0 }
const CounterStore = new Store({ count: 0 })

// Implementing some actions to update the store
const increment = () => CounterStore.set(state => ({ count: state.count + 1 }))
const decrement = () => CounterStore.set(state => ({ count: state.count - 1 }))

const Counter = () => {
  const state = useStore(CounterStore) // Takes a single store
  return (
    <div>
      <button onClick={decrement}>-</button>
      <span>{state.count}</span>
      <button onClick={increment}>+</button>
    </div>
  )
} 
```

`useStore`获取单个商店，但是您也可以导入`useStores`来获取一组商店。下面可以看到一个`useStores`的例子。

```
import { Store } from 'laco'
import { useStores } from 'laco-react'

const CounterStore = new Store({ count: 0 })
const AnotherStore = new Store({ test: "hello" })

const Counter = () => {
  const [counterState, anotherState] = useStores([CounterStore, AnotherStore])
  return <div>{anotherState.test + counterState.count}</div>
} 
```

# 代码沙盒示例

使用挂钩编写沙箱代码:

*   [Todo MVC](https://codesandbox.io/s/74yq01ovl1)
*   [计数器和开关](https://codesandbox.io/s/jvly033v63)

使用渲染道具编码沙箱:

*   [Todo MVC](https://codesandbox.io/s/207504xx1y)
*   [计数器和开关](https://codesandbox.io/s/6l7on1m473)

# 结论

我认为新的 hooks api 比 render props 做事情的方式更好，因为在 render props 中，你需要在你的孩子周围包装一个“订阅”组件，这有点复杂。在这种情况下，Hooks api 更容易阅读。