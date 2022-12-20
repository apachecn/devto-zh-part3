# 像五岁小孩一样解释非阻塞 I/O

> 原文：<https://dev.to/frosnerd/explain-non-blocking-i-o-like-i-m-five-2a5f>

# 简介

十年前，网络应用程序开发领域发生了重大转变。2009 年，Ryan Dahl 发明了 Node.js，因为他对流行的 Apache HTTP 服务器处理数千个并发连接的有限可能性感到不满。Node.js 项目结合了 JavaScript 引擎、事件循环和 I/O 层。它通常被称为非阻塞 web 服务器。

非阻塞 I/O 与事件循环相结合的想法并不新鲜。早在 2002 年，Java 社区就在 J2SE 1.4 中添加了 NIO 模块。 [Netty](https://github.com/netty/netty) 是一个用于 Java 网络应用开发的非阻塞 I/O 客户机-服务器框架，自 2004 年以来得到了积极的发展。即使在此之前，操作系统就提供了一旦套接字可读或可写就得到通知的功能。

如今你经常会听到或读到类似“X 是一个非阻塞的、事件驱动的、可伸缩的，[在此插入另一个时髦词]框架”这样的评论。但是它是什么意思，为什么有用？该职位的其余部分结构如下。下一节将用一个简单的类比来说明非阻塞 I/O 的概念。之后，我们将讨论非阻塞 I/O 的优点和缺点。下一节将让我们了解非阻塞 I/O 是如何在不同的操作系统中实现的。我们将给出一些最后的想法来结束这篇文章。

# 自己的表厂

## 你的第一个员工和工作台

想象你正在开始一项生产桌子的生意。你租了一栋小楼，买了一个工作台，因为你只有一个员工，就叫他乔治吧。早上，乔治走进大楼，走到工作台前，从收件箱中取出一份新订单。

桌子的大小和颜色各不相同。各自的资源和供应品可在储藏室获得。然而，有时储藏室没有所需的材料，例如缺少一种颜色，因此 George 必须订购新的材料。因为乔治喜欢在开始另一件事之前先完成一件事，所以他只会在工作台上等着，直到新的供应品送到。

在这个类比中，工厂代表一个计算机系统，工作台代表你的 CPU，乔治是一个工作线程。订购新的供应品相当于 I/O 操作，你可以被看作是操作系统，协调所有的交互。CPU 没有多任务处理能力，每个操作不仅阻塞一个线程，而且阻塞整个 CPU，从而阻塞整个计算机。

## 多个员工，单个工作台

您想知道是否可以通过说服 George 在交付供应品的同时从事其他工作来提高生产率。新的货物可能要过几天才会到达，而乔治只会站在那里无所事事。你向他提出你的新计划，但他回答说:“我真的不擅长上下文切换，老板。但我很乐意回家，在那里什么也不做，这样我至少不会妨碍工作！”。

你意识到这不是你所希望的，但至少你可以雇用另一名员工在长凳上工作，而乔治在家等待送货。你正在雇佣吉娜，她正在组装另一张桌子，而乔治在家。有时，乔治必须等吉娜吃完一张桌子后才能继续工作，但生产率几乎翻了一番，因为乔治的等待时间得到了更好的利用。

通过让多个员工共享同一个工作台，我们引入了一种多任务的形式。有不同的多任务技术，这里我们有一个非常基本的技术:当一个线程在等待 I/O 时被阻塞，它可以被暂停，另一个线程可以使用 CPU。然而，在 I/O 繁重的应用程序中，这种方法需要我们雇用更多的员工(产生更多的线程)来等待。雇佣工人是昂贵的。有没有其他提高生产率的方法？

## 多任务、非阻塞员工

在她的第二周，吉娜也用完了供应。她意识到，在等待送货时在另一张桌子上工作实际上并没有那么糟糕，所以她请你在送货到达时给她发一条短信，这样她就可以在完成当前工作或等待下一次送货时继续在那张桌子上工作。

现在，吉娜朝九晚五地使用工作台，乔治意识到她比他更有效率。他决定换工作，但幸运的是 Gina 有一个和她一样灵活的朋友，多亏你卖掉了所有的桌子，你可以再买一个工作台。现在，每个工作台都有一名员工全天工作，利用等待供货的时间同时处理另一个订单。由于您的到货通知，他们可以专注于自己的工作，而不必定期检查交货状态。

在将工作模式更改为等待交付时不再空闲之后，您的员工正在以非阻塞的方式执行 I/O。虽然乔治在家里开始等待送货后也不再阻塞 CPU，但他仍然在等待，因此被阻塞了。Gina 和她的朋友只是在做其他事情，暂停需要交付供应品的桌子的组装，等待操作系统向他们发出 I/O 结果就绪的信号。

# 非阻塞 I/O 的好处

我希望前面的类比已经阐明了非阻塞 I/O 的基本思想。但是什么时候有用呢？一般来说，一旦您的工作负载受到 I/O 的严重限制，好处就开始显现。例如，这意味着你的 CPU 会花很多时间等待你的网络接口。

在适当的情况下使用非阻塞 I/O 将提高应用程序的吞吐量、延迟和/或响应能力。它还允许您使用单个线程，潜在地消除了线程之间的同步以及与之相关的所有问题。Node.js 是单线程的，但是可以毫无问题地用几个 GB 的 RAM 处理[百万个连接](http://blog.caustik.com/2012/08/19/node-js-w1m-concurrent-connections/)。

一个常见的误解是，非阻塞 I/O 意味着快速 I/O。仅仅因为您的 I/O 没有阻塞您的线程，它就不会执行得更快。和往常一样，没有灵丹妙药，只有权衡取舍。在 [TheTechSolo](https://thetechsolo.wordpress.com/2016/02/29/scalable-io-events-vs-multithreading-based/) 上有一篇不错的博文，讨论了围绕这个话题的不同概念的优缺点。

# 实现

非阻塞 I/O 有许多不同的形式和实现。然而，所有主要的操作系统都有可用于执行非阻塞 I/O 的内置内核函数。 [`epoll`](http://man7.org/linux/man-pages/man7/epoll.7.html) 通常用于 Linux 系统，它的灵感来自基于 BSD 的系统(如 Mac OS X)中可用的 [`kqueue`](https://www.freebsd.org/cgi/man.cgi?query=kqueue&sektion=2) ( [研究论文](https://people.freebsd.org/~jlemon/papers/kqueue.pdf))。

使用 Java 时，开发人员可以依赖 Java NIO。在大多数 JVM 实现中，如果适用的话，您可以期望 Java NIO 使用这些内核函数。然而，当涉及到细节时，有一些微妙之处。由于 Java NIO API 足够通用，可以在所有操作系统上工作，所以它不能利用像`epoll`或`kqueue`这样的个别实现所提供的一些高级特性。它类似于非常基本的轮询语义。

因此，如果您正在寻找一点额外的灵活性或性能，您可能希望直接切换到本机传输。Netty ，JVM 上最好的网络应用框架之一，支持 Java NIO 传输以及 Linux 和 Mac OS X 的[本地库](https://netty.io/wiki/native-transports.html)

当然，大多数时候你不会直接使用 Java NIO 或 Netty，而是使用一些 web 应用程序框架。一些框架将允许你在某种程度上配置你的网络层。例如，在 [Vert.x](https://vertx.io/) 中，您可以选择是否要使用本地传输(如果适用并且它提供)

*   [`EpollTransport`](https://github.com/eclipse-vertx/vert.x/blob/master/src/main/java/io/vertx/core/net/impl/transport/EpollTransport.java) 基于 Netties [`EpollEventLoopGroup`](https://netty.io/4.1/api/io/netty/channel/epoll/EpollEventLoopGroup.html) ，
*   [`KQueueTransport`](https://github.com/eclipse-vertx/vert.x/blob/master/src/main/java/io/vertx/core/net/impl/transport/KQueueTransport.java) 基于 [`KQueueEventLoopGroup`](https://netty.io/4.1/api/io/netty/channel/kqueue/KQueueEventLoopGroup.html) ，而
*   [`Transport`](https://github.com/eclipse-vertx/vert.x/blob/master/src/main/java/io/vertx/core/net/impl/transport/Transport.java) 基于 [`NioEventLoopGroup`](https://netty.io/4.1/api/io/netty/channel/nio/NioEventLoopGroup.html) 。

# 最后的想法

术语非阻塞在许多不同的方式和上下文中使用。在这篇文章中，我们关注的是非阻塞 I/O，它指的是不等待 I/O 操作完成的线程。然而，有时人们认为 API 是非阻塞的，只是因为它们不阻塞当前线程。但这并不一定意味着它们执行非阻塞 I/O。

以 JDBC 为例。根据定义，JDBC 是阻塞。然而，有一个 [JDBC 客户端](https://vertx.io/docs/vertx-jdbc-client/java/)有一个异步 API。它会在等待数据库响应的时候阻塞你的线程吗？不要！但是正如我前面提到的，JDBC 的阻挡是定义上的阻挡，那么谁在阻挡呢？这里的技巧就是用第二个线程池代替主线程来接收 JDBC 请求和阻塞。

为什么这很有帮助？它允许你继续做你的主要工作，例如回复 HTTP 请求。如果不是每个请求都需要一个 JDBC 连接，那么当线程池被阻塞时，您仍然可以用您的主线程来响应这些请求。这很好，但是仍然会阻塞 I/O，一旦您的工作受到 JDBC 通信的限制，您就会遇到瓶颈。

领域很广，还有很多细节需要探索。不过，我相信，对阻塞与非阻塞 I/O 有了基本的了解之后，当您遇到性能问题时，应该能够提出正确的问题。你在应用程序中使用过本地传输吗？你这样做是因为你可以，还是因为你在与性能问题作斗争？请在评论中告诉我！

* * *

保罗·恩格尔菲尔德的封面图片