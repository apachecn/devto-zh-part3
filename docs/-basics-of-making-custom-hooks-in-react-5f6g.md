# 在 React 中制作自定义挂钩的基础

> 原文：<https://dev.to/chriswcs/-basics-of-making-custom-hooks-in-react-5f6g>

### 为什么要用钩子？

钩子是 React 中的一个新特性。它们是在组件之间共享有状态逻辑的极好方式。它们还具有难以置信的可组合性，这非常适合 React，因为 React 就是关于组合的。

请参见[钩子文档](https://reactjs.org/docs/hooks-rules.html)了解钩子基础知识的更多信息。我还会在这篇文章的最后列出一些其他的好资源。

### 规则被认为是自定义的钩子

1.  自定义挂钩的名称需要以 use like useState 开头。
2.  自定义挂钩可以调用其他挂钩。
3.  自定义钩子需要遵循使用钩子的规则，这只是从函数的顶层调用钩子。不能从条件、循环或嵌套函数中调用钩子。

### 基本示例

这里有一个简单而琐碎的例子让我们开始。这是一个名为 useCounter 的自定义钩子。这个钩子的用户可以通过传入初始计数，然后使用返回的计数和函数，轻松地创建一个计数器。

我首先在计数器组件中使用了自定义钩子。我所要做的就是调用它，然后得到我需要的状态和函数。

```
import React from 'react'
import useCounter from './useCounter'

const Counter = ({initialCount}) => {
    // here is the invocation of useCounter
    // I can pass in the initial count
    // It returns to me the count as well as two functions to help me change it
    const { count, increment, decrement } = useCounter(initialCount) 

    return (
        <div>
            <button onClick={increment}>Increment</button>
            <h1>{count}</h1>
            <button onClick={decrement}>Decrement</button>
        </div>
    )
} 
```

下面是 useCounter 的实现。它遵循上述规则。它从使用开始，并从内部调用其他钩子。其他的钩子在顶层被调用。我可以很容易地将它包含在 Counter 组件中，但是当逻辑变得复杂或需要重用时，能够将逻辑和状态提取到一个自定义钩子中是非常有用的。

```
import React from 'react'

const useCounter = initial => {
    const [count, setCount] = React.useState(initial)
    const increment = () => {
        setCount(c => c + 1)
    }

    const decrement = () => {
        setCount(c => c - 1)
    }

    return {
        count,
        increment,
        decrement,
    }
} 
```

这是另一个例子。这个使用了 useEffect 和 useState。这个钩子可以被导入到应用程序中任何需要时钟的地方。你只需要调用它，然后时钟将始终保持当前的本地时间。

```
import React from 'react'

const useClock = () => {
    const [clock, setClock] = React.useState(new Date().toLocaleTimeString())

    React.useEffect(() => {
        let intervalId = setInterval(() => {
            setClock(new Date().toLocaleTimeString())
        }, 1000)
        return () => {
            clearInterval(intervalId)
        }
    }, [])

    return {
        clock,
    }
} 
```

### 挂钩构图

到目前为止，在本文中，我已经展示了使用 React 提供的 useState 和 useEffect 基本钩子的定制钩子。钩子也可以调用其他自定义钩子！这导致了钩子图案的强大组合。

下面是在一个自定义钩子中使用另一个自定义钩子的例子。它很容易在一个钩子中实现，但是希望它演示了如何组合它们。

```
import React from 'react'

const useIsEmpty = () => {
    const [count, setCount] = React.useState(0)
    const empty = React.useMemo(() => count === 0, [count])

    const increment = () => {
        setCount(x => x + 1)
    }
    const decrement = () => {
        setCount(x => x - 1)
    }

    return {
        empty,
        increment,
        decrement,
    }
}

const useList = () => {
    const [list, setList] = React.useState([])
    const {empty, increment, decrement} = useIsEmpty()
    const addToEnd = (newItem) => {
        setList(l => [...l, newItem])
        increment()
    }
    const removeLast = () => {
        setList(l => [...l.slice(0, l.length)])
        decrement()
    }

    return {
        list,
        addToEnd,
        removeLast,
        empty
    }
} 
```

### 今天就来试试钩子吧！

看看你能用钩子做些什么。尝试用钩子实现一些你通常在 React 中会做的事情。

*   看看这个[钩子列表](https://usehooks.com/)，看看其他人在做什么。
*   查看 Tanner Linsley 关于 hooks 的这篇伟大的[帖子](https://tannerlinsley.com/blog/react-hooks-the-rebirth-of-state-management/)
*   也可以看看丹·阿布拉莫夫的这个[帖子](https://overreacted.io/making-setinterval-declarative-with-react-hooks/)。他已经发布了很多精彩的内容，人们已经帮助他翻译成多种语言！

感谢阅读！