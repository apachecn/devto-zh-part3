# 卡尔米特的坚持-2019 年 4 月版

> 原文：<https://dev.to/viir/persistence-in-kalmit-april-2019-edition-3bgm>

本帖最初发布于[https://michaelrätzel.com/blog/persistence-in-kalmit-2019-4-版](https://michaelr%C3%A4tzel.com/blog/persistence-in-kalmit---april-2019-edition)
反映了[卡尔米特](https://github.com/Viir/Kalmit)在 2019 年 4 月的状态。由于这个项目还很年轻，所以我不会对后来的重大变化感到惊讶。

本指南假设你已经知道状态改变在 [Elm](https://elm-lang.org) 应用程序中是如何工作的。要了解这里使用的`update`函数的作用，请看一下 lucamug 制作的[榆树建筑(茶)动画](https://medium.com/@l.mugnaini/the-elm-architecture-tea-animation-3efc555e8faf)。请注意，他的示例包含“视图”和“DOM”部分，这些部分特定于客户端应用程序，在此不使用。

## 持续发展和经营

即时自动持久化是 Kalmit web 主机的主要特性之一。应用程序状态的每一个 [`update`](http://toreto.re/tea/) 都会被立即持久化。

“立即自动持久化”到底是什么意思？让我们来分解一下，定义一下这些术语。

*   为什么*坚持*？在服务器应用程序向外部方确认或报告事务后，它应该记住结果状态。Kalmit 记录应用程序状态的每一个变化，即使在宿主机器突然关闭后也能支持一致的继续。由于服务器应用程序要运行数月甚至数年，这样的中断迟早会发生。当系统再次启动时，应用程序状态会自动恢复。
*   *立即*意味着在更新的其他影响变得可观察之前。由更新功能发出的命令仅在保存了来自同一更新的状态之后才被应用。例如，当客户机收到来自服务器的 HTTP 响应时，您知道服务器的新状态已经被持久化了。
*   我使用属性 *automatic* 来阐明这种持久性的实现不需要应用程序开发人员的关注，因为 Kalmit web 主机会处理它。因为应用程序状态本身是持久的，所以没有必要在应用程序代码中考虑数据库。

要了解配置和操作，请参阅指南[如何配置和部署您的 Kalmit Web 应用程序](https://github.com/Viir/Kalmit/blob/7563bdba082345df1beda6f67587bf8b171b598c/guide/how-to-configure-and-deploy-your-kalmit-web-app.md)。

## 引擎盖下——工作原理

在阐明了运行时为支持开发和操作所提供的保证之后，本指南的其余部分说明了在幕后发生了什么来使这成为可能和高性能的。

### 意外关机后恢复

在流程运行期间，系统记录进入流程的事件序列(在 Elm 行话中有时称为“消息”)。事件被附加到包含在合成记录中的合成日志中。每个合成记录还包含前一个合成记录的 SHA256 散列。这些从一个记录到另一个记录的引用形成了一个链，实现了完全的可追溯性。读者可以使用哈希引用来确定已处理事件的原始顺序，即使记录在此期间已经被打乱。

在启动时，Kalmit web 主机使用合成日志来恢复进程状态。它读取事件序列并重放更新，以再次组成流程状态。

对于涵盖流程状态恢复的自动化测试，请参见实现中的[方法`Restore_process_state_over_compositions`。](https://github.com/Viir/Kalmit/blob/7563bdba082345df1beda6f67587bf8b171b598c/implementation/PersistentProcess/PersistentProcess.Test/TestPersistentProcess.cs#L112-L154)

### 优化以加快恢复速度

对于现实世界的应用程序来说，每次我们想要恢复一个进程状态时，依赖于重放整个历史是不实际的。因为恢复不仅用于灾难恢复，还用于实验和性能分析，所以更是如此。除了重放乐曲所花费的时间之外，不断增长的乐曲链会占用越来越多的存储空间。

为了解决恢复时间和存储空间的问题，Kalmit 还定期存储合成链的缩减。缩减的值等于从缩减的组成链导出的过程状态。执行恢复时，不再将合成链读回到历史的开头，而仅读回到最后写入的可用缩减记录。由于恢复时不再需要合成日志的早期部分，因此可以将其截断，从而释放存储空间。

实现中的自动化测试 [`Restore_process_state_from_combination_of_reduction_and_compositions`展示了在 reductions 的帮助下，从截断的合成日志中恢复流程状态。](https://github.com/Viir/Kalmit/blob/7563bdba082345df1beda6f67587bf8b171b598c/implementation/PersistentProcess/PersistentProcess.Test/TestPersistentProcess.cs#L156-L218)