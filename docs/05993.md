# React 的伪 setState 反模式

> 原文：<https://dev.to/josepot/react-s-bogus-setstate-anti-pattern-287e>

## TL；速度三角形定位法(dead reckoning)

这是**坏** :

```
function useCounter() {
  const [count, setCount] = useState(0)

  const increment = useCallback(() => setCount(count + 1), [count])
  const decrement = useCallback(() => setCount(count - 1), [count])

  return { count, increment, decrement }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是**好** :

```
function useCounter() {
  const [count, setCount] = useState(0)

  const increment = useCallback(() => setCount(x => x + 1), [])
  const decrement = useCallback(() => setCount(x => x - 1), [])

  return { count, increment, decrement }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 经验法则

转换状态时，使用函数重载。否则，您可能无法使用最新的状态。

替换状态时，使用值重载。

## 第一次实现怎么了？

基本上，如果`increment`或`decrement`在同一个事件处理程序中被调用多次，事情就不会正常工作。

为了说明这个问题，让我们检查一下`useCounter`是如何组合的:

```
function useNCounter(nTimes) {
  const {count, increment: inc, decrement: dec} = useCounter();

  const increment = useCallback(() => {
    for (let i = 0; i < nTimes; i++) {
      inc();
    }
  }, [nTimes])

  const decrement = useCallback(() => {
    for (let i = 0; i < nTimes; i++) {
      dec();
    }
  }, [nTimes])

  return { count, increment, decrement };
} 
```

Enter fullscreen mode Exit fullscreen mode

`useNCouter`是一个钩子，它通过接受一个表示计数器应该增加/减少的次数的参数来增强`useCounter`。

在[这个代码盒子](https://codesandbox.io/s/v0z14739p3)——它使用了`useCounter`的第一个实现——中，我们可以看到`useNCounter`是如何不正确工作的。另一方面，在[中，另一个代码沙箱](https://codesandbox.io/s/oynvv7oxz)——它使用了第二个实现——`useNCounter`——工作得很好。

## 为什么那两个实现不等价？

React 批处理发生在其事件处理程序中的更新，以避免对 render 函数进行无意义的评估。

在初始实现中，递增/递减函数总是设置相同的值。直到那个值被更新，一个新的回调函数才被创建。直到下一次更新才会发生。这就是为什么`setState`应该被当作异步函数。

## 这个问题是针对钩子的吗？

没有。传统的`this.setState`也是异步的。所以，只要记住这个简单的规则:如果你的下一个状态依赖于前一个状态，使用函数重载。

## 这个有 ESLint 规则吗？

据我所知没有。

## 这实际上是一个问题吗？

根据我的经验，这种反模式是许多[意外错误](https://twitter.com/Joseptec/status/1110287144105459712)的原因。我使用的代码来自 react-hooks-testing-library 的[主示例，我在许多其他地方也看到过，比如在](https://github.com/mpeyper/react-hooks-testing-library/blob/3dc4643f9a701e7d148a2fe38dbfd37e6fca758d/README.md#example) [Rangle 的博客文章](https://medium.com/@rangleio/refactor-to-react-hooks-not-classes-d06dea062b2)，或者在 [Dan Abramov 的文章](https://overreacted.io/making-setinterval-declarative-with-react-hooks/#just-show-me-the-code)。

## 没门！丹·阿布拉莫夫不会犯这样的错误！你一定是错了！

好吧，你是对的。丹·阿布拉莫夫知道他在做什么。这段代码运行得很好。

然而，为了证明我的观点，一个稍微好一点的实现应该是:

```
function Counter() {
  const [count, setCount] = useState(0);
  const incCount = useCallback(() => setCount(x => x + 1), []);

  useInterval(incCount, 1000);

  return <h1>{count}</h1>; } 
```

Enter fullscreen mode Exit fullscreen mode

我想说的是，如果你的下一个状态依赖于前一个状态，那么使用函数重载总是一个好主意。

最后，我不想从帖子上不公平地批评丹的代码。我认为他没有使用函数重载的主要原因是因为他不想一下子用太多的概念来混淆读者。这是一篇很棒的文章，如果你还没有看过的话，你应该读一读。