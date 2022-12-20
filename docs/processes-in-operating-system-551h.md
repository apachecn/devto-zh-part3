# 操作系统中的进程

> 原文：<https://dev.to/hrishi2710/processes-in-operating-system-551h>

这里我们将讨论操作系统中进程的概念。对于操作系统的基础知识，你可以访问下面的文章:

[![hrishi2710 image](img/a76ada0dabba3bfcbbbf7637d26a8eb3.png)](/hrishi2710) [## 基本操作系统服务和结构。

### hrishikesh Kumar 1919 年 5 月 27 日 3 分钟阅读

#computerscience #explainlikeimfive](/hrishi2710/basic-operating-system-services-and-structures-275e)

那么，让我们从过程的概念开始。

进程是执行中的程序。它不仅是程序代码，还可以是程序计数器、当前活动、堆等。

### **过程概念**

**过程状态**

任何时候都可能有 5 种流程状态，如下所示:

1.  新-当流程即将执行时。
2.  运行中——当处理器正在执行进程时。
3.  等待——当流程已经执行，但还在等待某个东西，比如来自任何设备的输入。
4.  就绪-当进程在队列中，准备发送到 CPU。
5.  终止-当执行结束，相应的资源和内存被释放。

流程状态图:
[![process state diagram](img/fd1256c6378f0ea61171a2d8a7500574.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O7xDo9x4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pwntoken.files.wordpress.com/2014/11/diagram-of-process-state.jpg%3Fw%3D676)

**过程控制模块(PCB)**

过程控制块包括:

1.  流程状态:-如上所示。
2.  程序计数器:-由指向下一条要执行的语句地址的指针组成。
3.  CPU 寄存器:- PCB 由 CPU 寄存器的状态组成
4.  CPU 调度信息:-决定什么时候发送一个进程来执行，什么时候让它等待。
5.  内存管理信息:- PCB 由任何进程中涉及的第一个和最后一个寄存器的地址组成。
6.  记帐信息:-收集运行时间、进程使用的内存等数据。
7.  输入/输出状态

**线程**

在一个进程中，有几种不同的小进程(不要与子进程混淆)同时运行，它们被称为线程。例如，一个文字编辑程序可以接受键盘输入，同时运行拼写检查。这是单个进程中的两个线程。

### **进程调度**

流程调度图:
[![processing queue](img/809373d0ecccc1277d9facf223fb277d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zsylkF6q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.cs.odu.edu/%257Ecs471w/spring10/lectures/Processes_files/image028.jpg)

任何进入的程序(或作业)首先在**作业队列**中排队，然后当它准备好执行时被发送到就绪队列。**就绪队列**基本上是一个链表，其头部 L1 由第一个 PCB(过程控制块)的地址组成，尾部由最后一个要发送给 CPU 执行的 PCB 的地址组成。

一旦轮到执行，调度程序就把它分派给 CPU 执行。如果作业只是计算性的，那么它将被执行，然后进程被终止，但是如果它需要 I/O 或者它创建了子进程，该子进程的执行必须在该进程完成之前完成，那么该进程暂时等待，同时另一个进程被分配给 CPU。

一旦中断/**I/O**/子进程完成其执行，该进程再次排队到就绪队列，然后在通过 CPU 完成处理后终止。

**调度器**

有两种调度器:**作业调度器/长期调度器**和 **CPU 调度器/短期调度器**。

**作业调度器**调度如何将作业分配到待执行的就绪队列。只有当就绪队列有空间或已有进程完成执行并终止时，它才开始工作，因此它有相对较多的时间来工作，这就是它的名字，长期调度程序。

**CPU 调度程序**往往更快，因为它必须将程序从就绪队列分派到 CPU 执行。它的工作相对来说更快，因为程序执行(或部分执行)花费的时间更少，因此它的名字叫短期调度程序。

大多数进程可以分为两类，即 **I/O 绑定**和 **CPU 绑定**。受 I/O 限制的进程需要较少的计算工作和更多的外部输入，而受 CPU 限制的进程往往是纯计算性的。

调度器必须共同完成发送两个节目的混合的工作，以便有效地使用资源。假设发送了更多 I/O 绑定的进程，那么作业队列将会过载(因为没有一个现有的程序在等待 I/O 时完成执行)。另一方面，假设分派了更多 CPU 绑定的进程，那么 I/O 队列将是空的，导致资源浪费。

还有一类调度程序现在在许多操作系统中使用，即**中期调度程序**。使用这种调度程序的操作系统将进程直接发送到就绪队列。当出现中断时，中期调度程序开始起作用。在此期间，中期调度程序将正在执行的进程与中断交换，并在中断结束时成功调度它。

**上下文切换**

上下文切换主要是媒体调度器所做的。当中断发生时，进程的当前状态(或上下文)被保存(称为**状态保存**或**上下文保存**)，一旦中断结束，状态被恢复(**状态恢复**)。

### **对流程的操作**

**流程创建**

一个进程可以创建一个**子进程**，这个子进程又可以创建自己的另一个子进程。用一棵树来维持它们之间的联系。每个创建的进程都由一个称为进程 ID 的 ID 来标识( **pid** )。

流程执行的两种类型:

1.  父代和子代同时执行。
2.  父进程等待子进程完成执行。

使用两种类型的地址空间:

1.  子进程是使用相同资源的父进程的副本。
2.  子进程作为一个新程序使用自己的资源。

**过程终止**

当一个进程的执行完成或任何其他进程强制它终止时，该进程被终止。

在某些程序中，父进程的终止会导致其所有子进程的终止。这被称为**级联终止**。

一旦子进程终止，它必须等待进程通过`wait()`语句调用它。在此之前，子进程被称为**僵尸进程**。在这种情况下，如果父进程被终止而子进程没有被终止，那么子进程被称为**孤儿进程**。在 Linux 和 Unix 中，孤儿被附加到下一个进程，然后被终止。

### **进程间通信(IPC)**

有两种类型的通信过程。**不需要通信的独立流程**和需要通信的**协同流程**。

沟通有两种方式(或模式):

1.  共享内存模型
2.  消息传递模型

**共享内存模型**

为了说明这个模型，让我们来看一个生产者-消费者模型的简单问题，其中生产者生产信息供消费者消费。这里，两个进程同时运行，在共享内存中必须有一个项目缓冲区，可以由生产者填充，由消费者清空。

两种类型的缓冲液可用于上述过程。**无界缓冲区**对缓冲区的大小没有实际限制。消费者可能不得不等待新的商品出现，但生产者总能生产出新的商品。**有界缓冲区**采用固定的缓冲区大小。在这种情况下，如果缓冲区是空的，消费者必须等待，如果缓冲区是满的，生产者必须等待。

**消息传递模型**

为了在两个进程之间传递消息，应该存在一个通信链接。与此相关的问题有:

***命名***

想要通信的进程必须有一种相互引用的方式。他们可以使用直接或间接的沟通。

在**直接通信**下，每个想要通信的流程都必须明确指定消息的接收者或发送者。它可以定义为:

*   `send(P, message)` -向流程 p 发送消息
*   `receive(Q, message)` -接收来自进程 q 的消息。

由于两个进程都知道另一个进程的名称，所以在这个方案中有一种对称感。这里有一个**的不对称变体**，发送者说出接收者的名字，但是接收者不需要知道发送者的名字。它可以被解释为:

*   `send(P, message)` -向流程 P 发送消息
*   `receive(id, message)` -接收来自任何进程的消息。变量`id`被设置为已经发生通信的进程的名称。

在**间接通信**下，消息被发送到一个邮箱或端口，消息的接收者可以从那里得到它。它可以被解释为:

*   `send (A, message)` -发送消息到邮箱 a。
*   `receive (A, message)` -从邮箱 a 接收消息。

因此，很明显，对于两个进程的通信，应该有一个共享的邮箱。此外，与上一种只有两个进程可以通过直接通信进行通信不同，这里两个以上的进程可以通过从邮箱接收相同消息的不同进程进行通信。

现在，我们假设进程 P，Q，R 都共享同一个邮箱，P 在那里发送，Q，R 在那里接收。那么哪个进程会收到 P 发来的消息，如何避免冲突？。可以通过以下方法确定:-

*   最多允许一个链接与两个流程相关联。
*   一次最多允许一个进程执行一个`receive()`操作。
*   选择一个随机进程(从 Q 和 R 中选择，但不能同时选择),然后以循环方式选择剩余的进程。

***同步***

消息传递可能是**阻塞**或**非阻塞**——也称为**同步**和**异步**

*   阻止发送-发送进程被阻止，直到该进程或邮箱收到邮件。
*   非阻塞发送——发送进程发送消息并继续操作。
*   阻塞接收-接收方阻塞，直到有消息可用。
*   非阻塞接收-接收方检索有效消息或空值。

可以使用发送和接收呼叫的组合。

***缓冲*T3】**

无论通信是直接的还是间接的，进程交换的消息都驻留在一个临时队列中。这种队列可以通过三种方式实现。

1.  零容量——队列的最大长度为零，因此没有消息可以等待，因此发送方必须阻塞，直到收到消息。
2.  有限容量——队列的最大长度为 n，因此发送方只有在 n 条消息填满队列时才会阻塞。
3.  无限的容量——发送方从不阻塞。

### **客户端-服务器系统中的通信**

除了上述 IPCs 之外，客户端-服务器系统还可以使用另外三种模型:**套接字**、**远程过程调用(RPC)** 、**管道**

**插座**

套接字是通信的端点。一对进程需要一对套接字。套接字被定义为 IP 地址后跟端口号。例如，162.0.0.1:80 意味着机器的 IP 地址为 162.0.0.1，监听端口为 80。

实现指定系列(如 FTP、HTTP)的服务器侦听众所周知的端口(FTP 到端口 21，HTTP 到端口 80)。所有编号小于 1024 的端口都是**众所周知的端口**。套接字只允许交换非结构化的字节流。因此被称为**低层通信**。结构化的责任在于客户机或服务器应用程序。

**远程过程调用(RPC)**

RPC 松散地基于消息共享模型，因为包含所有信息(如监听端口地址、函数参数等)的消息被发送到具有监听端口的系统。接收消息，如果有任何输出要返回，则以同样的方式完成。

需要处理的一个问题是客户机和服务器上数据表示的差异。考虑 32 位整数的表示/一些系统(称为**大端**)首先存储最高有效字节，而其他系统(称为**小端**)首先存储最低有效字节。为了解决这些差异，许多 RPC 系统定义了独立于机器的数据表示。一种这样的表示被称为**外部数据表示(XDR)** 。在客户端或服务器端，此 XDR 数据可以转换为与机器相关的数据。

另一个问题是，客户端和服务器如何知道彼此的端口，因为它们不共享内存？对此有两种方法:

1.  该信息是预先确定的，即每次都使用相同的端口。但是，在这种情况下，编译之后，端口是不能更改的。
2.  通过发送端口信息请求，然后从另一端获取端口信息，可以动态地获取端口信息。

**管道**

管道就像一个导管，允许进程之间相互通信。
实施管道时可能会出现以下问题:

*   双向或单向，具体取决于是否允许双向消息流。
*   如果允许双向通信，则它是半双工(消息一次只允许单向传输)或全双工(两种方式都允许)。？
*   父流程和子流程之间有管理部门关系吗？
*   管道可以通过网络或在同一台机器上通信吗？

这里讨论了两种管道:

**普通管道**

在 Windows 上也称为**匿名管道**。这种管道是单向的，并且采用了必须父-子关系。这意味着这种管道用于同一台机器上的通信。一旦交流结束，这个管道就不存在了。

**命名管道**

这些是双向管道，它们可以在任何两个进程之间使用通信(不像普通管道只在父进程和子进程之间使用通信)。在一个特定的通信结束后，管道仍然存在，并且可以被之后的其他进程使用。

希望流程的基本概念在此之后变得清晰。