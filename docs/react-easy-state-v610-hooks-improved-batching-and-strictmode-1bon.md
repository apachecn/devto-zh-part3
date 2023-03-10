# React Easy State v6.1.0:挂钩、改进的批处理和 StrictMode

> 原文：<https://dev.to/solkimicreb/react-easy-state-v610-hooks-improved-batching-and-strictmode-1bon>

*[易态](https://github.com/solkimicreb/react-easy-state)是 React 的一个实用状态管理库。*

React 现在变化很快，生态系统必须跟上。Easy State 的 v6.1.0 更新增加了钩子支持、StrictMode 兼容性和批处理改进，而没有任何 API 变化。

## 挂钩

感谢[本期](https://github.com/solkimicreb/react-easy-state/issues/59)的参与者，Easy State 的核心现在支持函数组件中的钩子和局部状态存储。让我们来看看同一个计数器应用程序的三个变体，以进行快速演示。

### 具有本地状态的类

这是带有易状态的本地状态管理的老派版本，在 v6.1.0 更新后将继续工作。

```
import React, { Component } from 'react'
import { view, store } from 'react-easy-state'

class Counter extends Component {
  counter = store({ num: 0 })
  increment = () => this.counter.num++

  render() {
    return <button onClick={this.increment}>{this.counter.num}</button>
  }
}

export default view(Counter) 
```

它创建一个本地状态存储作为每个组件实例唯一的类属性。此计数器的多个实例分别递增。

### 具有本地状态的功能

该版本仅适用于 Easy State v6.1.0+和 React v16.8.0(alpha)+版本。

```
import React from 'react'
import { view, store } from 'react-easy-state'

export default view(() => {
  const counter = store({ num: 0 })
  return <button onClick={() => counter.num++}>{counter.num}</button>
}) 
```

在函数组件内部调用`store`会创建一个本地状态存储，它对于每个组件实例都是唯一的。此计数器的多个实例分别递增。

> `store`函数已经被改进，当它从一个函数组件中被调用时，表现得像一个 React 钩子。

### 具有全局状态的功能

这个版本在更新前有效，更新后也将继续有效。

```
import React from 'react'
import { view, store } from 'react-easy-state'

// a single store is created outside of the component
const counter = store({ num: 0 })
const increment = () => counter.num++

export default view(() => (
  <button onClick={increment}>{counter.num}</button>
)) 
```

全局`store`在所有组件之间共享。上述计数器的多个实例一起递增。

## 表现

这个版本包括一个相当大的核心变化，值得一些基准测试。我为 React 16.8 更新了一些 [js-framework-benchmark](https://github.com/krausest/js-framework-benchmark) 条目，并重写了简单状态实现，只使用函数组件。这些是在我的 MacBook 上运行 10x 基准测试的结果。

[![Benchmark results](img/8ba7a46b870126c36aa1e423365cfdf4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_lDtvwtA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9wk866dcb28vwqva61vb.png)

基准测试是为了更好地衡量，请在下结论之前考虑以下几点。

*   只要用户获得流畅的 60 fps 体验，这些性能指标并不重要。
*   大多数 React 项目还没有更新到使用钩子。
*   这种基准测试对于排除非常慢的库是有用的，但是您不应该根据微小的性能差异来做出决定。我使用它们来避免在更新时增加意外的性能问题。

## 其他变化

*   为本机 DOM 事件处理程序添加了自动同步批处理以提高性能。

*   重写了批处理实现以使用 React 的`unstable_batchedUpdates`。

    *   如果一个相关的商店和它们的道具都在一个同步批处理中被改变，组件将不再渲染两次。
    *   组件总是在单个批处理中以父子顺序呈现。
    *   `batch`函数只是`unstable_batchedUpdates`的别名。
*   修复了 ES6 地图和集合的一个错误，其中包含嵌套数据的对象值有时没有反应。

*   测试用`<StrictMode>`包装，以保证与明天的 React 兼容。

*   添加了 React Native 示例和 React Native 的测试覆盖率。

*   添加了`batch`功能的类型。([感谢 moritzuehling](https://github.com/solkimicreb/react-easy-state/pull/70) )

*   改进的[文档](https://github.com/solkimicreb/react-easy-state#usage)。

## 未来

最激动人心的并发 React 更新仍在进行中。在`ConcurrentMode`和`scheduler`包稳定后，Easy State 可能会收到另一个更新，这将让它挂钩到 React 的新异步调度程序，而不是当前的自定义批处理。

如果这篇文章引起了你的兴趣，请分享出来。此外，去看看[易邦回购](https://github.com/solkimicreb/react-easy-state)吧，走之前留下一颗星。

*谢谢！*