# 使用获取 API 请求超时

> 原文：<https://dev.to/lowmess/request-timeouts-with-the-fetch-api-3bc3>

我是[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 的忠实粉丝。我经常在各种项目中使用它，包括我的网站和支持[关于页面](https://www.lowmess.com/about/)统计数据的 API。然而，它并不总是像在像 [Axios](https://github.com/axios/axios) 这样的库中那样清楚如何处理错误和请求超时。

如果你不熟悉`fetch`，它是一个原生 API，与旧的 [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHTTPRequest) 方法相比，它极大地简化了 AJAX 请求，并且[它在所有现代浏览器](https://caniuse.com/#feat=fetch)中都得到支持。然而，当它最初登陆时，没有简单的方法来处理请求超时。你可以用 [`Promise.race`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race) 或者[用另一个承诺](https://github.com/github/fetch/issues/175#issuecomment-216791333)包装你的`fetch`来伪装它，但是这些解决方案实际上并没有取消请求。这就是 [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) 的用武之地。

是一个 API，就像它的名字和我前面的句子暗示的那样，允许我们中止(取消)请求。虽然在撰写本文时[浏览器支持还不是*精彩的*，但它可以在大多数现代浏览器中使用，并且](https://caniuse.com/#feat=abortcontroller)[polyfill 可用](https://github.com/mo/abortcontroller-polyfill)。API 本身有一个非常小的表面区域:一个附加到请求对象的`signal`属性，和一个实际取消请求的`abort`方法。因为 API 非常简单，所以它非常灵活——Jake Archibald 在 Google 开发者博客上有一篇相当深入的文章，回顾了各种取消场景，以及 API 背后的历史，我强烈建议阅读一下。

有了`AbortController`，如果在给定的时间内请求没有解决，取消请求就变得很简单:如果在请求解决之前(或者在响应`Body`被使用之前)调用了`abort`方法，请求就会被取消；如果它在之后被调用，浏览器会忽略这个调用。要将所有这些放在一起，我们需要:

1.  创建一个`AbortController`的实例
2.  创建一个调用控制器的`abort`方法的`setTimeout`函数
3.  将控制器的`signal`传递给`fetch`的选项对象

## 把所有的东西放在一起

首先，因为我们基本上是围绕`fetch`写一个垫片，所以我要增加一点额外的好处。如果响应没有在`200`范围内返回(也就是说，如果`response.ok`的计算结果为`false`，我们将抛出一个错误。我们绝对不需要这样做——我们可以只捕捉我们的超时，函数将同样工作(我们实际上甚至不需要*做那件事)。然而，我总是执行这种检查，所以这为我去除了许多样板代码。*

不管怎样，这是我的通用`fetchWithTimeout`函数。它应该可以在任何支持`fetch`和`AbortController`的环境下工作。

```
const fetchWithTimeout = (uri, options = {}, time = 5000) => {
  // Lets set up our `AbortController`, and create a request options object
  // that includes the controller's `signal` to pass to `fetch`.
  const controller = new AbortController()
  const config = { ...options, signal: controller.signal }

  // Set a timeout limit for the request using `setTimeout`. If the body of this
  // timeout is reached before the request is completed, it will be cancelled.
  const timeout = setTimeout(() => {
    controller.abort()
  }, time)

  return fetch(uri, config)
    .then(response => {
      // Because _any_ response is considered a success to `fetch`,
      // we need to manually check that the response is in the 200 range.
      // This is typically how I handle that.
      if (!response.ok) {
        throw new Error(`${response.status}: ${response.statusText}`)
      }

      return response
    })
    .catch(error => {
      // When we abort our `fetch`, the controller conveniently throws a named
      // error, allowing us to handle them separately from other errors.
      if (error.name === 'AbortError') {
        throw new Error('Response timed out')
      }

      throw new Error(error.message)
    })
} 
```

使用该函数相当简单。因为我们直接返回`fetch`,所以我们可以用同样的方式使用它；唯一的变化应该是增加了第三个参数(我们的`time`参数)和我们上面讨论的额外的错误处理。

```
// This example _always_ logs the error, because I'm telling httpstat.us to wait
// at least 1s before responding, but setting the timeout threshold to 500ms.
// Also, this could definitely be written in async/await if you preferred.
fetchWithTimeout(
  'https://httpstat.us/200?sleep=1000',
  { headers: { Accept: 'application/json' } },
  500
)
  .then(response => response.json())
  .then(json => {
    console.log(`This will never log out: ${json}`)
  })
  .catch(error => {
    console.error(error.message)
  }) 
```

* * *

就是这样。整篇帖子就这么多。尽管这个代码片段非常简单(只有 20 行，没有空格和注释),但编写这个代码给我带来了三个主要好处:它迫使我将函数抽象成最可重用的版本，它给了我一个研究`AbortController`的机会，以确保我确切地知道它是如何工作的，它提供了一个地方，我可以在将来找到这个代码片段，而不是在旧项目中翻找。