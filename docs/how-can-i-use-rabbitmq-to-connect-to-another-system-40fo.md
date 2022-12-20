# RabbitMQ 是解决请求超时问题的合适方案吗？

> 原文：<https://dev.to/haamida/how-can-i-use-rabbitmq-to-connect-to-another-system-40fo>

我们正在处理的系统依赖于从外部 API 收到的响应，现在经常是，我们发送的请求超时结束，我们最终会丢失正在进行的事务。

我们需要不断地重新发送我们的请求，直到 API 响应，这样我们才能继续事务。

有人建议我使用 RabbitMQ，让它对请求进行排队，直到 API 做出响应，这样我的应用程序应该是一个推送队列的生产者，我仍然无法理解的是，一旦我的消息(请求成功执行)发出，我将如何从队列中接收回调？RabbitMQ 能保证我的请求成功到达目的地吗？它是否会一直尝试发送请求，直到 api 做出响应(api 可用于交换)？
我的应用程序将如何恢复与请求相关的执行？我最初的想法是隔离那些依赖外部 API 即时响应的代码。

甚至 RabbitMQ 是这里最好的选择吗？

我对 RabbitMQ 毫无经验，这就是为什么我仍然怀疑它在这个用例中是否合适，所以如果这些问题完全是愚蠢的，我提前表示歉意。

PS:我们正在跟踪超时数据。我们正在连接的 API 是外部系统的一部分，我们无法控制它。