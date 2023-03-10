# Docker 了解您的工具#2

> 原文：<https://dev.to/where_i_stuck/docker-for-mac--know-your-tools-2-28a7>

[![Docker Image](img/6e5e2781e588776f20d969446a2df2cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SzxmAh6U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8kex8622nc3p5je4c9g6.png)

Docker 微服务和分布式系统领域的巨兽。因此，在我目前的实习中，我被要求在 docker 容器环境中部署我的 Kafka Ruby 消费者。根据我对 Docker 的了解，这似乎很容易。但是我的经历完全不同。

## 你这里有什么？

*   关于 Docker 如何在 Mac 上工作的清晰明了的知识。

你为什么需要这个？你问是因为你在 Mac 上开发，生产实例是 Ubuntu。两者都是基于 UNIX 的操作系统，但是在 Docker 如何运行方面有所不同。

*   如果你不想自己去研究两种环境的 Docker 官方文档，那么这位先生/女士就是你要找的东西。它对调试也有很大帮助，因为你知道那里发生了什么。

* * *

## 区别

如果你是新来的，那么让我们先把地板打扫干净。有一种东西叫做基于容器的解决方案。这种方法的作用是将你的应用程序的代码、依赖关系、环境以及可执行文件捆绑在一起。并将该包称为容器。现在，应用程序正常运行所需的一切都在这个包或容器中。从现在开始，我们称它为容器。

所以现在我们需要一个操作系统内核来在硬件上运行这个应用程序。你得到那首热门歌曲了吗？这些不是您主机上的虚拟环境机器。没有虚拟机，一定要记住这一点。

那么 Docker 是什么？ [Docker](https://www.docker.com/) 只是这个方法的一个实现。而且在市场上并不孤单， [Kubernetes](https://kubernetes.io/) 也在这样做。

现在，如果你登录官方网站，你会看到类似于**扩展**、**自动部署**、**更好的架构**、**以及各种 DevOps 问题术语**的词汇。

Docker 做到了这一点，但是这些事情现在已经超出了这篇博客的范围。

* * *

### 现在让我们来挖掘一下，它是如何工作的。

[![digging deep](img/5bb487d9e665da38505c5280b5300821.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C6lrm1Ad--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gk4od66qmuexrsbplzdp.jpg)

* * *

**Linux OS (Ubuntu)**

我认为 docker 实际上是为在 Linux 环境下工作而构建的。这就是为什么它在 Ubuntu 上很好用。**你怎么问的？**

Ubuntu 上运行的 Docker 容器就像一个普通的内核进程。

*   如果我们使用`-p`选项从容器中公开端口，我们可以在主机上访问它。

```
docker container run -p <host port>:<container port> 
```

*   如果我们在 Ubuntu 中为一个容器使用一个卷(容器外的内存空间),我们可以从我们的主机上看到并访问它。因为它在您的主机上，您可以像访问另一个文件一样访问它。似乎显而易见，是的，但对 Ubuntu 来说。

**苹果操作系统**

首先，要明白 Mac OS Docker 有两条路可走。

*   Docker 工具箱(老方法)
*   Docker(新方式)

如果你停留在一个点，然后你去了堆栈溢出，你可能会看到一个答案，它应该在 Docker 工具箱上运行，而你正在 Mac 的 Docker 上运行，最终**弄糟了一切**。

### Docker 工具箱

无论你读到哪里，他们都说 docker 不是在虚拟机上运行，而是在主机操作系统上运行。转折来了。

> Docker Toolbox **在运行 Docker 容器的 Mac OS 上运行 Linux 的 VM**。

因此，如果你正在使用这个工具箱，请记住你在 Mac OS 和 docker 容器之间有一个虚拟的 Linux OS。你问这如何影响你的系统？

*   现在，如果你从 Docker 容器中公开一个端口，它将被 Linux 操作系统公开，而不是直接被 Mac 操作系统公开。**现在你需要将这个端口从 Linux 操作系统暴露给 Mac 操作系统**。看，额外的工作和头痛。

*   如果你的 Docker 容器使用了一个卷，它将在 **Linux 操作系统**和**上，而不是在你的 MacOS 上**。这意味着你**不能**访问它，知道它的存在。
    如果您想查看，那么您需要进入 Linux 操作系统，然后访问该卷。看到又头疼。

### MAC 坞站

在 mac 的新版本中，他们去掉了中间使用虚拟机的繁重的 Linux 层。
相反，现在 docker 是一个原生应用**但是**它仍然使用一个**原生虚拟化框架**叫做 [**HyperKit**](https://github.com/moby/hyperkit) 。

对你有什么影响？

*   如果您从容器中公开端口，它将对您的主机可用。所以中间不会再暴露操作系统。

*   但是我们仍然不能直接从主机访问 Docker 容器的卷。它们仍然在轻量级 Linux 层中。请参见[本](https://stackoverflow.com/a/41281658/6274382)了解如何访问它们的更多信息。

* * *

## 外卖

如果你在 docker 容器上遇到一些奇怪的问题，请记住以下几点:

*   在 Ubuntu(或其他 Linux)中，docker 容器和主机操作系统之间没有中间层。我猜你 90%的生产操作系统也是 Ubuntu。但是在你的 MacOS 上有一个中间层。

*   尽管 Docker 团队已经很好地使它尽可能地原生，但是在 MacOS 和容器之间仍然有一个轻量级的 Linux。

*   还要检查堆栈溢出的解决方案可能是针对 Docker 工具箱的，而不是针对 Docker for Mac(新版本)的。

> 如果它能帮助任何努力使用 docker 的人，它就达到了目的。

非常感谢你的阅读，让我们在评论区讨论更多。抛开你对这件事的想法，指出我是否犯了错误。让我们一起学习。