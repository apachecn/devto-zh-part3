# Oracle Tuxedo 内部-阻塞` tpgetrply()'

> 原文：<https://dev.to/aivarsk/oracle-tuxedo-internals-blocking-tpgetrply-2d9g>

本帖是[封杀 tpacall()](http://aivarsk.github.io/2019/03/22/blocking-tpacall/) 的后续

`msgrcv()`函数用于在 Oracle Tuxedo 中实现`tpgetrply()`。它与我在上一篇文章中描述的实现超时有相同的问题。但是 Oracle Tuxedo 对`tpgetrply()/msgrcv()`的超时有不同的策略:它总是执行一个*阻塞* `msgrcv()`。那么接下来会发生什么？

这是 Tuxedo 的`BBL`进程的职责之一:超时后`BBL`进程将一条消息放入回复队列，然后`msgrcv()`调用结束。虽然我没有直接证据，但我认为每个 Tuxedo 客户端在执行`msgrcv()`调用之前都会将信息写入共享内存。Tuxedo 中有两种类型的超时:被配置为`SCANUNIT`乘以`BLOCKTIME`参数的阻塞超时，以及在配置中或以编程方式指定的事务超时。它们都以同样的方式工作:

*   将信息写入`BBL`进程的共享内存
*   执行阻塞`msgrcv()`调用
*   每隔`SCANUNIT`秒`BBL`停止接受对`.TMIB`服务的服务请求，并执行健全性检查
*   如果`SCANUNIT` x `BLOCKTIME`或事务超时已经过期，则将消息放入队列
*   `msgrcv()`呼叫返回

但是，没有办法中断耗时太长的服务调用，它仍然会执行工作，并且响应会被放入调用流程的回复队列中。这意味着调用者必须注意这样的延迟回复，以使回复队列不会填满。事实上，它在每第 n 个`tpacall()`或关闭之前都会这样做(如下例所示):对于超时的`mtype`请求，会执行`msgrcv(..., IPC_NOWAIT)`。它还引入了一个竞争条件:真正的回复可能就在超时回复被添加到队列之前到来。所以内务处理过程必须兼顾这两种情况。

有两种方法可以改变`tpgetrply()`的行为:

*   TPNOBLOCK 标志使其在回复不可用时立即返回。
*   TPNOTIME 标志使调用不受`SCANUNIT` x `BLOCKTIME`超时的影响，但事务超时仍可能发生。

我必须提到的另一个问题是:如果不使用事务超时，可能会出现调用者收到阻塞超时，但请求仍然在被调用者的请求队列中的情况。没有人取消请求，被调用方将处理它，尽管没有人再等待响应。我认为甲骨文 TSAM 公司有一个允许放弃此类请求的功能，但这是一个额外价格的附加产品。因此，如果您的服务需要几秒钟才能完成，那么在消息中添加“请求过期时间”字段并在程序代码中删除过期的请求可能是个好主意。

[下面是一个简单的代码来调查阻塞`tpgetrply()`](https://github.com/fuxedo/tuxedo-examples/tree/master/blocking-tpgetrply)