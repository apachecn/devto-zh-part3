# 我对反应钩子的问题！

> 原文：<https://dev.to/sajjanbalar/my-problem-with-react-hooks-4kao>

嗨，

最近，我正在用 react 编写一个新的电子应用程序。我决定用钩子来写它。从我的经验来看，我发现钩子在很大程度上是很棒的。然而，在某些情况下使用它有一些问题。在这种情况下，我发现使用类更有用。

我发现在钩子中编写代码非常痛苦的一个模式是，当我想编写一个自定义钩子，并向它传递一个回调函数，它需要在特定条件下调用这个回调函数。我发现如果这个回调使用一个状态变量或者更多。对于这个变量状态的每次更新，我都必须将我的新回调传递给钩子。

这导致了两个问题。

1.  我必须记住函数将使用的每个属性和状态变化，并编写一个 useEffect 钩子来改变回调。

2.  回调可能会在钩子内部以一种复杂的方式使用，并且一次又一次地更新它可能会很痛苦。

我不知道这是否有意义，所以我给你举个例子。

我编写了一个钩子，负责处理 eventsource，并在收到消息时调用回调。我们称之为 useEventSourceManager。我喜欢 useEventSourceManager 的想法，因为它让我能够将所有事件源管理重构到一个钩子中。
现在，useEventSoucreManager 被传递了一个回调“handleMessage ”,该回调用于为接收到的新消息呈现 ui。

我对钩子的问题基本上是下面的代码不能工作:

```
const someFunction = props => {
  const [condition, setCondition] = useState(false);
  useEventSoureManager(...someProps, ...someStateVariable, handleMessage);

  some other code that will change state of condition variable

  function handleMessage(){
    if(condition){ do something...}
    else{ do something else...}
  }
} 
```

上面的代码不起作用，因为传递给 useEventSourceManager 的 handleMessage 实例总是将状态变量“condition”设置为 false。

此外，我知道为什么会这样。而绕过的方法就是刷新回调函数。我想指出的是，同样的代码在类中编写要容易得多。