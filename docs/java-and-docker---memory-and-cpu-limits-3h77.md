# Java 和 Docker -内存和 CPU 限制

> 原文：<https://dev.to/zac_siegel/java-and-docker---memory-and-cpu-limits-3h77>

本文起源于[zsiegel.com](https://zsiegel.com/2018/11/26/java-and-docker-memory-and-cpu-limits)

多年来，java 开发人员已经习惯于调整他们的代码和 JVM 标志来获得性能和稳定性。

现在我们更经常地部署到容器中，理解底层容器技术如何影响我们的应用程序也很重要。

让我们看一个真实世界的例子，来理解当我们调整控制内存和 CPU 分配的容器标志时，我们的应用程序是如何反应的。

这个例子适用于 Docker、Kubernetes、Mesos 和其他编排好的容器环境。

以下所有命令都可以在 Docker CE for Linux 和 Mac v18 及更高版本上运行。我们在这些例子中使用 JDK8 来演示最低容器感知的 JVM。

## 简单的可执行文件

让我们从一个简单的 Java 可执行文件开始，它将检测到的处理器和运行时可用的最大内存记录到控制台。

```
public class Docker {

  public static void main(String[] args) {

    Runtime runtime = Runtime.getRuntime();

    int processors = runtime.availableProcessors();
    long maxMemory = runtime.maxMemory();

    System.out.format("Number of processors: %d\n", processors);
    System.out.format("Max memory: %d bytes\n", maxMemory);
  }
} 
```

然后，我们创建一个简单的 Dockerfile 文件，其中包含具有这个主函数的 JAR。

```
FROM openjdk:8-jre
COPY /build/libs/java-and-docker-1.0.jar java-and-docker.jar

CMD ["java", "-XX:+UnlockExperimentalVMOptions", "-XX:+UseCGroupMemoryLimitForHeap", "-jar", "java-and-docker.jar"] 
```

在本例中，我们将运行 JDK 8，它是容器感知能力最低的 JDK。有关 JDK 版本之间容器感知能力差异的更多信息，您可以查看之前的文章[这里](https://zsiegel.com/2018/11/25/java-and-docker-runtime-basics)。

现在我们需要构建容器。

```
$ docker build -t zsiegel:java-and-docker . 
```

构建好容器后，让我们在当前机器上运行它，看看会得到什么。

```
$ docker run zsiegel:java-and-docker
Number of processors: 4
Max memory: 466092032 bytes 
```

您可以将这个结果与 Mac 上 Docker 应用程序首选项中的 CPU 和内存设置进行比较，或者看看它与 Linux 上的机器规格相比如何。内核数量应该匹配，显示的 ram 应该略低。

## 内存限制

让我们看看如何限制容器可以使用的内存，以及运行时是如何调整的。

```
$ docker run -it --memory=512m zsiegel:java-and-docker
Number of processors: 4
Max memory: 119537664 bytes 
```

既然我们已经调整了容器本身可用的内存量，堆的大小就由运行时来调整了。运行时如何决定这个大约等于 T1 的值？如果您深入研究 [JVM 调优指南](https://docs.oracle.com/javase/9/gctuning/parallel-collector1.htm#JSGCT-GUID-74BE3BC9-C7ED-4AF8-A202-793255C864C4)，您将会看到以下内容。

> “除非在命令行中指定了初始和最大堆大小，否则它们是根据机器上的内存量来计算的。默认的最大堆大小是物理内存的四分之一，而初始堆大小是物理内存的 1/64。分配给年轻一代的最大空间量是总堆大小的三分之一。

默认情况下，运行时将使用可用内存的`1/4th`。在我们的例子中，这意味着大约是我们看到的数字`512/4 = 128 megabytes`。

## 处理器限制

让我们来看看限制容器可用的 cpu，看看会发生什么。从 JDK 8 更新 131 开始，运行时应该知道可用 CPU 的数量，并相应地调整线程数。在 JVM 的情况下，一个 cpu 相当于一个内核。

```
$ docker run -it --cpus=1 zsiegel:java-and-docker
Number of processors: 4
Max memory: 468189184 bytes 
```

这是我第一次运行它时所期望的，所以让我们深入了解一下到底发生了什么。

Docker `--cpus`标志指定容器可以使用的可用 CPU 资源的百分比。具体指的是`cpu-shares`。它确实`not`调整容器可以访问的物理处理器的数量，这是 jdk8 运行时当前在设置处理器数量时使用的。

在 JDK 10 中，未来的工作将会纠正这个缺点。可以关注[这里](https://bugs.openjdk.java.net/browse/JDK-8146115)T3】的进展和讨论

让我们尝试使用另一个名为`--cpuset-cpus`的标志。此标志用于限制容器可以使用的核心。在 4 核计算机上，我们可以指定 0-3。我们可以通过逗号分隔核心索引来指定单个核心甚至多个核心。

```
$ docker run -it --cpuset-cpus=0 zsiegel:java-and-docker
Number of processors: 1
Max memory: 508887040 bytes 
```

```
$ docker run -it --cpuset-cpus=0,1 zsiegel:java-and-docker
Number of processors: 2
Max memory: 508887040 bytes 
```

这个结果比较符合我们的预期。我们现在可以让运行时正确地看到 2 个内核可用，而不是 4 个。当我们这样做时，运行时将相应地调整编译器和垃圾收集线程的数量。

值得注意的是，许多依赖线程池的库和框架会根据这些数量来调整线程的数量。`You would think that this would address our problems but there is a catch!`

## 容器编排

上面的例子有一个主要问题。像 Mesos 和 Kubernetes 这样的绝大多数容器编排工具都设置了`cpu-shares`而不是`cpuset-cpus`。这意味着，在之前提到的 JDK10 [中的工作](https://bugs.openjdk.java.net/browse/JDK-8146115)完成之前，依赖`runtime.availableProcessors()`的运行时和框架将无法正确调整它们的线程数。我的希望是，如果可能的话，这项工作将至少回移植到 JDK9。

如果你想了解更多关于 Docker 中用于调整资源限制的标志的信息，你可以查看文档[这里](https://docs.docker.com/config/containers/resource_constraints/)