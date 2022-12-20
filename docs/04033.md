# componentWillMount() vs componentDidMount()

> 原文：<https://dev.to/torianne02/componentwillmount-vs-componentdidmount-5f0n>

在 bootcamp 中构建 React 和 Redux 项目时，我很难理解`componentWillMount()`和`componentDidMount()`方法之间的根本区别。当一个人工作时，我总是感到沮丧，但我不明白如何工作或为什么工作。

## `componentWillMount()`

直到参加在线学习小组会议，我才意识到一个大问题是`componentWillMount()`在 2018 年被弃用。虽然您可以继续使用它(直到 React 17.0)，但这不是最佳实践，因为它对于异步渲染不安全。如果您确实选择继续使用它，您应该使用`UNSAFE_componentWillMount()`。

#### 原因

在`componentWillMount()`中使用 fetch 调用会导致组件首先使用空数据进行渲染，因为在组件第一次渲染之前`componentWillMount()`将**而不是**返回。

由于 JavaScript 事件是异步的，所以当您进行 API 调用时，浏览器会在调用进行的同时继续做其他工作。使用 React，当组件渲染时，它不会等待`componentWillMount()`完成，所以组件继续渲染。

综上所述，您需要创建一个组件，即使没有您希望显示的数据，它看起来也还不错。在数据出现之前，没有办法(甚至没有计时器)阻止组件进行渲染。

当我构建我的项目时，我不知道`componentWillMount()`被弃用，也不明白为什么我总是得到“无法读取未定义的属性‘map’”的错误。我很困惑，因为我有一个数组，它应该被数据填充，但它没有。

事实证明这完全有意义，因为组件最初是用空数据呈现的，所以数组是空的，不能被迭代。只有在这一点上，我才知道使用`componentDidMount()`是最佳实践。

## `componentDidMount()`

调用获取数据的最佳位置是在`componentDidMount()`内。`componentDidMount()`只在客户端被调用一次，相比之下`componentWillMount()`被调用两次，一次在服务器端，一次在客户端。当客户端从服务器接收数据时，在初始呈现之后，在数据显示在浏览器中之前调用它。由于数据直到初始渲染后才会被加载，开发者**需要**正确设置组件的初始状态。

另一方面，使用`componentDidMount()`你可以使用一个计时器，让数据经常更新，而不需要用户刷新页面。我认为这是一个非常好的特性，在项目/网站中非常有用。

## 期末笔记

当我知道我应该使用`componentDidMount()`时，我的项目真正开始了。其他一切都正常工作，这是我要修复的最后一个错误。令人惊讶的是，这是一个如此简单的问题，只是一开始对我来说似乎很复杂。我写得越多，就越清楚。我希望当你讲到这里的时候，你会觉得你对这个话题也有了更好的理解。如果您需要任何额外的信息，请随时查看我的资源，它们是很棒的文章！

#### 来源

[componentidmount()v/s componentidmount()—React](https://medium.com/coffee-and-codes/componentdidmount-v-s-componnetwillmount-react-47f4f631276c)
[取数据位置:componentidmount vs componentidmount](https://daveceddia.com/where-fetch-data-componentwillmount-vs-componentdidmount/)
[componentidmount 在 react 16.3 中已弃用](https://github.com/styled-components/styled-components/issues/1575)
[React 16.3 新增功能(. 0-alpha)](https://medium.com/@baphemot/whats-new-in-react-16-3-d2c9b7b6193b)