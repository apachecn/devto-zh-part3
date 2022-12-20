# JVM 与本机和世界的性能比较

> 原文：<https://dev.to/benchuknii/performance-of-jvm-vs-native-vs-the-world-3mie>

最近我试图理解虚拟内存如何工作的

和 JVM 如何工作和执行的。
我的直觉是‘如果一个 JVM 是用 C 写的，它可能不如 C 的性能’
所以在大量阅读和调查之后，我发现这个故事比我最初想的要复杂得多。JVM 有许多优化，这使得它在大多数时候非常快，它确实有开销，但 JIT 中的优化使一切都值得。
基本上，JVM 将加载到本机虚拟内存中(并在可能的情况下与其他进程共享)
它将使用复杂的算法在本机堆上分配内存，以创建托管堆。
它将 JIT 执行得很好，将向量化操作，以及什么使运行时间开销不值得。
那时我在想‘好吧，那么 javascript 是一个 VM，它应该也能很好地运行’
但是[在线基准测试显示了不同的东西](https://www.techempower.com/benchmarks/#section=test&runid=8ca46892-e46c-4088-9443-05722ad6f7fb&hw=ph&test=plaintext)。我知道 javascript 是单线程的，但是这个测试考虑到了这一点(例如 fork 多进程)。
V8 引擎经过了高度优化，并且[做了各种聪明的事情](https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e)来加快执行速度，例如在后台创建类和类型以及缓存来加快执行速度。与 C 和 Java 相比，V8 还很年轻，所以我相信它会更好。我的印象是，优化 javascript 这样的动态语言比优化 java 这样的语言更难，但在这里我可能错了。

无论如何，我认为我必须做自己的测试，以更好地了解不同情况下的性能差异，所以这里是，[我的结果](https://github.com/benchuk/performance_tests)

1.  当执行循环和在内存中时，java 和 node 胜出

2.  从磁盘读取文件到内存

3.  java 在 http 服务器中胜出

希望这些信息对你有所帮助！

评论更受欢迎。

我还有一个博客，里面有其他有趣的东西:

[https://www.2bitornot2bit.com/blog](https://www.2bitornot2bit.com/blog)