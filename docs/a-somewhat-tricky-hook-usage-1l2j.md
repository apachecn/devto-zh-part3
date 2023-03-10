# 有点棘手的钩子用法

> 原文：<https://dev.to/solkimicreb/a-somewhat-tricky-hook-usage-1l2j>

我最近更新了一个 React 状态管理库，希望将它的 API 从两个包装函数减少到一个钩子中。我没有成功，但最终还是得到了一些好东西。以下是原因和方法。

## 起点

该库有以下用于处理全局状态的 API

```
import React from 'react'
import { store, view } from 'react-easy-state'

const count = store({ num: 0 })
const increment = () => count.num++

export default view(() => <button onClick={increment}>{count.num}</button>) 
```

...和地方政府。

```
import React, { Component } from 'react'
import { view, store } from 'react-easy-state'

class Counter extends Component {
  counter = store({ num: 0 })
  increment = () => counter.num++

  render() {
    return <button onClick={this.increment}>{this.counter.num}</button>
  }
}

export default view(Counter) 
```

最初，我希望将 API 减少到如下所示。

```
import React from 'react'
import useStore from 'react-easy-state'

export default function Counter () {
  const counter = useStore({ num: 0 })
  const increment = () => counter.num++

  return <button onClick={increment}>{counter.num}</button>
} 
```

> 请注意在这个失败的实验中,`view` HOC 是如何消失的。

## 议题

*   默认情况下，状态管理库应该使用`shouldComponentUpdate`或`memo`来优化重新渲染。这种渲染式的救助是做不到的，[也不应该用钩子做](https://overreacted.io/why-isnt-x-a-hook/#not-a-hook-usebailout)。

*   一些状态管理库需要知道关于组件的细节——比如它们期望什么道具，或者它们从别处使用什么数据。钩子没有包装组件，所以它们不能提供这种更高层次的信息。

*   一个`useStore`钩子对于函数组件中的局部状态来说是很好的，但是对于类组件中的全局状态和局部状态来说却是无效的。

以上所有问题都可以用高阶元件解决。尽管如此，仍然需要钩子来启用函数组件的本地状态。

## 第二次迭代

在做了一些修补和原型制作之后，我放弃了将 API 简化成一个钩子的想法，但我尽量不扩展它，至少不增加新的功能。这次我成功了，通过一个有点非正统的钩子用法。

我重载了`store`函数，使其在函数组件内部被调用时表现得像一个钩子，但在其他情况下保持原来的行为。结果，我得到了本地状态的语法

```
import React from 'react'
import { view, store } from 'react-easy-state'

export default view(() => {
  const count = store({ num: 0 })
  const increment = () => count.num++

  return <button onClick={increment}>{count.num}</button>
}) 
```

...和全球状态。

```
import React from 'react'
import { view, store } from 'react-easy-state'

const count = store({ num: 0 })
const increment = () => count.num++

export default view(() => {
  return <button onClick={increment}>{count.num}</button>
}) 
```

> 请注意，唯一的区别是状态存储的位置。

## 实现

这是一个简化版本的`view` HOC，它在包装的函数组件呈现时将`isInsideFunctionComponent`标志切换为真。这不能用钩子来完成。

```
import { memo } from 'react'

export let isInsideFunctionComponent = false

export default function view (Comp) {
  return memo(props => {
    isInsideFuntionComponent = true
    try {
      return Comp(props)
    } finally {
      isInsideFunctionComponent = false
    }
  })
} 
```

下面的代码片段是`store`包装器的简化版本。它根据`isInsideFunctionComponent`标志决定它的行为是像本地商店还是像全局商店。

```
import { useMemo } from 'react'
import { isInsideFunctionComponent } from './view'

export default function store (obj) {
  if (isInsideFunctionComponent) {
    return useMemo(() => storeImplementation(obj), [])
  }
  return storeImplementation(obj)
} 
```

如果`store`在函数组件内部被调用，它返回一个记忆化的本地状态存储，否则它返回一个全局存储。

## 但是...

*   **不叫`useStore`。**

`useX`命名只是一个惯例，可以用一个好的理由来打破它。人体工程学的 API 设计对我来说已经足够好了。

*   **它是一个`if`语句后面的钩子。**

如果你熟悉挂钩的[规则，那么`if`的说法可能会让你皱眉，但它实际上是坚持规则而不是违反规则。](https://reactjs.org/docs/hooks-rules.html)

每当从功能组件调用`store`时，`isInsideFunctionComponent`标志为真，进入`if`程序块。从功能组件的角度来看，`useMemo`并不在`if`块之后，每次组件被渲染时，它都在正确的位置被调用。

*   这是魔术(又名肮脏的黑客)。

这就是我喜欢 JavaScript 的原因。一个人可以用得体的语言知识和轻松的心态来节省巨大的努力。如果你知道你在做什么，越简单越好。

*   react-easy-state 正在用 ES6 代理改变语言行为。
*   我有一个围绕' deprecated' `with`关键字构建的库，我认为它很棒。
*   我喜欢使用稀疏数组和`delete`关键字来存储数据(在重复数据删除优先级队列中)。
*   React 团队开始抛出承诺。

这种“黑客”很棒，只要它们不会给用户带来边缘情况和隐藏的陷阱。

* * *

如果这篇文章引起了你的兴趣，请分享出来。此外，如果您有一些状态要管理，请查看 [React Easy State repo](https://github.com/solkimicreb/react-easy-state) 。

*谢谢！*