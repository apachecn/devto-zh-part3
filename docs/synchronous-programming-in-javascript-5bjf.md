# 用 JavaScript 控制异步代码

> 原文：<https://dev.to/ashraful/synchronous-programming-in-javascript-5bjf>

### 同步 vs 异步？

*   **同步**是顺序执行程序的术语。简而言之，我们将称之为**同步**
*   **异步**是顺序执行程序的术语，但是当它有阻塞调用时，它不会等待，而是执行下一行。简而言之，我们将称之为**异步**

### 异步很烂吗？

**没有**不知什么原因挺好的。例如，你有数百万的网络活动，就像你有大量的用户一样。在这种情况下，使用异步方式非常合适。

### JavaScript 是如何写同步代码的？

我们已经知道 javascript 遵循异步架构。它遵循单线程并发。我们不要说那么多，而要看一个例子。

**看下面的代码**

```
function foo() {
    console.log("I am foo function")
    setTimeout(() => {
        console.log("This will delay 3 seconds")
    }, 3000)
    console.log("Function Ended")
} 
```

Enter fullscreen mode Exit fullscreen mode

**输出:**

> 我是 foo 功能
> 功能结束
> 这将延迟 3 秒

我们已经清楚地看到了异步编程的行为。你知道在第二行之前打印第三行吗？别担心。我们会谈到它。有一些方法可以实现这一点。让我们开始吧。

##### 举例(承诺实现)

```
function delayThreeSeconds() {
    // Return new promise
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log("This will delay 3 seconds")
            // simple resolve the promise
        resolve(/* We can pass data here. */)
        }, 3000)
    })
}

// Rewrite the foo funtion and call the delayThreeSeconds function
function foo() {
    console.log("I am foo function")
    // Now program will wait 3 seconds to resolve from delayThreeSeconds funtion
    delayThreeSeconds().then(() => {
        console.log("Function Ended")
    })
}

// Another way to call it
async function foo() {
    console.log("I am foo function")
    // Now program will wait 3 seconds to resolve from delayThreeSeconds funtion
    await delayThreeSeconds()
    console.log("Function Ended")
} 
```

Enter fullscreen mode Exit fullscreen mode

**输出:**

> 我是 foo 功能
> 这将延迟 3 秒
> 功能结束

**注意** async/await 仅适用于 es6。所以浏览器支持在这里很重要。如果你使用 babel，webpack 或任何其他 transpiler 将 es6 转换为 es5/浏览器支持的 javascript，那么它将为你工作。