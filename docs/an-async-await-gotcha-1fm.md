# 异步/等待捕获

> 原文：<https://dev.to/tyrw/an-async-await-gotcha-1fm>

## async 不是挺承诺的吗(还？)

在我们的代码库中，我们已经从承诺转移到了异步/等待，在很大程度上，这是相当顺利的。代码行更少，表达能力更强等等等等。

但是我们有一行代码检查传递的变量是否是一个承诺:

```
if (promise instanceof Promise) ... 
```

Enter fullscreen mode Exit fullscreen mode

事实证明这对于异步是失败的...有时候。

在节点 8 中，它给出假:

```
(async () => {})() instanceof Promise
// -> false 
```

Enter fullscreen mode Exit fullscreen mode

在 chrome 中，它给出 true:

```
(async () => {})() instanceof Promise
// -> true 
```

Enter fullscreen mode Exit fullscreen mode

我的假设是`true`是正确的响应，这是 node 中需要修复的一个 bug(可能已经修复了)。

同时，我们使用

```
if (promise instanceof Promise || promise.constructor.name === 'Promise') ... 
```

Enter fullscreen mode Exit fullscreen mode

我仍然

[![](img/1aefda8c5dd4550f967875f261aade0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7BLaHX4u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/01r59bisp30khppm8cxh.jpg)