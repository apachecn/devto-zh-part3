# React Hook 快速入门

> 原文：<https://dev.to/drifterz13/quick-introduction-to-react-hook-4cho>

你可能听说过 React Hook 吧？React Hook 是 React 的一个新特性，它帮助我们管理功能组件的状态和生命周期。在此之前，我们只能在类组件中这样做，但是现在我们也可以在功能组件中做同样的事情。很酷吧？

在这篇文章中，我们将探索 React hook 的一些简洁的功能，并学习如何在我们的应用程序中使用它。让我们开始吧。

# 使用状态介绍

这个 API 是您可能熟悉的`setState`的简化版本。`useState`是一个采用初始状态的函数，它返回当前状态的数组和一个用于设置新状态的函数。请参见下面的示例。

```
import React from "react"

const Car = () => {
  const [speed, setSpeed] = React.useState(0)

  return (
    <div>
      <h1>speed: {speed} km/hrs</h1>
      <button onClick={() => setSpeed(speed => speed + 10)}>+10 speed</button>
    </div>
  )
} 
```

# 使用效果介绍

当状态或属性改变了它自己的值时，这个 API 帮助我们管理功能组件的生命周期。

`useEffect`是一个接受两个参数作为输入的函数。第一个参数是回调函数，第二个参数是依赖项数组。

```
// Car.js

import React, { useEffect } from "react"

function mockFetch(value) {
  return new Promise(resolve => {
    return setTimeout(() => {
      return resolve(value)
    }, 300)
  })
}

const Car = () => {
  const [speed, setSpeed] = React.useState(0)
  useEffect(() => {
    const getInitialSpeed = async () => {
      const result = await mockFetch(40)
      setSpeed(result)
    }
    getInitialSpeed()
  }, [])

  return (
    <div>
      <h1>speed: {speed} km/hrs</h1>
      <button onClick={() => setSpeed(speed => speed + 10)}>+10 speed</button>
    </div>
  )
} 
```

依赖关系数组帮助`useEffect`决定何时执行回调函数。在这种情况下，如果我们不提供任何依赖关系，`useEffect`将在组件初始化时执行一次回调。

如果您希望`useEffect`在依赖关系改变时执行回调函数，您可以这样做。请参见下面的示例。

```
// ...
useEffect(() => {
  if (gas <= 0) {
    setSpeed(0)
  }
}, [gas]) 
```

假设父组件将`gas`传递给`<Car />`组件。每次`gas`的值改变，`useEffect`都会执行回调函数。

# 自己做钩子！

现在，我们将通过组合我们在之前主题中学到的`useState`和`useEffect`来制作我们自己的钩子。

```
function useCarController(gas) {
  const [speed, setSpeed] = React.useState(0)

  useEffect(() => {
    const getInitialSpeed = async () => {
      const result = await mockFetch(40)
      setSpeed(result)
    }
    getInitialSpeed()
  }, [])

  useEffect(() => {
    if (gas <= 0) {
      setSpeed(0)
    }
  }, [gas])

  return { speed, setSpeed }
} 
```

```
const Car = ({ gas }) => {
  const { speed, setSpeed } = useCarController(gas)
  return (
    <div>
      <h2>speed: {speed} km/hrs</h2>
      <h2>gas: {gas} %</h2>
      <button onClick={() => setSpeed(speed => speed + 10)}>+10 speed</button>
    </div>
  )
} 
```

您可以看到，我们可以创建我们的**定制钩子**来从组件中提取实现细节。

# 结论

React Hook 让我和 **React** 一起工作更容易，让我的代码更干净。顺便说一下， **React Hook** 还有其他有趣的 API，我在这篇帖子里没有提到。如果你们想了解更多，可以去官方网站看看。感谢阅读和**快乐编码**。

*原帖发表于[codenothing.co](https://www.codenothing.co/blogs/react-hook-in-3-minutes/)T3】*