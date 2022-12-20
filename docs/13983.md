# 最后，UI 组件意味着什么？

> 原文：<https://dev.to/revskill10/what-does-a-ui-component-mean-finally--13nl>

我需要给自己澄清一些术语，我发现它们太模糊，或者在日常编程中没有用，其中一些是:UI、组件、行为、视图、模型。

我可以把一个`Component`描述为:

```
Component = function(behavior, view) 
```

`behavior`是什么意思？

```
behavior = function(props) -> model 
```

是的，`behavior`是一个函数，它接收`props`作为输入，并返回一个`model`

在这种情况下,`model`只是任何数据结构。

一个`view`是什么意思？

```
view = function(model) -> UI 
```

一个`view`是接收一个`model`并返回一个`UI`的函数。

`UI`表示用户界面，是用户可以看到、感受到并与之交互的东西。

## 举例:

让我们用 React 和 Hooks 做一个`Counter`组件吧！

这是`makeComponent`，它接收一个`behavior`，一个`view`，并返回一个`Component`

```
import React from 'react'

const Component = ({behavior, view, ...rest}) => {
  const props = behavior(rest)
  const View = view
  return <View {...props} /> }
const makeComponent = (behavior, view) => {
  return (props) => <Component {...props} behaviour={behaviour} view={view} /> }

export default makeComponent 
```

`Counter`行为:

```
import {useState, useCallback} from 'react'

const behavior = ({initialCount}) => {
  const [count, setCount] = useState(initialCount)
  const increment = useCallback(() => setCount(count + 1))
  const decrement = useCallback(() => setCount(count - 1))
  return {initialCount, count, increment, decrement}
} 
```

`Counter`视图:

```
const view = ({initialCount, count, increment, decrement}) => {
  return (
    <div>
        <button onClick={increment}>+</button>
        <div>Current count: {count}</div>
        <button onClick={decrement}>-</button>
    </div>
  )
} 
```

最后，我们来做一个`Counter`组件:

```
const Counter = makeComponent(behavior, view)

React.render(<Counter initialCount={0} />, body) 
```

瞧，我得到了一个干净的反应元件！

## 但是，这是`MVC`？

是啊！`behavior`就是`controller`的确切含义。

## 那么，a `Component`和`MVC`是一样的吗？

不要！只有两个参数:V 和 C，没有 m。