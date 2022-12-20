# await Promise.all:不仅仅是异步函数

> 原文：<https://dev.to/clicktravelengorg/await-promiseall-not-just-for-async-functions-4bk6>

在我们的代码中，我们最终对第三方和内部 REST APIs 进行了大量异步、独立的调用，以便构建货币兑换率、机场 IATA 代码->名称映射以及获取用户航班搜索的结果集。

这导致了许多关于如何提高速度的挠头，我们需要在继续之前解决所有这些调用，但是因为它们都是独立的，我们需要等待每个承诺解决之后才能调用另一个吗？

简而言之:不，我们可以使用 Promise.all 一次调用所有独立的异步调用，并等待它们全部解决，而不用担心哪个先解决:

```
const [
 conversionRates,
 airports,
 flights,
] = await Promise.all([
 getConversionRates(),
 getAirports(),
 getFlights()
]); 
```

太棒了。我们等待一次，等待最长的过程来解决和一次性收集所有数据。

但是我们仍然有一些同步调用，它们是独立的，不可变的功能代码。我们能在那里做同样的事情吗？

答案是肯定的，通过预先考虑将函数组合在一起，并确保我们的代码在设计中完全起作用，我们能够使用 Promise.all 来等待多个函数的结果，而不管它们是否被定义为`async`。

*   编写功能独立的同步和异步 Javascript 函数
*   使用 Promise.all 同时运行它们的组
*   等待所有函数解析，而不是一次一个函数
*   ？？？
*   利润