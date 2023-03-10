# JS 承诺

> 原文：<https://dev.to/vish448/js-promise-1139>

承诺是代表异步操作最终完成或失败的对象。从本质上讲，promise 是一个返回的对象，您可以将 callback 附加到它上面，而不是将 callback 传递到函数中。

承诺是这些状态之一:

*   **待定:**初始(默认)状态，既未履行也未拒绝。
*   **已完成:**操作成功完成
*   **拒绝:**操作失败

待定承诺可以完全填入值，也可以因某个原因(错误)而被拒绝。当这些选项中的任何一个发生时，由 promise 的 then 方法排队的相关处理程序被调用。

### 关于承诺的三个主要问题

### 如何创建承诺

使用 new 关键字及其构造函数创建一个 Promise 对象。
`const promise = new Promise()`

### 如何改变承诺的状态

Promise 构造函数有一个带两个参数的回调函数。当承诺的结果是 resolve 时调用 resolve 回调函数，当承诺的结果是 rejected 时调用 reject 回调函数。

```
const promise = new Promise((resolve, reject)=>{
    resolve(); // When the promise is fulfilled it change the status of the promise to resolve
    reject(); // When the promise is rejected it change the status of the promise to rejected.
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 如何在承诺状态改变时监听

我们可以听听这两种方法的变化。

。如果承诺实现了(解决)附属的功能。然后调用方法
。catchIf 承诺被拒绝(rejected)附加到的函数。catch 方法调用

如果您记录了空承诺，您可以看到附加到其原型的方法。

[![Promise console](img/4cff1ca05367923dc4decbd8736f5e79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1dI6jc3J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ypt70mb3tgsxkcbmrzzu.png)

简单的例子