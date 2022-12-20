# 使用 React.memo、useCallback 和 useReducer 进行 React 优化。

> 原文：<https://dev.to/changoman/react-optimizations-with-reactmemo-usecallback-and-usereducer-42ni>

*本帖最初发表于[codebushi.com](https://codebushi.com/react-component-optimization/)T3】*

随着最近 React 钩子的发布，我已经在我的 React 代码中使用了越来越多的功能组件。在阅读 React 文档时，我不断看到提到`useReducer`比`useState`更具“性能”。我不清楚为什么，所以我深入研究了这个话题。经过大量的研究和实验，这些是我的发现。

没有视频很难展示这些概念，但希望这些内容有意义。我推荐使用 React Dev 工具并在设置中打开`Highlight Updates`来测试。将 console.log 放在子组件上也很有帮助，如果您看到控制台日志，那么它将被重新呈现。

===

**我们的场景:一个父组件和一个从父组件接收道具的子组件。假设两者都是功能组件，并且父组件使用`useState`来管理状态。**

如果传递给子组件的道具很简单，那么子组件应该用`React.memo`包装起来，以防止在道具不变的情况下重新渲染。`React.memo`将自动优化功能组件，其行为类似于`shouldComponentUpdate`生命周期方法。阅读更多关于 [React.memo](https://reactjs.org/docs/react-api.html#reactmemo) 的信息。

如果父组件正在向下传递一个函数(特别是更新父组件状态的函数)给子组件，只使用`React.memo`将不起作用。父组件中的函数需要用`useCallback`钩子包装。这是因为每当父对象重新渲染时，该函数将被“重新渲染”，因此子对象将始终将该函数视为新道具。阅读更多关于[使用回调](https://reactjs.org/docs/hooks-reference.html#usecallback)的信息。

如果在父组件中使用`useReducer`钩子来管理状态，那么我们就不必担心`useCallback`。`useReducer`将返回一个可以传递给子组件的`dispatch`方法。`dispatch`方法不会在每次父方法重新呈现时都被重新呈现。我很确定这就是为什么`useReducer`比`useState`更“高性能”。阅读更多关于 [useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer) 的信息。

当处理深度嵌套的子组件时，建议将`useReducer`挂钩与 React 上下文结合使用。您可以使用 Context 沿着树向下传递`dispatch`方法，这可以防止 prop drilling。阅读更多关于[这种模式](https://reactjs.org/docs/hooks-faq.html#how-to-avoid-passing-callbacks-down)。