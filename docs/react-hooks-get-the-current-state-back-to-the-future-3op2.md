# React hooks:获取当前状态，回到未来

> 原文：<https://dev.to/scastiel/react-hooks-get-the-current-state-back-to-the-future-3op2>

*这篇文章不是最近写的，但仍然相关！我仍然经常看到开发人员在这个问题上遇到麻烦。我在我的新书《React 钩子开发者指南 T2》中谈到了这个话题和更多关于 React 钩子的内容。*

React 钩子真的很棒，但是我玩得越多，我发现的技巧就越多，有时我会花很多时间去弄清楚为什么我的代码没有做它应该做的事情。

我的最后一个问题是:我想从异步触发的回调中访问组件的当前状态(用 [`useState`](https://reactjs.org/docs/hooks-state.html) 创建)，例如在 [`useEffect`](https://reactjs.org/docs/hooks-effect.html) 或 [`useCallback`](https://reactjs.org/docs/hooks-reference.html#usecallback) 中。

下面是一个不像你想象的那样工作的代码示例:

```
const Counter = () => {
  const [counter, setCounter] = useState(0)
  const onButtonClick = useCallback(() => setCounter(counter + 1), [counter])

  const onAlertButtonClick = useCallback(
    () => {
      setTimeout(() => {
        alert('Value: ' + counter)
      }, 5000)
    },
    [counter]
  )

  return (
    <div>
      <p>You clicked {counter} times.</p>
      <button onClick={onButtonClick}>Click me</button>
      <button onClick={onAlertButtonClick}>
        Show me the value in 5 seconds
      </button>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能还记得从 [React 文档](https://reactjs.org/docs/hooks-state.html)中提取的反例，我在其中添加了一个新按钮。单击此按钮时，五秒钟后会显示一条警报，显示计数器的当前值。或者这就是你所能想象的，不幸的是显示的值不是当前值。

假设你在计数器为 5 时点击按钮，然后在你点击增量按钮三次后立即点击。您希望警报显示 8，但它却显示 5。这是因为在赋予`setTimeout`的函数中，`counter`的值是 5，没有理由更新它(React hooks 没有那么神奇的*)。这是普通的 JavaScript 闭包和作用域问题，所以显然我们需要找到另一种方法来做我们想做的事情。*

答案:*参*和钩 [`useRef`](https://reactjs.org/docs/hooks-reference.html#useref) 。这个想法是使用一个 ref 作为计数器；它会在每次`counter`的时候更新，我们会在给`setTimeout`的函数中使用它的当前值。

所以首先我们声明我们的 ref，用当前的计数器值作为初始值:

```
const counterRef = useRef(counter) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们想在每次`counter`更新的时候更新它，所以我们可以用`useEffect` :

```
useEffect(
  () => { counterRef.current = counter },
  [counter]
) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们只需要在 out 超时函数中使用`counterRef.current`:

```
const onAlertButtonClick = useCallback(() => {
  setTimeout(() => {
    alert('Value: ' + counterRef.current)
  }, 5000)
}, []) 
```

Enter fullscreen mode Exit fullscreen mode

*注:我认为没有必要给`[counter]`作为第二个参数，因为`counterRef`不应该在渲染之间改变。*

这个很好用！我们甚至可以创建一个定制的钩子来使这个过程更加简单和可重用:

```
const useRefState = initialValue => {
  const [state, setState] = useState(initialValue)
  const stateRef = useRef(state)
  useEffect(
    () => { stateRef.current = state },
    [state]
  )
  return [state, stateRef, setState]
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的组件代码被大大简化:

```
const Counter = () => {
  const [counter, counterRef, setCounter] = useRefState(0)
  const onButtonClick = useCallback(() => setCounter(counter + 1), [counter])

  const onAlertButtonClick = useCallback(() => {
    setTimeout(() => {
      alert('Value: ' + counterRef.current)
    }, 5000)
  }, [])

  return (
    <div>
      <p>You clicked {counter} times.</p>
      <button onClick={onButtonClick}>Click me</button>
      <button onClick={onAlertButtonClick}>
        Show me the value in 5 seconds
      </button>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我不完全确定这是解决在未来获得状态值*这一问题的最佳方式，尽管它似乎工作得很好。你在 state 和 hooks 上遇到过同样的问题吗？你认为还有别的方法吗，或者这个方法有什么问题吗？*

*这篇文章最初发表在我的博客上。谢尔盖·佐尔金在 Unsplash 拍摄的照片。*