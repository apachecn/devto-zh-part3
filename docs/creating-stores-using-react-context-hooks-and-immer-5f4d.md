# 使用 React context、hooks 和 Immer 创建商店

> 原文：<https://dev.to/ferdaber/creating-stores-using-react-context-hooks-and-immer-5f4d>

在创建任何大中型 React 应用程序时，拥有一个存储来包含您的核心应用程序数据是非常有用的。我们不希望从分散的组件中的 API 加载相同的数据，也不希望必须处理道具钻取问题(在 React 树中向下传递多个级别的道具)。

有许多应用程序数据管理解决方案，其中 Redux 和 MobX 是最受欢迎的两个。在本文中，我们将使用 React context、hooks 和 Immer 创建自己的商店管理解决方案。

Immer 是一个很棒的库，它允许你在 JavaScript 中对非原始数据结构进行突变，同时仍然保留旧数据。它通过创建您想要编辑的数据结构的“草稿副本”,遍历它并创建 ES6 代理来捕获您执行的任何变化。然后记录这些变化，并根据原始数据结构的深层副本进行重放。

首先，我们将创建两个 React 上下文:一个包含存储数据，另一个允许编辑数据。我们将使用 React 的`createContext` API:
来实现这一点

```
const initialState = {
  /* whatever you want */
}

const StateContext = React.createContext(initialState)
const UpdateContext = React.createContext(null) // soon to be populated with an updater function 
```

我们甚至可以聪明地让`UpdateContext`提供者有一个默认的更新函数，在开发模式中抛出一个错误，以确保我们总是有一个封闭的提供者:

```
function invariantUpdaterFn() {
  if (process.env.NODE_ENV === 'development') {
    throw new Error('Updater was called without an enclosing provider.')
  }
}
const UpdateContext = React.createContext(invariantUpdaterFn) 
```

接下来，我们希望将这两个上下文封装到一个提供者中，以便它们总是相互配对。

```
export function StoreProvider({ children }) {
  return (
    <UpdateContext.Provider>
      <StateContext.Provider>{children}</StateContext.Provider>
    </UpdateContext.Provider>
  )
} 
```

但是我们实际上想要为我们的提供者添加值，以便它们实际上可以被更新！我们可以利用一个内置的挂钩:

```
export function StoreProvider({ children }) {
  const [state, setState] = React.useState(initialState)
  return (
    <UpdateContext.Provider value={setState}>
      <StateContext.Provider value={state}>{children}</StateContext.Provider>
    </UpdateContext.Provider>
  )
} 
```

这种方法适用于最简单的 updater 函数，消费者只需传入一个全新的商店状态，整个状态就会被替换。尽管我们想要更好的东西；我们希望能够利用 Immer 的功能来编辑状态，这给了用户最大的权力，同时也保留了旧的状态。为此，我们可以使用 reducer 函数，使用 React 的`useReducer`钩子:

```
import produce from 'immer'

export function StoreProvider({ children }) {
  const [state, updater] = React.useReducer(produce, initialState)
  return (
    <UpdateContext.Provider value={updater}>
      <StateContext.Provider value={state}>
        {children}
      </StateContext.Provider>
    </UpdateContext.Provider>
  )
} 
```

`useReducer`钩子将一个减速器函数作为它的第一个参数，初始状态作为第二个参数。reducer 函数本身有一个签名，将当前状态作为第一个参数，将某种动作作为第二个参数。

动作本身可以是任何东西(在 canonical Redux 中，它是一个带有一个`type`和一个`payload`的普通对象)。然而，在我们的例子中，动作将是某个更新器函数，它获取状态的代理副本，并对其进行变异。幸运的是，这正是 Immer 的`produce`函数所期望的函数签名(因为它被建模为一个缩减器)！所以我们可以将`produce`功能原样传递给`useReducer`。

这就完成了我们的提供者的实现，它实现了更新存储状态的必要逻辑。现在，我们需要为用户提供一种方法，让他们能够获取存储状态，并在必要时进行更新。我们可以为此创建一个自定义挂钩！

```
export function useHook() {
  return [useContext(StateContext), useContext(UpdateContext)]
} 
```

这个定制钩子将返回一个元组，这个元组可以被分解成状态和 updater 函数，很像`useState`钩子。

我们的实现完成后，应用程序将会这样使用它(我们最喜欢的例子是 Todo 应用程序):

```
// store.js
import React from 'react'
import produce from 'immer'

// an array of todos, where a todo looks like this: 
// { id: string; title: string; isCompleted: boolean }
const initialTodos = []

const StateContext = React.createContext(initialTodos)
const UpdateContext = React.createContext(null)

export function TodosProvider({ children }) {
  const [todos, updateTodos] = React.useReducer(produce, initialTodos)
  return (
    <UpdateContext.Provider value={updateTodos}>
      <StateContext.Provider value={todos}>
        {children}
      </StateContext.Provider>
    </UpdateContext.Provider>
  )
}

export function useTodos() {
  return [React.useContext(StateContext), React.useContext(UpdateContext)]
}

// app.js
import { TodosProvider } from 'store'

export function App() {
  return (
    <TodosProvider>
      {/* ... some deep tree of components */}
    </TodosProvider>
  )
}

// todo-list.js
import { useTodos } from 'store'

export function TodoList() {
  const [todos, updateTodos] = useTodos()

  const completeTodo = id =>
    updateTodos(todos => {
      todos.find(todo => todo.id === id).isCompleted = true
    })

  const deleteTodo = id =>
    updateTodos(todos => {
      const todoIdxToDelete = todos.findIndex(todo => todo.id === id)
      todos.splice(todoIdxToDelete, 1)
    })

  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          <span>{todo.title}</span>
          <button>Complete</button>
          <button>Delete</button>
        </li>
      ))}
    </ul>
  )
} 
```

就这么简单！我们创建商店的逻辑是如此的普通，以至于我们甚至可以把它包装成我们自己的`createStore`函数:

```
// create-store.js
import React from 'react'
import produce from 'immer'

export function createStore(initialState) {
  const StateContext = React.createContext(initialState)
  const UpdateContext = React.createContext(null)

  function StoreProvider({ children }) {
    const [state, updateState] = React.useReducer(produce, initialState)
    return (
      <UpdateContext.Provider value={updateState}>
        <StateContext.Provider value={state}>
          {children}
        </StateContext.Provider>
      </UpdateContext.Provider>
    )
  }

  function useStore() {
    return [React.useContext(StateContext), React.useContext(UpdateContext)]
  }

  return { Provider: StoreProvider, useStore }
}

// app.js
import { createStore } from 'create-store'

const TodosStore = createStore([])

export const useTodos = TodosStore.useStore

export function App() {
  return <TodosStore.Provider>{/* ... */}</TodosStore.Provider>
}

// todo-list
import { useTodos } from 'app'

export function TodoList() {
  const [todos, updateTodos] = useTodos()
  /* ... */
} 
```

这种方法非常适用于小型应用程序，在这些应用程序中，React 树很浅，调试不会花费很长时间。然而，对于较大的应用程序或较大的团队，您可能希望使用 Redux，因为它强制实施特定的风格，并且还允许您通过检查开发工具来更好地调试操作。