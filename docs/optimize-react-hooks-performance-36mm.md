# 优化 React 挂钩性能

> 原文：<https://dev.to/rahmanfadhil/optimize-react-hooks-performance-36mm>

*阅读原文[此处](https://rahmanfadhil.com/optimize-react-hooks)T3】*

根据 React [官方文档](https://reactjs.org) , **钩子是让你从功能组件**中“挂钩”React 状态和生命周期特性的函数。这意味着您现在可以完全控制您的功能组件，就像其他基于类的组件一样。

所以，如果你对什么是反作用钩子有很好的理解，看看这个简单的反作用应用程序。

## 入门

我在我的 GitHub 上发布了一个类似的项目，你可以在这里克隆它。

让我们从使用 [Create React App](https://facebook.github.io/create-react-app) 初始化 React 应用程序开始。

```
$ npx create-react-app app-name 
```

然后，编辑`./src/App.js`文件。

```
// ./src/App.js

import React, { useState } from "react"
import Counter from "./Counter"

export default function App() {
  const [value, setValue] = useState("")

  return (
    <div>
      <input
        type="text"
        onChange={e => setValue(e.target.value)}
        value={value}
      />
      <Counter />
    </div>
  )
} 
```

在`App`组件中，我们使用的是从`./src/Counter.js`文件导入的`Counter`组件，这个文件我们还没有创建。让我们通过创建它来修复它。

```
// ./src/Counter.js

import React, { useState, useRef } from "react"

export default function Counter() {
  const [counter, setCounter] = useState(0)
  const renders = useRef(0)

  return (
    <div>
      <div>Counter: {counter}</div>
      <div>Renders: {renders.current++}</div>
      <button onClick={() => setCounter(counter + 1)}>Increase Counter</button>
    </div>
  )
} 
```

在本例中，有两个功能组件。首先是`App`组件，它包含`useState`钩子来控制输入值。

第二个是`Counter`组件，它包含保存计数器状态的`useState`钩子和计算该组件更新或重新呈现次数的`useRef`钩子。

试着运行这个应用程序，玩玩它。目前您应该不会看到性能问题。所以，我们来看看它有没有问题。

## 一个大问题

当您按下“增加计数器”按钮几次时，渲染计数器显示与计数器状态完全相同的数字。这意味着每当我们的计数器状态改变时,`Counter`组件就会更新。

但是当您键入`App`组件文本输入时，您会看到渲染计数器也增加了。这意味着无论何时我们的文本输入状态改变，我们的`Counter`组件都会重新呈现。

那么，如何才能修复呢？

## 记忆元件

React 16.6(及更高)自带[高阶组件](https://reactjs.org/docs/higher-order-components.html)称为 [`React.memo`](https://reactjs.org/docs/react-api.html#reactmemo) 。这与 [`React.PureComponent`](https://reactjs.org/docs/react-api.html#reactpurecomponent) 非常相似，只是用功能组件代替了类。

基本上，它**帮助我们控制组件何时重新呈现**。

> “在计算中，记忆化是一种优化技术，主要用于通过存储昂贵的函数调用的结果并在相同的输入再次出现时返回缓存的结果来加速计算机程序”――维基百科

让我们记住我们的`Counter`组件，以防止不必要的重新呈现。

```
// ./src/Counter.js

import React, { useState, useRef } from "react"

export default React.memo(() => {
  const [counter, setCounter] = useState(0)
  const renders = useRef(0)

  return (
    <div>
      <div>Counter: {counter}</div>
      <div>Renders: {renders.current++}</div>
      <button onClick={() => setCounter(counter + 1)}>Increase Counter</button>
    </div>
  )
}) 
```

简单对吗？让我们来看看我们的新应用程序，当我们输入文本输入时，您会看到`Counter`组件没有重新呈现。

## 问题依然存在

`React.memo`很棒。但是，问题还没有解决。

在记忆反应元件时，有一件事需要记住。当父组件将 props 传递给内存化的 React 组件时，事情变得有点奇怪。

当传递**数字或字符串**作为道具时，记忆组件将检查道具是否被改变。只有当字符串数量改变时，组件才会重新呈现。

但是当传递**函数或对象**时，记忆化的组件总是会在父组件重新呈现时重新呈现。发生这种情况是因为每当父组件传递这种数据时，内存组件**无法检查该函数或对象是否被更改**。

为了证明这一点，让我们尝试将一个道具传递给`Counter`组件。

```
// ./src/App.js

import React, { useState } from "react"
import Counter from "./Counter"

export default function App() {
  const [value, setValue] = useState("")

  return (
    <div>
      <input
        type="text"
        onChange={e => setValue(e.target.value)}
        value={value}
      />
      <Counter greeting="Hello world!" />
    </div>
  )
} 
```

在这种情况下，我们传递包含字符串的`greeting` prop。然后，尝试运行应用程序，你会看到我们的应用程序将按照我们的预期运行。现在，尝试传递一个函数或对象。

```
// ./src/App.js

import React, { useState } from "react"
import Counter from "./Counter"

export default function App() {
  const [value, setValue] = useState("")

  return (
    <div>
      <input
        type="text"
        onChange={e => setValue(e.target.value)}
        value={value}
      />
      <Counter
        addHello={() => setValue(value + "Hello!")}
        myObject={{ key: "value" }}
      />
    </div>
  )
} 
```

您会注意到，每当您在文本字段中键入内容时，`Counter`就会重新呈现。那么，我们如何解决这个问题呢...又来了？

## 记忆功能

我们可以使用 [`useCallback`](https://reactjs.org/docs/hooks-reference.html#usecallback) 钩子来记忆我们通过道具传递的回调。

钩子返回我们函数的一个记忆版本，只有当其中一个依赖关系改变时，它才会改变。换句话说，除非状态值已经改变，否则我们的函数永远不会被重新创建。让我们在应用程序中实现这一点。

```
// ./src/App.js

import React, { useState, useCallback } from "react"
import Counter from "./Counter"

export default function App() {
  const [value, setValue] = useState("")

  const addHello = useCallback(() => setValue(value + "Hello!"), [value])

  return (
    <div>
      <input
        type="text"
        onChange={e => setValue(e.target.value)}
        value={value}
      />
      <Counter addHello={addHello} myObject={{ key: "value" }} />
    </div>
  )
} 
```

当你有不止一个状态钩子时，这个方法**非常有用。记忆功能仅在所选状态改变时更新。为了证明这一点，让我们添加另一个输入字段。** 

```
// ./src/App.js

import React, { useState, useCallback } from "react"
import Counter from "./Counter"

export default function App() {
  const [value, setValue] = useState("")
  const [newValue, setNewValue] = useState("")

  const addHello = useCallback(() => setValue(value + "Hello!"), [value])

  return (
    <div>
      <input
        type="text"
        onChange={e => setValue(e.target.value)}
        value={value}
      />
      <input
        type="text"
        onChange={e => setNewValue(e.target.value)}
        value={newValue}
      />
      <Counter addHello={addHello} myObject={{ key: "value" }} />
    </div>
  )
} 
```

现在，当我们输入新的文本字段时，`Counter`组件不会重新呈现。因为我们的记忆函数只在`value`状态改变时更新。

我们成功地用`useCallback`钩子记住了我们的功能。但是，问题仍然存在...

## 记忆对象

现在我们知道了如何记忆我们的函数，但是关于记忆还有最后一件事你应该知道。

目前，无论何时状态改变，我们的`Counter`组件仍然会被重新呈现。这是因为`myObject`道具还没有被记忆。那么，我们怎样才能记住这种东西呢？

[`useMemo`](https://reactjs.org/docs/hooks-reference.html#usememo) hook 让你通过传递一个“创建”函数和一个依赖数组来记忆一个值(包括对象)。只有当其中一个依赖关系发生变化时，该值才会重新计算(就像`useCallback` hook 一样)。

让我们应用它，看看会发生什么。

```
// ./src/App.js

import React, { useState, useCallback } from "react"
import Counter from "./Counter"

export default function App() {
  const [value, setValue] = useState("")
  const [newValue, setNewValue] = useState("")

  const addHello = useCallback(() => setValue(value + "Hello!"), [value])
  const myObject = useMemo(() => ({ key: "value" }), [])

  return (
    <div>
      <input
        type="text"
        onChange={e => setValue(e.target.value)}
        value={value}
      />
      <input
        type="text"
        onChange={e => setNewValue(e.target.value)}
        value={newValue}
      />
      <Counter addHello={addHello} myObject={myObject} />
    </div>
  )
} 
```

通过添加这些更改，您现在可以将属性传递给内存化的组件，而不会损失良好的性能。