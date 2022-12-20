# Oracle Tuxedo 内部-阻塞` tpacall()'

> 原文：<https://dev.to/aivarsk/oracle-tuxedo-internals-blocking-tpacall-44ja>

Oracle Tuxedo 中的服务调用由`tpcall()`函数执行，该函数实际上是另外两个函数的包装器:

*   `tpacall()` -向服务发送请求
*   `tpgetrply()` -等待服务的响应

这篇文章将关注`tpacall()`函数的一些实现方面。

众所周知，Oracle Tuxedo 使用 System V IPC 消息队列进行进程间通信。但是与 POSIX 消息队列 API 不同，System V 消息队列 API 没有超时的概念:POSIX 有`mq_send()`和`mq_timedsend()`用于发送消息，而 System V 只有`msgsnd()`功能。那么 Oracle Tuxedo 是如何实现超时(`UBBCONFIG`中的`BLOCKTIME`)来向服务发送请求的呢？

为阻塞调用添加超时的标准 UNIX 方法是使用`alarm()`函数，该函数在几秒钟后向进程发送`SIGALRM`信号。信号中断系统调用的执行，并将`errno`设置为`EINTR`。这是在上个世纪软件相当简单时编写代码的方式。用信号编程从来都不容易，而且总是容易出错。除此之外，软件可能已经将`alarm()`用于其他目的，或者它可能有多个线程(信号将被发送到一个“随机”线程)，很明显`alarm()`不是合适的选择。

那么 Oracle Tuxedo 是如何为`tpacall()/msgsnd()`调用实现超时的呢？嗯，当队列中没有足够的可用空间时,*总是用`IPC_NOWAIT`标志调用`msgsnd()`,导致函数返回等于`EAGAIN`的`errno`。如果发生这种情况，Oracle Tuxedo 会休眠一秒钟(！！！)并一次又一次地重复该调用，直到它成功或者发生阻塞超时。并且它会在不休眠的情况下进行第一次重试——可能是因为队列在很短的一秒钟内就满了。*

很聪明，对吧？但是这种实现对于每秒处理几个调用(更不用说每秒几千个调用)的系统有严重的后果:

*   每秒钟有超过 1 个请求被发送到队列。
*   由于处理时间的波动，请求排队。
*   队列变满，呼叫者休眠一秒钟。
*   调用者不再自己接受请求，因此 1 秒钟的请求在调用者处排队。
*   因为没有新的请求被添加到第一个队列中，所以所有的请求都可能被处理，并且进程进入空闲状态，看起来完全是无辜的。

导致一个队列填满的一个小问题将会沿着调用者堆栈向上传播，并使您的 Oracle Tuxedo 应用程序崩溃。

我试图通过用`TPNOBLOCK`调用`tpacall`并重试失败的调用直到它们成功来避免睡眠。但是，要么旋转消耗了太多的 CPU，阻止其他进程获得 CPU 时间，要么负载平衡和序列化请求的`tpacall()`开销产生了比让 Oracle Tuxedo 休眠并重试更糟糕的结果。

第二种选择是用`TPNOTIME`标志调用`tpacall()`——可能会阻塞，直到队列有足够的空间。这在理论上听起来很糟糕，但是我越想无阻塞`msgsnd()`下的实际算法，我就越喜欢这种方法。

当然，最好的解决方案是使用大队列和节流来防止队列被填满，并且一开始就必须处理这个问题。

[下面是一个简单的代码来调查阻塞`tpacall()`](https://github.com/fuxedo/tuxedo-examples/tree/master/blocking-tpacall)