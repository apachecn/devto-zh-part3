# 我又做了一次。

> 原文：<https://dev.to/kspeakman/i-did-it-again-4mjb>

今天我试着运行一些代码。它超时了，没有响应。过了一会儿，我发现我颠倒了一个布尔条件。这导致了无限循环。

[![cat rolling soccer ball infinite loop](img/bcaf7a40772026bde8555def4de2acc9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--brr1KaPO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hwg28815uveoa7avib15.gif)

这么多年过去了，我仍然在布尔值上犯同样的错误——忘记在条件前面加一个`not`(或`!`)。

你有什么东西总是让你犯错吗？

/∞