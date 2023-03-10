# 使用带有 React 的 Typescript 的初学者指南

> 原文：<https://dev.to/bmcmahen/a-beginners-guide-to-using-typescript-with-react-7m6>

在过去的几个月里，我一直在使用 Typescript 开发 React 应用程序和库，我想我应该分享一下我在这一过程中所学到的一些东西。这些是我在 Typescript 中使用的模式，在 80%的情况下都会做出反应。

开发 React 应用值得学习 Typescript 吗？绝对的。我发现拥有健壮的类型会带来更可靠的代码和更快的迭代，尤其是在更大的代码库中。一开始你可能会感到沮丧，但是随着你的努力，你会发现最少的额外模板是非常值得的。

如果你被什么卡住了，记住你总是可以输入一些东西作为`any`。任何都是你的朋友！

让我们来看例子。

#### 你的基本反应组件与类型脚本

那么标准的 react 组件在 typescript 中是什么样子的呢？让我们将其与标准的 javascript react 组件进行比较。

```
import React from 'react'
import PropTypes from 'prop-types'

export function StandardComponent({ children, title = 'Dr.' }) {
  return (
    <div>
      {title}: {children}
    </div>
  )
}

StandardComponent.propTypes = {
  title: PropTypes.string,
  children: PropTypes.node.isRequired,
} 
```

现在是打字稿版本:

```
import React, { ReactNode } from 'react'

export type StandardComponentProps = {
  title?: string;
  children: ReactNode;
}

export function StandardComponent({
  children,
  title = 'Dr.',
}: StandardComponentProps) {
  return (
    <div>
      {title}: {children}
    </div>
  )
} 
```

很相似，是吧？我们已经用 typescript 类型替换了 propTypes。我们的道具仍然是可选的，而儿童道具是必需的。我们已经导出了我们的类型，以防其他组件需要引用它。

#### 扩展标准 HTML 属性

如果我们希望父组件能够提供额外的类型化`div`属性，比如`aria-hidden`、`style`或`className`，我们可以在`type`中定义这些属性，或者扩展一个内置类型。在下面的例子中，我们说我们的组件接受除了`title`和`children`之外的任何标准`div`道具。

```
import * as React from 'react'

export type SpreadingExampleProps = {
  title?: string;
  children: React.ReactNode;
} & React.HTMLAttributes<HTMLDivElement>;

export function SpreadingExample({
  children,
  title = 'Dr.',
  ...other
}: SpreadingExampleProps) {
  return (
    <div {...other}>
      {title}: {children}
    </div>
  )
} 
```

#### 处理事件

我们可以键入我们的事件处理程序，以确保我们的事件参数类型正确。下面的例子演示了实现这一点的各种方法:

```
export type EventHandlerProps = {
  onClick: (e: React.MouseEvent) => void;
}

export function EventHandler({ onClick }: EventHandlerProps) {
  // handle focus events in a separate function
  function onFocus(e: React.FocusEvent) {
    console.log('Focused!', e.currentTarget)
  }

  return (
    <button
      onClick={onClick}
      onFocus={onFocus}
      onKeyDown={e => {
        // When using an inline function, the appropriate argument signature
        // is provided for us
      }}
    >
      Click me!
    </button>
  )
} 
```

不确定使用哪个参数签名？在编辑器中，尝试将光标悬停在相关的事件处理程序属性上。

#### 使用字符串文字

你有没有一个道具需要一个字符串来匹配一组预定义的选项？为此，您可以使用 typescript 字符串文字。

```
type Props = {
  title: "senior" | "junior";
}

function Label({ title }: Props) {
  return <div>Title: {title}</div>
} 
```

现在如果标题是除了`senior`或者`junior`之外的任何东西，typescript 都会吼你。

#### 对 react 组件使用泛型

这更像是一个高级特性，但也是一个非常强大的特性。通常，您将在 react 组件中定义数据类型及其特定属性。假设您的组件需要一个配置文件对象。

```
type ProfileType = {
  name: string;
  image: string;
  age: number | null;
}

type ProfilesProps = {
  profiles: Array<ProfileType>;
}

function Profiles(props: ProfilesProps) {
  // render a set of profiles
} 
```

但是现在让我们假设你有一个组件可以接受任何类型的数组。泛型类似于邮寄包裹。courior(我们的组件)不需要知道您发送的包裹的确切内容，但是发送者(父组件)希望接收者得到他们发送的内容。

我们是这样做的:

```
type GenericsExampleProps<T> = {
  children: (item: T) => React.ReactNode;
  items: Array<T>;
}

export function GenericsExample<T>({
  items,
  children,
}: GenericsExampleProps<T>) {
  return (
    <div>
      {items.map(item => {
        return children(item)
      })}
    </div>
  )
} 
```

这个例子有点奇怪……但是它证明了这一点。该组件接受任何类型的项目数组，遍历该数组并调用子项作为 item 对象的呈现函数。当我们的父组件作为子组件提供渲染回调时，`item`将被正确输入！

不明白吗？没关系。我仍然没有完全理解泛型，但是你不太可能经常需要它。你越是使用 typescript，它就越有意义。

#### 打字挂钩

钩子基本上是开箱即用的。两个例外有时是`useRef`和`useReducer`。下面的例子演示了我们如何输入引用。

```
import * as React from 'react'

type HooksExampleProps = {}

export function HooksExample(props: HooksExampleProps) {
  const [count, setCount] = React.useState(0)
  const ref = React.useRef<HTMLDivElement | null>(null)

  // start our timer
  React.useEffect(
    () => {
      const timer = setInterval(() => {
        setCount(count + 1)
      }, 1000)

      return () => clearTimeout(timer)
    },
    [count]
  )

  // measure our element
  React.useEffect(
    () => {
      if (ref.current) {
        console.log(ref.current.getBoundingClientRect())
      }
    },
    [ref]
  )

  return <div ref={ref}>Count: {count}</div>
} 
```

我们的状态是自动键入的，但是我们已经手动键入了我们的`ref`,以指示它将是 null 或者包含一个`div`元素。当我们在`useEffect`函数中访问我们的引用时，我们需要确保它不为空。

#### 键入一个减速器

reducer 稍微复杂一点，但是正确地输入它真的很好。

```
// Yeah, I don't understand this either. But it gives us nice typing
// for our reducer actions.
type Action<K, V = void> = V extends void ? { type: K } : { type: K } & V

// our search response type
type Response = {
  id: number;
  title: string;
}

// reducer actions. These are what you'll "dispatch"
export type ActionType =
  | Action<'QUERY', { value: string }>
  | Action<'SEARCH', { value: Array<Response> }>

// the form that our reducer state takes
type StateType = {
  searchResponse: Array<Response>;
  query: string;
}

// our default state
const initialState: StateType = {
  searchResponse: [];
  query: '';
}

// the actual reducer
function reducer(state: StateType, action: ActionType) {
  switch (action.type) {
    case 'QUERY':
      return {
        ...state,
        query: action.value,
      }

    case 'SEARCH':
      return {
        ...state,
        searchResponse: action.value,
      }
  }
}

type ReducerExampleProps = {
  query: string;
}

export function ReducerExample({ query }: ReducerExampleProps) {
  const [state, dispatch] = React.useReducer(reducer, initialState)

  React.useEffect(
    () => {
      if (query) {
        // emulate async query
        setTimeout(() => {
          dispatch({
            type: 'SEARCH',
            value: [{ id: 1, title: 'Hello world' }],
          })
        }, 1000)
      }
    },
    [query]
  )

  return state.searchResponse.map(response => (
    <div key={response.id}>{response.title}</div>
  ))
} 
```

#### 使用`typeof`和`keyof`来键入组件变量

假设我们想要构建一个具有各种外观的按钮，每个外观都用一组键和样式定义在一个对象中，就像这样:

```
const styles = {
  primary: {
    color: 'blue',
  },
  danger: {
    color: 'red',
  },
} 
```

我们的按钮组件应该接受一个`type` prop，它可以是`styles`对象的任何键(即‘primary’或‘danger’)。我们可以很容易地输入:

```
const styles = {
  primary: {
    color: 'blue',
  },
  danger: {
    color: 'red',
  },
}

// creates a reusable type from the styles object
type StylesType = typeof styles

// ButtonType = any key in styles
export type ButtonType = keyof StylesType

type ButtonProps = {
  type: ButtonType
}

export function Button({ type = 'primary' }: ButtonProps) {
  return <button style={styles[type]}>My styled button</button>
} 
```

这些例子应该能让你达到 80%的目标。如果您遇到困难，通常值得查看现有的开源示例。

[Sancho UI](https://github.com/bmcmahen/sancho) 是一套用 typescript 和 emotion 构建的 react 组件。

[Blueprint](https://github.com/palantir/blueprint) 是另一套用 typescript 构建的 react 组件。