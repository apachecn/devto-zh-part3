# Java 中的纤维(是的纤维！！！)

> 原文：<https://dev.to/chatter_14/fibers-in-java-yes-fibers--589j>

纤程是 Java 中即将出现的特性，它将为线程提供一个轻量级的替代品。然而，问题是为什么纤维，一个词的答案是“钱”。

Java 创建的每个线程对应一个操作系统线程。创建线程的成本很高，而上下文切换到另一个线程的成本也很高。因此，在大多数应用程序中，线程会被阻塞，服务器仍未得到充分利用。另一方面，Java 纤程将由 JVM 通过自己的调度程序来管理。

如果我们将线程类的方法与纤程进行对比，将会使图片更加清晰。

线程:t0]check access()count stack frames()get context class loader()getid()get name()get riority()get tack trace()get thread group()getunconatexceptionhandler()interrupt()is live()is live()is interruted()join()resume()run()set context class loader()set daemon()set name()set riority()setunchecxceptionhandler()start()stop()suspend()

纤程:
await()cancel()is alive()is cancelled()schedule()

创建和启动纤程非常简单

var Fiber = new Fiber(()-> system . out . println(" Hello from Fiber "))；
fiber . schedule()；

在当今的云计算世界中，应用程序是按小时计费的，降低服务器利用率是应用程序所有者最优先考虑的事情，而 fibers 肯定会帮助他们。