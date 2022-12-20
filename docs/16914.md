# useCallback hook 不是类方法的直接替代，如何避免在 useCallback 中重新呈现和访问状态/属性

> 原文：<https://dev.to/seveibar/usecallback-hook-isnt-a-drop-in-replacement-for-class-methods-how-to-avoid-rerenders-and-access-stateprops-within-usecallback-2nla>

React 钩子真的很酷。当我遇到一个主要的性能障碍时，我正在把一些库转换成钩子。

乍一看，下面的组件可能看起来做同样的事情...

```
 // Class Style

class ClassStyleComponent extends React.Component {

    state = { val: 0 }

    onAdd = () => {
        const { val } = this.state
        this.setState({ val: val + 1 })
    }

    onSubtract = () => {
        const { val } = this.state
        this.setState({ val: val - 1 })
    }

    render() {
        const { val } = this.state
        return (
            <div>
                <div>val: {val}</div>        
                <button onClick={this.onAdd}>
                    Increment
                </button>
                <button onClick={this.onSubtract}>
                    Multiply by 2
                </button>
            </div>
        )
    }
}

// Hooks Style

const NaiveHooksComponent = () => {
    const [val, changeVal] = useState(0)
    const onAdd = useCallback(() => changeVal(val + 1), [val])
    const onSubtract = useCallback(() => changeVal(val - 1), [val])

    return (
        <div>
            <div>val: {val}</div>        
            <button onClick={onAdd}>
               Increment
            </button>
            <button onClick={onSubtract}>
               Multiply by 2
            </button>
        </div>
    )
} 
```

毫无疑问，这些组件在功能上做同样的事情，但是有一个关键的性能差异。

### **在钩子样式的组件上`val`每改变一次按钮就重新渲染一次，但是在类样式的组件中，按钮只渲染一次！**

这样做的原因是`useCallback`必须在每次状态改变时重新创建回调函数。类组件回调在不创建新函数的情况下访问状态。

简单的解决方法是:**利用`useReducer`并使用传递给减速器的状态。**

下面是重新编写的钩子组件，这样按钮只呈现一次:

```
const ReducerHooksComponent = () => {
    const [val, incVal] = useReducer((val, delta) => val + delta, 0)
    const onAdd = useCallback(() => incVal(1), [])
    const onSubtract = useCallback(() => incVal(-1), [])

    return (
        <div>
            <div>val: {val}</div>        
                <button onClick={onAdd}>
                    Increment
                </button>
                <button onClick={onSubtract}>
                    Multiply by 2
                </button>
            </div>
        </div>
    )
} 
```

都修好了！按钮现在只渲染一次，因为`onAdd`和`onSubtract`不会在每次`val`改变时改变。您可以通过传递更详细的操作来使其适应更复杂的用例。

sophiebits 有一个稍微复杂一点的技术，非常适合事件回调。要使用它，我们必须定义一个名为`useEventCallback`的定制钩子。

```
 function useEventCallback(fn) {
  let ref = useRef()
  useLayoutEffect(() => {
    ref.current = fn
  })
  return useCallback((...args) => (0, ref.current)(...args), [])
}

// This looks a lot like our intuitive NaiveHooksComponent!
const HooksComponentWithEventCallbacks = () => {
    const [val, changeVal] = useState(0)

    // Swap useCallback for useEventCallback
    const onAdd = useEventCallback(() => changeVal(val + 1))
    const onSubtract = useEventCallback(() => changeVal(val - 1))

    return (
        <div>
            <div>val: {val}</div>        
            <button onClick={onAdd}>
               Increment
            </button>
            <button onClick={onSubtract}>
               Multiply by 2
            </button>
        </div>
    )
} 
```

这个例子很简单(按钮没有很大的渲染成本)，但是在重构大型应用程序时，糟糕的内存化会带来巨大的性能影响。

干杯，祝你好运采用钩子！