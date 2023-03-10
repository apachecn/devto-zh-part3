# Java 和 Docker - JVM 运行时基础

> 原文：<https://dev.to/zac_siegel/java-and-docker---jvm-runtime-basics-2l20>

本文起源于[zsiegel.com](https://zsiegel.com/2018/11/25/java-and-docker-runtime-basics)

随着容器部署变得越来越普遍，理解您的应用程序和运行时如何与各种 Linux 容器技术协作是很重要的。

根据您的 JVM 目标版本，您可能需要做一些额外的工作来确保您的 Java 运行时知道处理器和内存的使用限制。如果您不小心，您的应用程序可能会应用不正确的优化或试探法，从而可能导致性能问题甚至崩溃。

## 名称空间和 Cgroups

首先，如果你需要温习使 Docker 成为可能的底层 Linux 技术，你可能想要阅读 [Julia Evans](https://mobile.twitter.com/b0rk) 优秀的博客文章[这里](https://jvns.ca/blog/2016/10/10/what-even-is-a-container/)。

有了对 [cgroups](https://en.wikipedia.org/wiki/Cgroups) 和[名称空间](https://en.wikipedia.org/wiki/Linux_namespaces)的基本理解，我们现在可以将注意力转向确保我们的 Java 运行时知道这些技术。为了确保 Java 运行时知道容器上设置的内存和处理器限制，您需要知道以下内容。

## Java 7

遗憾的是，JDK 7 中没有提供容器感知的标志或功能。如果您在 JDK 7 上运行，您可以调整应用程序线程数，以减少潜在的处理器资源问题。

## Java 8

Java 8 有几个标志可以帮助运行时以一种更能感知容器的方式运行。这些更新已添加到自 2017 年 4 月 18 日起提供的 Java 8 Update 131 版本中。

该更新包含改进的处理器和内存容器意识，并允许使用实验性 VM 标志来实现自动堆调整。要解锁此功能，只需在您的`java`命令行选项中添加以下标志。

```
java -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap -jar app.jar 
```

这些实验标志根据容器的`sys/fs/cgroup/memory/memory.limit_in_bytes`文件中指定的值来设置堆的大小。你可以在这里查看 bug 报告和与此[相关的工作。](https://bugs.openjdk.java.net/browse/JDK-8170888)

## Java 9 & 10

Oracle 已经表示，JDK 9 和 JDK 10 将自动识别容器，同时考虑处理器和内存限制。他们还提供手动覆盖，允许手动禁用或设置这些限制。有许多票，这些功能的工作可以在下面的 [OpenJDK Bug tracker](https://bugs.openjdk.java.net/projects/JDK/issues/JDK-8192936?filter=allopenissues%20%22OpenJDK%20Bug%20Tracker) 上跟踪。

*   [JDK 10 版本说明:码头集装箱的改进](https://bugs.openjdk.java.net/browse/JDK-8196595)
*   [Docker 内存限制](https://bugs.openjdk.java.net/browse/JDK-8170888)
*   [Docker 处理器限制](https://bugs.openjdk.java.net/browse/JDK-8140793)

## 结论

为了让 JVM 完全了解容器化，还有很多工作要做，但是到目前为止，已经取得了很好的进展，更重要的是，为了从社区获得更多的反馈，已经返回到 JDK 8。

如果您发现自己在容器中运行 java 应用程序，并且需要帮助，请经常回来查看更多更新，因为我开始了一个关于运行 Java 应用程序的新系列文章，作者是 [Docker](https://www.docker.com/) 和 [Kubernetes](https://kubernetes.io/) 。