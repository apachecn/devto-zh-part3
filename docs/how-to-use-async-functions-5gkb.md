# 如何使用异步函数

> 原文：<https://dev.to/kahlil/how-to-use-async-functions-5gkb>

Axel Rauschmayer 博士的这篇文章正是我所需要的，让我思考如何使用异步函数而不产生混淆。

因为到目前为止我只是凭直觉使用它们，而且由于它们的同步风格，我对何时使用`try-catch`感到困惑。我还试图调用一个前面没有`await`的异步函数，同时在函数体中使用`await`，完全期望它会被同步执行。

重要的是要记住异步函数的基础是承诺。

对我来说，阿克塞尔文章中最有趣的部分是:

*   [异步功能同步启动，异步结算](http://2ality.com/2016/10/async-function-tips.html#async-functions-are-started-synchronously-settled-asynchronously)
*   [并行度](http://2ality.com/2016/10/async-function-tips.html#parallelism)
*   [立即调用异步函数表达式](http://2ality.com/2016/10/async-function-tips.html#immediately-invoked-async-function-expressions)