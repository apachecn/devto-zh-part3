# Async-Await vs. Then 以避免回调地狱📞😈

> 原文：<https://dev.to/pan/when-to-use-async-await-vs-then-with-promises-1gb7>

[![Dante's Inferno Callback Hell](img/64522f560bd2d2d62e403bba53c43cb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--94hdKSCm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5ofrscwnfljka6uhoe7j.jpg)

## 回调地狱🔥

当使用 JavaScript 时，有一个被称为回调地狱的概念。它描述了一组深度嵌套的回调函数，很难阅读和维护。回调地狱的发生是由于 JavaScript 的异步非阻塞特性。下面是基于但丁的插图😈九层地狱。

```
hell() {
    firstCircle() {
        secondCircle() {
            thirdCircle() {
                fourthCircle() {
                    fifthCircle() {
                        sixthCircle() {
                            seventhCircle() {
                                eighthCircle() {
                                    ninthCircle() {
                                        alert("Hell has Frozen Over!")
                                    }
                                }
                            }
                        }    
                    }
                }
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 承诺

在发出 AJAX HTTP 请求时，您通常会遇到回调问题。为了简化嵌套的回调以提高可读性和可维护性，[承诺可以使用](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。有了承诺，就有了两种拉平我们回调的技巧——(1)[`then`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then)(2)[`async-await`](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await)。

### `then`

这种模式有助于将嵌套的回调分解成连续的`thens`。这个`fetch`请求的整个执行是完全异步和非阻塞的。

```
someFunc() {
    fetch('https://someurl.com')
        .then((response) => response.json())
        .then((data) => console.log(data)

    console.log("I will not be blocked")
} 
```

Enter fullscreen mode Exit fullscreen mode

### `async-await`

这种模式做同样的事情，但是有所不同，因为每一行的`await`导致代码执行阻塞，同时等待承诺[解决](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve)。

```
async someFunc() {
    let response = await fetch('https://someurl.com'),
           data = await response.json()
    console.log("I will be blocked until I get a response", data)
} 
```

Enter fullscreen mode Exit fullscreen mode

## `async-await` vs `then`

### `async-await`

如果您的代码与`Promises`一起工作，并且需要顺序执行，那么这将非常有用。由于阻塞，您可能会失去一些并行处理代码的能力。我主要在从 Node.js 服务器发出 API 请求时使用`async-await`。

### `then`

这对于我在客户端工作时非常有用，因为请求是并行处理的，所以 UI 线程不会被阻塞。如果你用 React.js 开发你的前端，一个典型的用例可能是显示一个加载屏幕，直到一个`fetch`请求返回，然后使用一个`setState`更新 UI。

## 结论

两种`Promise`机制都可以用来解决回调地狱问题，每种机制都有自己的最佳用例。不要把自己局限于任何教条。做对你的用例有意义的事情。

> 如果你觉得这些内容有用，并想获得新内容的更新，请在 Twitter 上关注我。