# 在 c#中异步/等待轻松取消

> 原文：<https://dev.to/alialp/async-await-easy-cancellation-in-c-1lka>

使用 async/await 非常有趣，但同时也有很多情况下等待部分会永远卡住，特别是当涉及到网络编程时。除此之外，在某些情况下，应用程序等待响应的时间不得超过超时。

因此，为异步操作设置超时的能力是必须的，通过使用 CancellationToken 可以实现这一点，本文将通过应用任务对象类型的扩展方法来演示使用这些 CancellationToken 的更简单的方法。

## 实现