# 灵药和光束:并发性如何真正工作

> 原文：<https://dev.to/sophiedebenedetto/elixir-and-the-beam-how-concurrency-really-works-354n>

这篇文章最初发表在熨斗实验室的博客上。点击[这里](https://medium.com/flatiron-labs)查看熨斗学校技术团队提供的更多精彩内容。

## 不懂并发

从 Ruby 和 JS 背景来到 Elixir，我明白并发性是我想要的语言。然而，即使在编程 Elixir 几年后，我也没有真正理解它是如何并发运行的。我听说过 BEAM，并与 OTP 一起工作过，但我很难给出比这两者更好的定义。

许多“转行”程序员——我们这些没有计算机科学学位的程序员——通过动手实践来学习。我们建造东西。因此，我们有时会想当然地认为某些东西是如何工作的，并对它确实工作的事实感到满意。再加上提出“愚蠢”问题的困难甚至尴尬，你最终会和我在一起——有人编写了在光束上运行的代码，但不能告诉你光束是什么。

在过去的几周里，我对长生不老药的基础越来越好奇。Erlang 的创造者乔·阿姆斯特朗的去世启发我更多地了解他的工作，而 Bruce Tate 和 James Edward Gray 的书[design Elixir Systems with OTP](https://pragprog.com/book/jgotp/designing-elixir-systems-with-otp)的测试版让我思考 OTP 的真正含义。

如果您想停止吹嘘 Elixir 是并发的，请继续阅读，并开始理解它是如何实现这种并发的。

## 什么是光束？

[梁(波格丹一世 Erlang 抽象机)](https://en.wikipedia.org/wiki/BEAM_(Erlang_virtual_machine))是 Erlang 虚拟机。在本文中，我们将互换使用 BEAM 和 Erlang VM。

BEAM 将 Erlang 和 Elixir 代码编译成字节码。beam 文件，您可能已经在编译的 Elixir 应用程序中注意到了)并执行它。

## 光束和 OTP

为了讨论射束，我们需要讨论 OTP。

> OTP 是一组 Erlang 库，由 Erlang 运行时系统、许多主要用 Erlang 编写的现成组件和一组 Erlang 程序的设计原则组成。 [*](https://github.com/erlang/otp)

事实上，为了在您的机器上安装和使用 Erlang，您将[安装并构建一个 Erlang/OTP 发行版](http://erlang.org/doc/installation_guide/INSTALL.html#how-to-build-and-install-erlang-otp)。

OTP 提供了代表常见实践的标准实现的模块和行为，如流程监控、消息传递、生成任务等。

例如，OTP 框架对 GenServer 行为的定义是对某些类型的流程的常见使用/交互的抽象。因此，我们所有的 Erlang 和 Elixir 程序员都可以通过使用 GenServer 模块和行为来订阅一个共享的、成熟的和经过实战检验的服务器接口，而不是滚动自己的服务器并为每个新应用程序重新发明轮子。

OTP 还包含 Erlang 运行时系统(ERTS)。这意味着 OTP 框架包括了 BEAM。因此，当我们谈论 BEAM 时，我们谈论的是内置于 OTP 发行版中的虚拟机。

## 光束平行加工

作为 ERTS 的一部分，BEAM 负责调度 Erlang 进程。这就是并发奇迹发生的地方。
BEAM 每个内核使用一个操作系统线程。它在每个线程上运行一个调度程序。每个调度程序从自己的运行队列中拉出进程来运行。BEAM 还负责用要执行的 Erlang 流程填充这些运行队列。为了理解这有多酷，我们需要理解 Erlang 与并发性的关系。
并发和 Erlang 的创建

Erlang 编程语言是由乔·阿姆斯特朗和爱立信 CS 实验室的其他人于 1986 年开发的。爱立信建立并维护了拥有数十万用户的大型电话交换机。这些系统有一个重要的要求:它们不能失败。它们必须是完全容错的。当 Joe 投身于开发大型容错系统时，他发现这些系统的一个特点是并发性。

> 最初，我对并发本身并不感兴趣，我感兴趣的是如何构建容错系统。这些系统的一个特点是它们同时处理成千上万的电话呼叫。 [*](http://erlang.org/doc/installation_guide/INSTALL.html#how-to-build-and-install-erlang-otp)

认识到这一特点后，他开始将“面向并发的编程”概念化——一种对反映我们周围世界的应用程序建模的方法。

> 世界是平行的。如果我们希望编写的程序能够像现实世界中的其他对象那样运行，那么这些程序将会有一个并发的结构…人们作为独立的实体通过发送消息进行交流。这就是 Erlang 进程的工作方式……Erlang 程序是由许多小进程组成的，它们像人一样互相聊天。 [*](https://dev.toabout:blank#blocked)

考虑到这个模型，Erlang 被开发成容错和并发的。但是对于一种语言来说,“并发”到底意味着什么呢？

## 多核处理和光束的历史

在英特尔 2007 年发布酷睿 2 四核处理器之前，计算机芯片包含一个单核。核心是 CPU 的一部分，它接收指令并根据这些指令执行操作。在单核世界中，计算机有能力同时执行进程，但不是并行的。但是等等！“并发”和“并行”不是一回事吗？没有。

让我们想象我们正在做我们讨厌的事情，比如两大堆要洗的衣服。我们把我们的肤色从灯光中分离出来，然后去自助洗衣店。洗衣是最糟糕的事情，而且要花很长时间，所以我们想尽量减少在自助洗衣店的时间。我们决定同时洗两次衣服，而不是同时洗一次(放入深色衣物，加入洗涤剂，投入硬币，按下“开始”，等待洗衣过程结束，将衣物放入烘干机，在有灯的情况下重复)。

我们把深色装在一台机器里，把浅色装在另一台机器里。我们把肥皂放在一台机器里，然后放在另一台机器里，我们把硬币放在一台机器里，然后放在另一台机器里，我们按下一台机器上的“开始”键，然后放在另一台机器上。这代表了一组**并发的**过程——衣物的暗负荷和轻负荷同时处于相同的状态。但是这是由于我们在每个进程之间快速地来回切换(对暗负载和轻负载执行进程的单个步骤)而发生的。

[![](img/6ad70c4c13fa5a510303fda090361857.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qtrbNIgR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AX14b2Clrv0kgSNqcvwFUdg.png)

在这个并发的单核环境中，Erlang VM 在一个可用的内核上运行一个线程和一个调度程序。调度器从单个运行队列中挑选 Erlang 进程，并为每个进程分配一个“时间片”。如果一个进程超过了它的“时间片”，Erlang VM 就会暂停这个进程，把它放回队列，然后继续处理列表中的下一个项目。通过这种方式，Erlang VM 将通过在进程之间快速来回切换来“并发”执行 Erlang 进程，就像我们在深色衣物和浅色衣物之间快速来回切换以大致同时洗涤衣物一样。

[![](img/669a9eb9dc764fd9214d61e3d1b0766e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l_oQSjBX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A5lti3IA-KIC6i03FFtRZsw.png)

随着多核处理器的出现，BEAM 能够在 Erlang 中使用一个[对称多处理器(SMP)](http://erlang.org/euc/08/euc_smp.pdf) 。随着 2006 年 Erlang OTP R11B 的发布，Erlang VM 中的 SMP 出现了第一次迭代。这个版本的 Erlang VM 可以在一个线程上运行 1 到 1024 个调度程序，共享相同的运行队列，运行在同一个内核上。

[![](img/ef2d9e0516f1edbf675236941400a4fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JnZROwz0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AMjnRqYm6SDm1B8AV9ZET6Q.png)

随着 2007 年 OTP R12B 的发布，运行在多核处理器上的 Erlang VM 能够在每个内核上占用一个线程来运行调度程序。这允许 Erlang 进程并行运行，就像从一个共享的运行队列中抽取一组跨内核的调度程序来执行进程一样。因此，Erlang 现有的执行并发进程的能力允许它立即适应在多核计算机上运行并行进程。

重温一下我们的洗衣类比——想象我们身边有一个乐于助人的朋友，他只是喜欢洗衣服(感谢上帝，还有，谁？).当我们洗灯的时候，他们出现来处理黑暗负载。现在，我们将每套衣物同时放入两台不同的洗衣机中。我们已经实现了**并行处理**！

[![](img/34fe91ac5c7bda5920bf99ef62c3e5d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VZAIcFlj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AuXskTJltvUHAPDa0gNV9iw.png)

通过在我们的机器上启动一个 iex shell，我们可以看到这种能力的表现:

```
$ iex
Erlang/OTP 21 [erts-10.0.4] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:1] [hipe] [dtrace]
Interactive Elixir (1.7.2) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> 
```

提示的这一部分`[smp:4:4]`表明我的机器有四个可用的内核，有四个可用的调度程序，每个内核一个。

SMP 在 Erlang VM 上的第二次迭代受到这样一个事实的限制，即跨内核运行的一组并行调度程序仍然共享同一个运行队列。队列中的数据必须被锁定以避免污染。

这有可能造成处理瓶颈——当一个运行缓慢的进程或锁冲突出现时，所有等待访问运行队列的调度程序都会被延迟。例如，如果你和你古怪的爱洗衣服的朋友共用洗涤剂瓶，一个人伸手去拿洗涤剂会阻止另一个人继续下一步的洗衣过程。

[![](img/a52db42947a91bc1c4dbfc279b92972b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jylDjSYO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AEpzvhxXSe94sBtodSOQw6g.png)

OTP R13B 的发布解决了这个问题，它脱离了公共运行队列，实现了每个内核每个线程的专用运行队列。如今，Erlang VM 运行在每个内核的一个线程上。每个线程运行自己的调度程序，从自己的运行队列中提取数据。数据不会跨队列共享，因此不需要管理锁定。每个人都有自己要洗的衣服和自己要用的洗涤剂！

[![](img/c334a781976aaeafe31f74a6630efc48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cjMo_Gpx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AzLa_lGeF-ZlgRmr43nsmFA.png)

## 波束如何管理并行进程？

BEAM 为每个内核创建一个线程，为每个线程创建一个调度程序，为每个调度程序创建一个运行队列。它还负责用进程填充所有运行队列，以便调度程序并行执行。这由梁的负载平衡器管理。负载平衡器实现迁移逻辑，在独立内核上的运行队列之间分配进程。这个逻辑帮助负载平衡器从过载的队列中取走作业(“任务窃取”)，并将它们交给空的或负载不足的队列(“任务迁移”)。负载均衡器的目标是在调度器之间保持可运行进程的最大数量相等。

[![](img/b6fd223c6cc5a24ecf2123a01c6abf50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KudQWkby--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ay-ntFQVJC8qMBi1XK1S15w.png)

## 结论

深入研究 Elixir 和 Erlang 并发性的“如何”,无疑让我理解并欣赏了我一直在使用的工具。我希望这对你也一样！如需进一步阅读，请查看下面的参考资料。

## 资源

*   [https://en . Wikipedia . org/wiki/BEAM _(Erlang _ virtual _ machine)](https://en.wikipedia.org/wiki/BEAM_(Erlang_virtual_machine))
*   [https://github.com/erlang/otp](https://github.com/erlang/otp)
*   [https://learnyousomeerlang . com/what-is-OTP # its-the-open-telecom-*](https://learnyousomeerlang.com/what-is-otp#its-the-open-telecom-*)平台
*   [https://www . Erlang-solutions . com/blog/let-s-talk concurrency-with-Joe-Armstrong . html](https://www.erlang-solutions.com/blog/let-s-talkconcurrency-with-joe-armstrong.html)
*   [http://erlang.org/euc/08/euc_smp.pdf](http://erlang.org/euc/08/euc_smp.pdf)
*   [https://learnyousomeerlang . com/the-hitching liers-guide-to-concurrency](https://learnyousomeerlang.com/the-hitchhikers-guide-to-concurrency)
*   [https://blog.stenmans.org/theBeamBook/#_load_balancing](https://blog.stenmans.org/theBeamBook/#_load_balancing)
*   [http://www . Erlang-factory . com/upload/presentations/105/Kenneth lundin-Erlang factory 2009 London-AboutErlangOTPandMulti-coreperformanceinspecial . pdf](http://www.erlang-factory.com/upload/presentations/105/KennethLundin-ErlangFactory2009London-AboutErlangOTPandMulti-coreperformanceinparticular.pdf)