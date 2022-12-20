# 随机计算机术语:系统管理中断(SMI)

> 原文：<https://dev.to/yordiverkroost/random-computer-terms-system-management-interrupt-smi-4c54>

计算机科学领域的发展日新月异。如此之快，实际上，你应该总是在学习新的东西。除了成为终身学习者，别无选择。因此，让我们每周从大量的计算机和编程术语中找出一个来讨论吧！本周术语是**系统管理中断(SMI)** 。

好吧，我必须这么做。我不禁想到计算机之外的系统管理中断。在公司或人员管理领域，SMI 可能是这样的:

> 管理人员:*“我们可以构建特性 X 吗？对客户 y .*
> 发展很重要:*“当然，没问题。我们已经和团队一起思考过这个问题，我们打算用 z 的方式来建造它。”*
> 管理:*“不，不，不应该是这样的。相反，你应该这样思考这个问题...*
> 
> ***系统管理中断*发生。**

😂

哦，是的，我们的开发者生活会好得多。我们终于可以做任何我们想做的事情，而不会被管理人员叫回来。

不，开个玩笑，我们的生活确实需要管理。也许吧。

反正回到实际话题。让我们通过查看 SMI 的定义来了解它实际上是什么:

> 系统管理中断(SMI)用于提供扩展功能，如传统硬件设备仿真。它们也可以用于系统管理任务。SMI 与 nmi 的相似之处在于，它们使用特殊的电信号线直接连接到 CPU，并且通常无法被屏蔽。
> ( [来源](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_MRG/1.3/html/Realtime_Reference_Guide/sect-Realtime_Reference_Guide-Hardware_interrupts-System_management_interrupts.html))

我们走吧。源码页用一个例子进一步解释了这个定义。假设你有一台不支持软盘的计算机。如今这是一个合理的假设。这对你来说可能是一件坏事，因为你有一个很棒的软盘游戏，你现在就想玩。在这种情况下，“遗留硬件设备仿真”就派上了用场。把他的设备仿真想象成软盘周围的适配器。您的计算机不知道如何与软盘对话，但它知道如何与适配器对话。然后，适配器知道如何将计算机的请求转换成对软盘的请求。在回程中，它把指令翻译成计算机能理解的东西。酷，这是一个双赢的局面。一台可以和软盘对话的快乐电脑，一个还能玩那个超赞游戏的快乐你！

SMI 的另一部分是“直接进入 CPU 的信号线”。这部分定义引入了另一个术语:系统管理模式(SMM)。把 SMM 想象成一个工具，让你操作你计算机的硬件组件。要在操作系统运行时进入这个工具，可以使用 SMI。这对于需要经常访问计算机硬件的开发人员来说很方便。例如，当计算机空闲一段时间后，系统会发送一个 SMI。这导致各种硬件组件进入睡眠状态并节省能量。

关于 SMI 和 SMM，最后要知道的是如何进出 SMM。我们已经知道如何进入 SMM:通过发送一个 SMI。因为 CPU 当时只能做一件事，所以我们需要有一个地方来存储 CPU 的当前状态。这很重要。没有这一点，我们就无法知道在完成 SMM 指令后继续进行。当进入 SMM 时，它使用计算机的部分内存(RAM)来存储 CPU 的当前状态。当 SMM 完成时，CPU 从 RAM 中检索该状态。现在计算机可以从它停止的地方继续运行。

当然，这些是 SMI 的基础以及它们与 SMM 的关系。请自行搜索更多信息。当然，在下面的评论区讨论你的发现。

*我们下次要讨论的随机计算机术语是:**带宽**。*