# 你的 CPU 如何一点一点地蚕食

> 原文：<https://dev.to/scottslatton/how-your-cpu-can-nibble-on-a-bit-4mn4>

# 解剖 CPU

CPU(中央处理器)是你整个电脑的大脑。它通过计算嘎吱作响，并吐出我们可以使用的新信息。我知道这听起来很抽象，是一种“咄”的说法，所以我想继续深入探讨一下。让我们从硬件开始，逐步进入。当你用 Ruby、Javascript 或 C#这种高级语言输入源代码时，人类可读的代码会被分解，然后由 CPU 执行。让我们看看当你点击“运行”按钮或当你的浏览器遇到一些 Javascript 时会发生什么。

"最简单的计算机只是一个电灯开关."-我在电脑维修店的第一个老板。

令人惊讶的是，这句话看起来是如此的简单，的确如此，但它也是完全正确的。CPU 只计算 1 和 0，开或关。但是我们需要我们的计算机每秒处理几十万或几百万次计算。这是如何实现的？CPU 基本上是一个巨大的晶体管网络，开启或关闭。他们拨动这些开关的速度被称为“时钟速度”，用赫兹来衡量。

CPU 过去是用真空管制造的，但这些真空管体积大，价格昂贵，而且容易出故障。真空管也消耗相当多的能量，在 20 世纪 60 年代中期，当晶体管出现时，它们很容易被取代。

[![](img/908a9f819b9ba60a87afca19f01ac0fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xSQXQMc7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.csetutor.com/wp-content/uploads/2018/01/cpu.png)

仅仅从硬件的角度来说，你只需要 CPU、电源、内存和主板来运行你的计算机。RAM(读访问存储器)是易失性的，这意味着每当电流停止通过它时，它就会被擦除。有几种不同类型的 RAM，当提到这个词时，你可能会想到的是连接到主板上的记忆棒。然而，这只是一种类型，它们的主要用途是体积大、速度快，并且物理位置靠近 CPU。尽管 CPU 芯片上有更快更近的 RAM 缓存。这些缓存只是存储 CPU 接下来最有可能需要的信息，比如你刚刚声明和赋值的变量。如果你想在断电后保存这些数据，这就是硬盘发挥作用的地方。

总结一下:一旦你的计算机遇到一些代码，它就以指令流的形式进入 CPU，然后 CPU 利用它的 RAM 存储位以备后用。

[![Intel 4790k and a 5820k 6 core processor](img/4f8f8740ff6ca7ed068109dd2a9bf201.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jI0QHeGv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kitguru.net/wp-content/uploads/2014/10/CPU-1.jpg)

CPU 的大小可能会有所不同，因为 CPU 的要求可能不同。在上图中，右边的 CPU 支持更大的内存容量，并在芯片上存储更多的物理内核。

# 多线程 vs 单线程

在我们进入单线程和多线程之前，让我们先讨论一下线程对于 cpu 功能的意义。

[![](img/3ce2164c0b8ae4e038ec70fe70ea6b6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BGdkp197--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/a/a5/Multithreaded_process.svg/1920px-Multithreaded_process.svg.png)

在计算机科学中，执行线程是可由调度程序独立管理的最小程序指令序列，调度程序通常是操作系统的一部分——[维基百科](%E2%80%9Dhttps://en.wikipedia.org/wiki/Thread_(computing)%E2%80%9D)

因此，单个线程可以处理通过程序发送的数据流，同时预先管理实际计算的顺序，并在计算完成后重新组合。在现实世界中，这意味着 Javascript(一种解释型语言)被它的 JIT(实时)编译器自上而下地分解并送入 CPU。这就是在 Javascript 中对代码排序如此重要的原因，也是使用提升的原因。

现在知道了什么是单线程，你可能就明白什么是多线程了。在这种情况下，多个 CPU(也称为“核心”)相邻放置在同一个芯片上，可以同时处理来自多个来源的计算。

# 并发&并行

从这里开始，我想谈谈上述术语，因为一旦我们进入多线程，我们就在谈论同时完成多项任务。

你可以把排比想象成餐馆里的厨师整天做一道又一道菜。也许他们不得不在鸡肉烹饪时切芹菜，但他们会在鸡肉烧焦前回来把鸡肉从烤箱里拿出来。

一种流行的并行形式来自英特尔。他们的超线程技术将一个物理 CPU 内核分成两个逻辑内核，以优化任务之间的等待时间。

另一方面，并发就像有多个厨师在厨房里烹饪完全不同的饭菜，彼此完全独立。

点击此处阅读关于这个主题的更深入的文章。