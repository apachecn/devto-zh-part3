# 码头工人揭秘

> 原文：<https://dev.to/frosnerd/docker-demystified-27kl>

# 简介

自 2013 年开源发布以来，Docker 成为了最受欢迎的技术之一。许多公司都做出了贡献，大量的人正在使用和采纳它。但是为什么这么受欢迎呢？它提供了什么以前没有的东西？在这篇博客文章中，我们想深入了解 Docker 的内部结构，以了解它是如何工作的。

这篇文章的第一部分将给出基本架构概念的快速概述。在第二部分中，我们将介绍 Docker 容器中形成隔离基础的四个主要功能:1) cgroups，2) namespaces，3)可堆叠的图像层和写入时复制，以及 4)虚拟网桥。第三部分将讨论使用容器和 Docker 时的机遇和挑战。最后，我们回答一些关于 Docker 的常见问题。

# 基础架构

> " Docker 是一个开源项目，可以在软件容器中自动部署应用程序."——*维基百科*

当谈到操作系统级虚拟化时，人们通常会提到容器。操作系统级虚拟化是一种操作系统内核允许存在多个独立应用程序实例的方法。容器有很多实现，Docker 就是其中之一。

Docker 推出基于*图片*的集装箱。一个图像就像一个蓝图，定义了当它被创建的时候容器里面应该有什么。定义图像的通常方式是通过 *Dockerfile* 。docker 文件包含了如何一步一步建立你的映像的说明(不要担心，稍后你会对内部发生的事情有更多的了解)。例如，下面的 Dockerfile 将从包含 OpenJDK 的映像开始，在那里安装 Python 3，将`requirements.txt`复制到映像中，然后安装需求文件中的所有 Python 包。

```
FROM openjdk:8u212-jdk-slim

RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    Python3=3.5.3-1 \
    Python3-pip=9.0.1-2+deb9u1 \
  && rm -rf /var/lib/apt/lists/*

COPY requirements.txt requirements.txt
RUN pip3 install --upgrade -r requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

图像通常存储在名为 Docker *registries* 的图像库中。Dockerhub 是一个公共的 Docker 注册表。为了下载图像和启动容器，你需要一个 Docker *主机*。Docker 主机是一台运行 Docker *守护进程*的 Linux 机器(守护进程是一个始终运行的后台进程，等待工作完成)。

为了启动容器，您可以使用 Docker 客户机，它向 Docker 守护进程提交必要的指令。如果 Docker 守护进程在本地找不到请求的映像，它也会与 Docker 注册表进行通信。下图说明了 Docker 的[基础架构](https://docs.docker.com/engine/docker-overview/):

[![docker architecture](img/7afb609067673f9e85a522857b4d2ac7.png)](https://docs.docker.com/engine/docker-overview/)

需要注意的是，Docker 本身并不提供真正的容器化，而只是使用 Linux 中可用的东西。让我们深入研究技术细节。

# 容器隔离

Docker 通过四个主要概念的组合来实现不同容器的隔离:1) *cgroups* ，2) *namespaces* ，3)可堆叠的 *image-layers* 和 *copy-on-write* ，以及 4) *虚拟网桥*。在下面的小节中，我们将详细解释这些概念。

## 控制组(cgroups)

Linux 操作系统管理可用的硬件资源(内存、CPU、磁盘 I/O、网络 I/O，...)并为进程访问和利用它们提供了一种方便的方式。例如，Linux 的 CPU 调度程序负责每个线程最终在 CPU 内核上获得一些时间，这样就不会有应用程序在等待 CPU 时间。

控制组(cgroups)是一种将资源子集分配给特定进程组的方法。例如，这可以用来确保即使您的 CPU 忙于 Python 脚本，您的 PostgreSQL 数据库仍然可以获得专用的 CPU 和 RAM。下图在一个具有 4 个 CPU 内核和 16 GB RAM 的示例场景中说明了这一点。

[![cgroups](img/acd028fc418409ca8601fdd5193fb25b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NzsoSDzi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/49ymdqhhg9eb4cgemflr.png)

在 zeppelin-grp 中启动的所有 Zeppelin 笔记本将只利用核心 1 和 2，而 PostgreSQL 进程共享核心 3 和 4。这同样适用于记忆。Cgroups 是容器隔离中的一个重要构件，因为它们允许硬件资源隔离。

## 命名空间

cgroups 隔离硬件资源，而名称空间隔离和虚拟化系统资源。可以虚拟化的系统资源示例包括进程 id、主机名、用户 id、网络访问、进程间通信和文件系统。让我们首先深入到一个进程 ID (PID)名称空间的例子中，使这一点更加清楚，然后简单地讨论一下其他名称空间。

### PID 名称空间

Linux 操作系统在所谓的进程树中组织进程。树根是操作系统启动后启动的第一个进程，它的 PID 为 1。因为只能存在一个进程树，并且所有其他进程(例如 Firefox、终端模拟器、SSH 服务器)都需要由该进程(直接或间接)启动。由于该进程初始化所有其他进程，因此它通常被称为 *init* 进程。

下图显示了典型进程树的各个部分，其中 init 进程启动了一个日志记录服务(`syslogd`)、一个调度程序(`cron`)和一个登录 shell ( `bash` ):

```
1 /sbin/init
+-- 196 /usr/sbin/syslogd -s
+-- 354 /usr/sbin/cron -s
+-- 391 login
    +-- 400 bash
        +-- 701 /usr/local/bin/pstree 
```

Enter fullscreen mode Exit fullscreen mode

在这个树中，如果愿意，每个进程都可以看到其他进程并发送信号(例如，请求进程停止)。使用 PID 名称空间虚拟化特定进程及其所有子进程的 PID，使它认为它拥有 PID 1。然后，除了它自己的子进程之外，它也看不到任何其他进程。下图说明了不同的 PID 名称空间如何隔离两个 Zeppelin 流程的流程子树。

```
1 /sbin/init
|
+ ...
|
+-- 506 /usr/local/zeppelin
    1 /usr/local/zeppelin
    +-- 2 interpreter.sh
    +-- 3 interpreter.sh
+-- 511 /usr/local/zeppelin
    1 /usr/local/zeppelin
    +-- 2 java 
```

Enter fullscreen mode Exit fullscreen mode

### 文件系统名称空间

名称空间的另一个用例是 Linux 文件系统。与 PID 名称空间类似，文件系统名称空间虚拟化并隔离了树的一部分——在本例中是文件系统树。Linux 文件系统被组织成一棵树，它有一个根，通常称为`/`。

为了实现文件系统级的隔离，命名空间将把文件系统树中的一个节点映射到该命名空间内的一个虚拟根目录。浏览该名称空间内的文件系统，Linux 不允许您超出虚拟化的根目录。下图显示了一个文件系统的一部分，该文件系统在`/drives/xx`文件夹中包含多个“虚拟”文件系统根，每个根包含不同的数据。

[![filesystem namespace example](img/3d523da886c6cc2c58a11bed58341297.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sMKo15Wy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/758b8sygjjivyqdp0thz.png)

### 其他命名空间

除了 PID 和文件系统名称空间之外，还有其他类型的名称空间。Docker 允许您利用它们来达到您需要的隔离程度。例如，用户名称空间允许您将容器内部的用户映射到外部的不同用户。这可以用来将容器内的根用户映射到容器外的非根用户，因此容器内的进程在容器外就像管理员一样，没有特殊权限。

## 可堆叠图像层和写入时复制

既然我们对硬件和系统资源隔离如何帮助我们构建容器有了更详细的了解，我们将研究 Docker 存储图像的方式。正如我们前面看到的，Docker 图像就像一个容器的蓝图。它附带了启动它所包含的应用程序所需的所有依赖项。但是这些依赖是如何存储的呢？

Docker 将图像保存在*可堆叠层*中。层包含对前一层的更改。例如，如果您首先安装 Python，然后复制 Python 脚本，那么您的映像将有两个附加图层:一个包含 Python 可执行文件，另一个包含脚本。下图显示了一个 Zeppelin，一个 Spring 和一个 PHP 图像，都是基于 Ubuntu 的。

[![stackable image layers](img/1aa0ccf3787647bad037940da308ff3b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cjXlhCe3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ob5jicwj7klqay4avekh.png)

为了不三次存储 Ubuntu，层是不可变的，共享的。Docker 使用*写时复制*来只在有变化时复制一个文件。

当启动基于映像的容器时，Docker 守护进程将为您提供该映像中包含的所有层，并将其放在该容器的独立文件系统名称空间中。可堆叠层、写时复制和文件系统名称空间的组合使您能够完全独立于 Docker 主机上“安装”的东西来运行容器，而不会浪费大量空间。这是容器比虚拟机更轻量级的原因之一。

## 虚拟网桥

现在，我们知道了隔离硬件资源(cgroups)和系统资源(namespaces)的方法，以及如何为每个容器提供一组预定义的依赖关系，以独立于主机系统(图像层)。最后一个构建模块是*虚拟网桥*，它帮助我们隔离容器内的网络堆栈。

网桥是从多个通信网络或网段创建单个聚合网络的计算机网络设备。让我们来看看连接两个网段(LAN 1 和 LAN 2)的物理网桥的典型设置:

[![network bridge](img/dc7088a85356abee64d1ba419be85982.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8y3E8_ms--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qpwxpivuqqz9gir673aj.png)

通常我们在 Docker 主机上只有有限数量的网络接口(例如物理网卡),所有进程都需要共享对它的访问。为了隔离容器的网络，Docker 允许您为每个容器创建一个虚拟网络接口。然后，它将所有虚拟网络接口连接到主机网络适配器，如下图所示:

[![docker virtual network bridge](img/a8b0b47a6977c7871036c959d49e7f53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1pHJ5UY1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bpn9fs2lvmgigyvya7ot.png)

本例中的两个容器在其网络名称空间中有自己的`eth0`网络接口。它被映射到 Docker 主机上相应的虚拟网络接口`veth0`和`veth1`。虚拟网桥`docker0`将主机网络接口`eth0`连接到所有容器网络接口。

Docker 在配置网桥方面给了您很大的自由度，因此您可以只向外界公开特定的端口，或者直接将两个容器连接在一起(例如，一个数据库容器和一个需要访问它的应用程序)，而不向外界公开任何内容。

## 连接圆点

利用前面小节中描述的技术和特性，我们现在能够“容器化”我们的应用程序。虽然可以使用 cgroups、名称空间、虚拟网络适配器等手动创建容器。，Docker 是一个让它变得方便并且几乎没有开销的工具。它处理所有手动的、配置密集型的任务，使软件开发人员可以访问容器，而不仅仅是 Linux 专家。

事实上，Docker 的一位工程师做了一个很好的演讲，他演示了如何手动创建一个容器，并解释了我们在这一小节中涉及的细节。

# 码头工人的机遇与挑战

到目前为止，许多人每天都在使用 Docker。容器增加了什么好处？Docker 提供了什么以前没有的东西？最后，你需要的所有容器化应用程序的东西在 Linux 中已经存在很长时间了，不是吗？

让我们来看看在迁移到基于容器的设置时您所拥有的一些机会(当然不是详尽的列表)。当然，在采用 Docker 时，不仅有机会，也有可能给你带来困难的挑战。我们还将在这一部分列举几个例子。

## 机遇

Docker 启用 DevOps。devo PS 理念试图将开发和运营活动联系起来，让开发人员能够自己部署他们的应用。*你建造它，你经营它。*有了基于 Docker 的部署，开发人员可以直接将他们的工件和所需的依赖项一起发送，而不必担心依赖项冲突。它还允许开发人员编写更复杂的测试并更快地执行它们，例如，在另一个容器中创建一个真实的数据库，并在几秒钟内将其链接到他们笔记本电脑上的应用程序(参见 [Testcontainers](https://www.testcontainers.org/) )。

容器增加了部署的可预测性。不再“在我的机器上运行”。不再有失败的应用程序部署，因为一台机器上安装了不同版本的 Java。您只需构建一次映像，就可以在任何地方运行它(假设安装了 Linux 内核和 Docker)。

**采用率高，与许多杰出的集群经理有良好的整合。使用 Docker 的一个重要原因是围绕它的软件生态系统。如果您计划进行大规模操作，您将无法使用这种或那种集群管理器。如果您决定让其他人管理您的部署(例如 Google Cloud、Docker Cloud、Heroku、AWS，...)或者想要维护自己的集群管理器(例如 Kubernetes、Nomad、Mesos)，有很多解决方案。**

轻量级容器支持快速故障恢复或自动伸缩。想象一下经营一家网店。在圣诞节期间，人们会开始攻击你的网络服务器，而你目前的设置在容量上可能是不够的。假设您有足够的免费硬件资源，启动几个托管您的 web 应用程序的容器只需要几秒钟。此外，故障机器可以通过将容器迁移到新机器来恢复。

## 挑战

集装箱给人一种虚假的安全感。在保护您的应用程序时，有许多陷阱。认为把它们放在集装箱里是保护它们的一种方法是错误的。容器本身并不能保护任何东西。如果有人入侵了您的容器化 web 应用程序，他可能会被锁定在名称空间中，但是根据设置的不同，有几种方法可以避免这种情况。请意识到这一点，并在没有 Docker 的情况下投入尽可能多的精力来保护安全。

Docker 让人们可以轻松部署半成品解决方案。挑选你最喜欢的一款软件，将其名称输入谷歌搜索栏，添加“Docker”。您可能会在 Dockerhub 上找到至少一个(如果不是几十个)已经公开的包含您的软件的图片。那么，为什么不执行它，试一试呢？什么会出错？很多事情都会出错。东西放入容器时看起来闪闪发光，令人敬畏，人们不再关注里面的实际软件和配置。

**fat 容器反模式导致大型、难以管理的部署工件。**我见过 Docker 图片，它要求你在集装箱内露出 20 多个不同应用的端口。Docker 的理念是一个容器应该完成一项工作，你应该把它们组合起来，而不是让它们变得更重。如果您最终将所有工具放在一个容器中，您将失去所有优势，可能会有不同版本的 Java 或 Python 在里面，最终会有一个 20 GB 的、无法管理的映像。

在某些情况下，调试可能仍然需要深厚的 Linux 知识。你可能听过你的同事说 XXX 不与 Docker 合作。发生这种情况有多种原因。如果一些应用程序不能正确区分它们绑定到的网络接口和它们通告的网络接口，那么它们在桥接网络命名空间内运行时会出现问题。另一个问题可能与 cgroups 和 namespaces 有关，它们在共享内存方面的默认设置与您喜欢的 Linux 发行版不同，导致在容器中运行时出现 OOM 错误。然而，大多数问题实际上并不与 Docker 有关，而是与应用程序设计不当有关，而且它们并不常见。尽管如此，他们仍然需要对 Linux 和 Docker 的工作原理有更深入的了解，而这并不是每个 Docker 用户都具备的。

# 常见问题

## 问:容器和虚拟机有什么区别？

在不深入研究虚拟机(VM)架构细节的情况下，让我们从概念层面来看一下两者之间的主要区别。容器运行在操作系统内部，使用内核特性来隔离应用程序。另一方面，虚拟机需要在操作系统内部运行的虚拟机管理程序。然后，虚拟机管理程序创建可由另一组操作系统访问的虚拟硬件。下图比较了基于虚拟机的应用程序设置和基于容器的设置。

[![vm vs container](img/4209e0c0d84751e3260274e446ad77fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iiUS40qD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d5sazt7ln80e63qvhz1r.png)

如您所见，基于容器的设置开销较小，因为它不需要为每个应用程序添加额外的操作系统。这是可能的，因为容器管理器(例如 Docker)直接使用操作系统功能来以更轻量级的方式隔离应用。

这是否意味着容器优于虚拟机？看情况。这两种技术都有自己的用例，有时甚至有必要将它们结合起来，在 VM 中运行容器管理器。有许多博客文章讨论了这两种解决方案的优缺点，所以我们现在不打算详细讨论。重要的是要理解其中的区别，不要把容器看作某种“轻量级 VM”，因为它们在内部是不同的。

## 问:容器包含？

看看容器的定义和我们到目前为止学到的东西，我们可以有把握地说，使用 Docker 部署独立的应用程序是可能的。通过将控制组和命名空间与可堆叠的图像层和虚拟网络接口以及虚拟网桥相结合，我们拥有了完全隔离应用程序所需的所有工具，还可能锁定容器中的进程。然而现实表明这并不容易。首先，它需要正确配置，其次，您会注意到完全隔离的容器在大多数情况下没有多大意义。

最终，您的应用程序需要有一些副作用(将数据保存到磁盘，通过网络发送数据包，...).因此，您最终会通过转发网络流量或将主机卷装载到您的文件系统名称空间中来打破隔离。此外，不需要使用所有可用的名称空间功能。虽然网络、PID 和文件系统命名空间功能在默认情况下是启用的，但使用用户 ID 命名空间需要您添加额外的配置选项。

因此，认为把东西放在容器里就一定安全的想法是错误的。例如，AWS 使用一个名为[鞭炮](https://github.com/firecracker-microvm/firecracker)的轻量级 VM 引擎来安全地多租户执行短期工作负载。

## 问:容器让我的生产环境更稳定吗？

有些人认为容器增加了稳定性，因为它们隔离了错误。虽然在某种程度上，正确配置的名称空间和 cgroups 会限制一个进程失控的副作用，但在实践中需要记住一些事情。

如前所述，容器只有在正确配置并且大多数时候希望它们与系统的其他部分交互时才包含。因此，可以说容器有助于提高部署的稳定性，但是您应该始终记住，它不能保护您的应用程序免于失败。

# 结论

Docker 是一项很棒的技术，它以一种或多或少可复制和隔离的方式独立部署应用程序。和往常一样，没有一个放之四海而皆准的解决方案，在选择 Docker 作为工具之前，您应该了解您在安全性、性能、可部署性、可观察性等方面的需求。

幸运的是，围绕 Docker 已经有了一个伟大的工具生态系统。可以根据需要添加服务发现、容器编排、日志转发、加密和其他用例的解决方案。我想引用我最喜欢的一条推文来结束这篇文章:

> "把坏掉的软件放进 Docker 容器并不能减少它的坏掉."- [@sadserver](https://twitter.com/sadserver/status/587840646968250368)