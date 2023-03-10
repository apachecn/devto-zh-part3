# React 钩子组件在 React 中用钩子替换生命周期

> 原文：<https://dev.to/trentyang/replace-lifecycle-with-hooks-in-react-3d4n>

[![react hooks](img/b844956007fea1366cf0008f959a4a64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JgVQNUi4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://trentyang.com/assets/image/react-hooks.png)

如果您曾经使用过 React，您应该熟悉 React 生命周期的强大功能。即将到来的 React 钩子将改变我们处理生命周期的方式。

React 挂钩是现有特性的统一，包括状态和生命周期。在这篇文章中，我将向你展示如何将生命周期类方法转换成 React 钩子，从而让 React 钩子变得更加清晰。

对于三种最常见的生命周期方法(componentDidMount、componentDidUpdate、componentWillUnmount)中的每一种，我将演示一个类风格的实现和一个钩子风格的对应方法，并给出解释。

## 组件装载

```
class Example extends React.Component {
  componentDidMount() {
    console.log('I am mounted!');
  }
  render() {
    return null;
  }
} 
```

```
function Example() {
  useEffect(() => console.log('mounted'), []);
  return null;
} 
```

useEffect 是一个 React 钩子，你可以在其中应用副作用，例如，从服务器获取数据。

第一个参数是一个回调函数，它将在浏览器布局和绘制之后**被触发。因此，它不会阻止浏览器的绘制过程。**

第二个参数是一个值数组(通常是 props)。

*   如果数组中的任何值发生变化，回调将在每次渲染后触发。
*   当它不存在时，回调将总是在每次渲染后被触发。
*   当它是一个空列表时，回调将只被触发一次，类似于 componentDidMount。

## componentDidUpdate

```
componentDidMount() {
  console.log('mounted or updated');
}

componentDidUpdate() {
  console.log('mounted or updated');
} 
```

```
useEffect(() => console.log('mounted or updated')); 
```

钩子中没有直接的实现来替换 componentDidUpdate。`useEffect`函数可用于在组件每次渲染后触发回调，包括组件安装和组件更新后。

然而，这不是一个大问题，因为大多数时候我们在 componentDidMount 和 componentDidUpdate 中放置类似的函数。

只模仿 componentDidUpdate 可以在另一篇文章中讨论。

## componentWillUnmount

```
componentWillUnmount() {
  console.log('will unmount');
} 
```

```
useEffect(() => {
  return () => {
    console.log('will unmount');
  }
}, []); 
```

当您在传递给`useEffect`的回调中返回一个函数时，返回的函数将在组件从 UI 中移除之前被调用。

正如我们之前讨论的，我们需要传递一个空列表作为`useEffect`的第二个参数，这样回调将只被调用一次。这也适用于返回的函数。

通常我们会在组件 WillUnmount 中进行清理。假设您想在 componentDidMount 上创建一个事件侦听器，并在 componentDidUnmount 上清理它。通过钩子，代码将被组合成一个回调函数。

我们可以为不同的副作用创建多个钩子，并重用它们。将相关代码分组在一起并使状态管理可重用是 React hooks 的主要目的之一。

## use effect vs useLayoutEffect

现在我们可以将 componentDidMount、componentDidUpdate 和 componentWillUnmount 转换成 React 挂钩，太棒了！

没那么快，有一个问题:两种风格之间的效果并不完全相同。

> 与 componentDidMount 和 componentDidUpdate 不同，在延迟事件期间，传递给 useEffect 的函数在 layout 和 paint 之后触发。

通常这不是问题。但是如果你想在效果中操作 DOM，并且想确保它发生在浏览器绘制之前，你需要使用 [useLayoutEffect](https://reactjs.org/docs/hooks-reference.html#uselayouteffect) 。语法和`useEffect`一样。

## 总结

综上所述，我们可以用`useEffect` hook 来代替生命周期方法，但并不完全一样。当你使用钩子时，试着用钩子思考！

## 参考文献

*   [https://react js . org/docs/hooks-FAQ . html # from-classes-to-hooks](https://reactjs.org/docs/hooks-faq.html#from-classes-to-hooks)
*   [https://reactjs.org/docs/hooks-reference.html#useeffect](https://reactjs.org/docs/hooks-reference.html#useeffect)

如果你喜欢读这个，这是我的博客[https://trentyang.com/replace-react-lifecycles-with-hooks/](https://trentyang.com/replace-react-lifecycles-with-hooks/)