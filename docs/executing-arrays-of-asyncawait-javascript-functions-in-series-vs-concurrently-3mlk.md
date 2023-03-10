# 串行和并行执行 async/await JavaScript 函数数组

> 原文：<https://dev.to/ccleary00/executing-arrays-of-asyncawait-javascript-functions-in-series-vs-concurrently-3mlk>

***原载于 [coreycleary.me](https://www.coreycleary.me/executing-arrays-of-async-await-javascript-functions-in-series-vs-concurrently/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我也定期发送备忘单和其他赠品。*

当处理一组`async/await`函数(返回承诺)时，弄清楚如何在**系列**中全部执行它们(一次一个)以及如何在**中同时调用它们**(不是一次一个，在重叠的时间段内执行)可能会很棘手。

也许你一直试图按顺序执行它们，*，但它们最终却没有按顺序执行*。或者也许你一直试图同时执行它们，*，但是它们最终一次执行一个，连续执行*。

在这篇文章中，我们将解释这两种方法。

这样，如果你有一个需要确保每个函数都按顺序解析的需求，你将能够而不会绞尽脑汁地试图找出为什么函数会乱序解析...

如果您需要它们是并发的，您将能够这样做，并使它们比您串行处理时更快地得到解决。

### 系列

运行一系列`async/await`函数的最简单方法是使用`for...of`。这将按顺序执行它们，一次一个，并等待每个问题解决。

```
const asyncA = async () => {
  return 'a'
}

const asyncB = async () => {
  return 'b'
}

const asyncC = async () => {
  return 'C'
}

const list = [asyncA, asyncB, asyncC]

for (const fn of list) {
  await fn() // call function to get returned Promise
} 
```

### 同时地

对于并发执行，我推荐使用`Promise.all()`。记住，`async/await`函数是关于承诺的语法糖，所以你可以对它们使用`Promise.all()`。

```
const asyncA = async () => {
  return 'a'
}

const asyncB = async () => {
  return 'b'
}

const asyncC = async () => {
  return 'C'
}

const list = [asyncA, asyncB, asyncC]

await Promise.all(list.map(fn => fn()))  // call each function to get returned Promise 
```

当然，如果你想要这些异步函数的返回值，你可以:

```
const responses = await Promise.all(list.map(fn => fn()))

// destructured example
const [a, b, c] = await Promise.all(list.map(fn => fn())) 
```

*快速提示:这篇文章只是涵盖了串行和并发的“快乐之路”(即没有错误/承诺拒绝)。我已经计划了另一个帖子来处理更健壮的错误处理。现在请注意，对于 Promise.all()，它将拒绝第一个拒绝的承诺。*

### 执行

如果你对这两个函数的定义感到困惑，从执行的角度来看，这就是 series vs concurrent 的样子:
[![](img/608e17ac9599e165113d7ee9df41b79c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m0SI2F_h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.coreycleary.me/wp-content/uploads/2019/02/series-vs-concurrent.png) 
从技术上来说，并发执行的函数不会在完全相同的时间*所有的*启动，但实际上，这就是它的样子。

如果你想在代码中看到这些:

```
const wait = time => {
  return new Promise(resolve => setTimeout(resolve, time))
}

const someFn = async (item) => {
  await wait(2000)
  console.log(item)
}

// in series
for (const item of ['first', 'second', 'third']) {
  await someFn(item)
}
// 0s
// 2s - 'first'
// 4s - 'second'
// 6s - 'third'

// concurrently
await Promise.all(['first', 'second', 'third'].map(itm => someFn(itm)))
// 0s
// 2s (roughly) - 'first', 'second', 'third' 
```

### 包扎

下次你需要记住如何执行这两种类型时，参考这篇文章。如果你以前没有尝试过使用`Promise.all()`,下次当你不需要所有的异步函数都按顺序执行时，尝试一下。这是一个很好的速度提升，根据不同的场景，对你的最终用户来说也是一个很好的提升。

感觉你还没有完全掌握`async/await`和承诺？我每一两周发布一次关于 JavaScript 和 Node.js 的新帖子，包括处理异步场景。[这又是那个链接](https://www.coreycleary.me/about/)订阅我的时事通讯！