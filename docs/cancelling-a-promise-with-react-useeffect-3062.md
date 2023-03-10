# 用 React.useEffect 取消承诺

> 原文：<https://dev.to/juliang/cancelling-a-promise-with-react-useeffect-3062>

> **如果你在 2021 年登陆这里，请注意这篇文章是两年前的。从那以后，我们学会了使用 swr 和 react-query [来获取和缓存服务器数据](https://juliangaramendy.dev/blog/managing-remote-data-with-swr)。**

我见过用复杂的方法完成的，所以我必须把它写下来。

## 快速举例

```
function BananaComponent() {

  const [bananas, setBananas] = React.useState([])

  React.useEffect(() => {
    let isSubscribed = true
    fetchBananas().then( bananas => {
      if (isSubscribed) {
        setBananas(bananas)
      }
    })
    return () => isSubscribed = false
  }, []);

  return (
    <ul>
    {bananas.map(banana => <li>{banana}</li>)}
    </ul>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，`fetchBananas`函数返回一个承诺。我们可以通过在`useEffect`的范围内设置一个条件来“取消”承诺，防止应用程序在组件卸载后设置状态。

## 龙解释

假设我们有一个 REST API 端点，它给出了一个香蕉列表。我们可以通过使用返回承诺的`fetch` 通过[得到列表。我们将调用包装在一个漂亮的异步函数中，该函数自然会返回一个承诺。](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) 

```
async function fetchBananas() {

  return fetch('/api/bananas/')
    .then(res => {
      if (res.status >= 400) {
        throw new Error("Bad response from server")
      }
    })
    .then(res => {
      return res.data
    })

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们想在一个 React 函数组件中呈现一些香蕉。在传统的类组件中，我们会在`componentWillMount`或`componentDidMount`中进行异步调用，但是对于函数组件，我们需要使用 [`useEffect`](https://reactjs.org/docs/hooks-effect.html) 钩子。

> 函数组件的主体内部不允许出现突变、订阅、定时器、日志和其他副作用(称为 React 的渲染阶段)。这样做将导致 UI 中令人困惑的错误和不一致。([反应文件](https://reactjs.org/docs/hooks-reference.html#useeffect)

我们的`BananaComponent`应该是这样的:

```
function BananaComponent() {

  const [bananas, setBananas] = React.useState([])

  React.useEffect(() => {
    fetchBananas().then(setBananas)
  }, []);

  return (
    <ul>
    {bananas.map(banana => <li>{banana}</li>)}
    </ul>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`useState([])`，我们为`bananas`定义了一个初始值，这样我们就可以在承诺未决时呈现一个空列表。`useEffect`函数有两个参数:第一个是效果函数，第二个是“依赖项”或“输入”。我们的效果函数“订阅”承诺。对于我们的第二个参数，我们传递一个空数组，这样效果只运行一次。然后，当数据被检索时，承诺被解析，我们的`useEffect`调用`setBananas`，这导致我们的函数组件被重新呈现，这次数组中有一些香蕉。

## 等等！就这样吗？

**可惜没有。**我们的组件“订阅”承诺，但是它从不“取消订阅”或取消请求。如果由于任何原因，我们的组件在承诺解析之前被卸载，我们的代码将尝试在一个卸载的组件上“设置状态”(调用`setBananas`)。这会抛出一个警告:

```
Warning: Can't perform a React state update on an unmounted component.
This is a no-op, but it indicates a memory leak in your application.
To fix, cancel all subscriptions and asynchronous tasks in a useEffect cleanup function. 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过在组件卸载时取消请求来解决这个问题。在功能组件中，这是在`useEffect` 的清除功能中的[完成的。](https://reactjs.org/docs/hooks-effect.html#effects-with-cleanup) 

```
 ...

  React.useEffect(() => {
    fetchBananas().then(setBananas)
    return () => someHowCancelFetchBananas! <<<<<<
  }, []);

  ... 
```

Enter fullscreen mode Exit fullscreen mode

但是我们不能取消承诺。如果组件已经被卸载，我们可以做的是防止我们的代码设置状态。

过去有`isMounted`，但事实证明，[是一个反模式](https://reactjs.org/blog/2015/12/16/ismounted-antipattern.html)。有了类组件，我们可以实现自己的`this._isMounted`；但是在函数组件中没有实例变量。

我见过一些使用`useRef`来保持一个`mountedRef`的实现。

**但是还有一种更简单的方法。**

利用[闭包](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)，我们可以在`useEffect`中保存一个`isSubscribed`布尔值。

```
function BananaComponent() {

  const [bananas, setBananas] = React.useState([])

  React.useEffect(() => {
    let isSubscribed = true
    fetchBananas().then( bananas => {
      if (isSubscribed) {
        setBananas(bananas)
      }
    })
    return () => isSubscribed = false
  }, []);

  ... 
```

Enter fullscreen mode Exit fullscreen mode

我们从将`isSubscribed`设置为`true`开始，然后在调用`setBananas`之前添加一个条件，最后，我们在清理函数中将`isSubscribed`设置为`false`。

## 是吗？

**是**；这就是我们所需要的。

我们可以通过处理未决承诺和拒绝承诺来改进上面的代码。

```
function BananaComponent() {

  const [bananas, setBananas] = React.useState(undefined);
  const [error, setError] = React.useState('');

  React.useEffect(() => {
    let isSubscribed = true;
    fetchBananas()
      .then(bananas => (isSubscribed ? setBananas(bananas) : null))
      .catch(error => (isSubscribed ? setError(error.toString()) : null));

    return () => (isSubscribed = false);
  }, []);

  render (
    <ul>
    {!error && !bananas && <li className="loading">loading...</li>)}
    {!error && bananas && bananas.map(banana => <li>{banana}</li>)}
    {error && <li className="error">{error}</li>}
    </ul>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

## 甚至更好...

我们可以创建一个**定制钩子**，在那里我们返回一个类似`[value, error, isPending]`的元组。

> 坚持住！请注意，这篇文章是两年前的。从那以后，我们学会了使用 swr 和 react-query [来获取和缓存服务器数据](https://juliangaramendy.dev/blog/managing-remote-data-with-swr)。我们不需要下面的代码。上面提到的库处理这种情况和所有的边缘情况。

在下面的实现中，消费者不需要保持自己的状态，并且“挂起”状态是显式的。

```
export function usePromiseSubscription(promiseOrFunction, defaultValue, deps) {
  const [state, setState] = React.useState({ value: defaultValue, error: null, isPending: true })

  React.useEffect(() => {
    const promise = (typeof promiseOrFunction === 'function')
      ? promiseOrFunction()
      : promiseOrFunction

    let isSubscribed = true
    promise
      .then(value => isSubscribed ? setState({ value, error: null, isPending: false }) : null)
      .catch(error => isSubscribed ? setState({ value: defaultValue, error: error, isPending: false }) : null)

    return () => (isSubscribed = false)
  }, deps)

  const { value, error, isPending } = state
  return [value, error, isPending]
} 
```

Enter fullscreen mode Exit fullscreen mode

用法:

```
function BananaComponent() {

  const [bananas, error, pending] = usePromiseSubscription(fetchBananas, [], [])

  render (
    <ul>
    {pending && <li className="loading">loading...</li>)}
    {!pending && !error && bananas.map(banana => <li>{banana}</li>)}
    {error && <li className="error">{error}</li>}
    </ul>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我希望这是有用的。

## 提问？评论？

我很想听听你的想法。

*   你能看出这种方法有什么问题吗？
*   这比你以前做的好吗？
*   更糟吗？
*   我对`[value, error, isPending]`元组不太满意。你能为此想出一个更好的“API”吗？

* * *

本文最初发布在我的个人博客:[https://juliangaramendy.dev/use-promise-subscription/](https://juliangaramendy.dev/use-promise-subscription/)

* * *

[亚历克斯](https://unsplash.com/photos/ZR48YvUpk04?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com) 上的照片