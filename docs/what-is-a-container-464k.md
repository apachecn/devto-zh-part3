# 什么是容器？

> 原文：<https://dev.to/andrewhowdencom/what-is-a-container-464k>

容器最近已经成为在各种环境中的各种机器上打包、部署和运行软件的一种常见方式。随着 2013 年 3 月 Docker 的首次发布， <sup>[ [1](#wikipedia.docker) ]</sup> 容器已经在现代软件部署中无处不在，71%的财富 100 强公司都在以某种方式运行它 <sup>[ [2](#redmonk.f100) ]</sup> 。容器可用于:

*   运行面向用户的生产软件
*   运行软件开发环境
*   在沙箱中编译软件及其依赖项
*   在沙箱中分析软件的行为

就像航运业中的容器一样，容器被设计成可以很容易地将软件“提升和转移”到不同的环境中，并使软件在这些环境中以相同的方式执行。

容器因此在现代软件开发工具包中赢得了一席之地。然而，为了理解容器技术如何适应我们的现代软件架构，有必要理解我们是如何得到容器的，以及它们是如何工作的。

# 历史

Bryan Cantrill 将容器的“诞生”标记为 1982 年 3 月 18 日 <sup>[ [3](#youtube.bryancantrill.revolution) ]</sup> ，并添加了 BSD 中的`chroot` syscall。来自 FreeBSD 网站<sup>[[4](#bsd.jail)</sup>:

> 根据 SCCS 日志，chroot 调用是由 Bill Joy 在 1982 年 3 月 18 日添加的，大约比 4.2BSD 发布早一年半。那是在我们拥有任何类型的 ftp 服务器之前(ftp 直到 1983 年 1 月才出现在源代码树中)。我对其目的的最佳猜测是允许 Bill 进入/4.2BSD 构建目录，并仅使用该树中包含的文件、包含文件等来构建系统。这是我记得的早期 chroot 的唯一用法。
> 
> 马歇尔·柯克·麦克库西克博士

`chroot`用于将一个流程放入一个“变更根”；对父根文件系统具有有限访问权限或没有访问权限的新根文件系统。一个极小的`chroot`可以在 Linux 上创建如下 <sup>[ [5](#sagar.chroot) ]</sup> :

```
# Get a shell
$ cd $(mktemp -d)
$ mkdir bin
$ $(which sh) bin/bash

# Find shared libraries required for shell
$ ldd bin/sh
    linux-vdso.so.1 (0x00007ffe69784000)
    /lib/x86_64-linux-gnu/libsnoopy.so (0x00007f6cc4c33000)
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f6cc4a42000)
    libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f6cc4a21000)
    libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f6cc4a1c000)
    /lib64/ld-linux-x86-64.so.2 (0x00007f6cc4c66000)

# Duplicate libraries into root
$ mkdir -p lib64 lib/x86_64-linux-gnu
$ cp /lib/x86_64-linux-gnu/libsnoopy.so \
    /lib/x86_64-linux-gnu/libc.so.6 \
    /lib/x86_64-linux-gnu/libpthread.so.0 \
    /lib/x86_64-linux-gnu/libdl.so.2 \
    lib/x86_64-linux-gnu/

$ cp /lib64/ld-linux-x86-64.so.2 lib64/

# Change into that root
$ sudo chroot .

# Test the chroot
# ls
/bin/bash: 1: ls: not found
# 
```

这个早期的`chroot`实现存在一些问题，比如可以通过运行`cd..` <sup>[ [3](#youtube.bryancantrill.revolution) ]</sup> 来退出那个`chroot`，但是这些问题很快就解决了。为了提供更好的安全性，FreeBSD 将`chroot`扩展为`jail`<sup>[3](#youtube.bryancantrill.revolution)[4](#bsd.jail)</sup>，这允许运行希望作为`root`运行的软件，并在受限环境中运行，该受限环境是在该环境中的`root`，而不是在系统的其他地方的`root`。

这项工作进一步建立在 Solaris 操作系统上，以提供与主机 <sup>[ [3](#youtube.bryancantrill.revolution) ][ [6](#joyant.zones) ]</sup> 更全面的隔离:

*   用户分离(类似于`jail`)
*   文件系统分离(类似于`chroot`)
*   独立的处理空间

提供类似于现代容器概念的东西；运行在同一个内核上的进程。后来，在 Linux 内核中进行了类似的工作，在“名称空间” <sup>[ [7](#lwn.namespaces) ]</sup> 下基于每个进程隔离内核结构。

然而，与此同时，亚马逊网络服务(AWS)推出了他们的弹性计算云(EC2)产品，该产品采用了不同的方法来分离工作负载:虚拟化整个硬件<sup>[3](#youtube.bryancantrill.revolution)</sup>。这有一些不同的权衡；它限制了对主机内核或隔离实施的利用，但是运行额外的操作系统和虚拟机管理程序意味着资源的使用效率大大降低。

虚拟化继续主导工作负载隔离，直到当时作为“平台即服务”(PAAS)产品运营的公司“dotcloud”(现在的 Docker)开源了他们用于运行 PAAS 的软件。有了这个软件和大量的幸运容器，Docker 迅速发展，直到成为现在这样的强大公司。

Docker 发布其容器运行时后不久，他们开始将其产品扩展到构建、编排和服务器管理工具 <sup>[ [8](#coreos.rocket) ]</sup> 。不满意这一点的 CoreOS 创建了他们自己的容器运行时`rkt`，它的既定目标是与现有的服务如`systemd`进行互操作，遵循[UNIX 哲学](https://en.wikipedia.org/wiki/Unix_philosophy)“编写做一件事并把它做好的程序 <sup>[ [9](#catb.unix) ]</sup> ”

为了调和容器的这些分离的定义，开放容器倡议成立了 <sup>[ [10](#oci.registry) ]</sup> ，之后 Docker 贡献了它的模式和运行时，这相当于事实上的容器标准。

现在有许多容器实现，以及许多定义其行为的标准。

# 定义

“容器”并不是一个真实的东西，而是一个规范，这可能会令人惊讶。在撰写本规范时，该规范已于<sup>[11](#github.opencontainers.runtime):</sup>实施

*   Linux 操作系统
*   Windows 操作系统
*   Solaris
*   虚拟计算机

反过来，集装箱预计是<sup>[12](#github.opencontainers.principles)</sup>:

1.  可与一套标准的、可互操作的工具一起使用
2.  无论运行什么类型的软件，都保持一致
3.  与运行容器的底层基础设施无关
4.  以使自动化变得容易的方式设计
5.  质量优良的

有一些规范通过定义应该如何执行来规定容器应该如何达到这些原则(运行时规范 <sup>[ [11](#github.opencontainers.runtime) ]</sup> )、容器应该包含什么(映像规范 <sup>[ [13](#github.opencontainers.image) ]</sup> )以及如何分发容器“映像”(分发规范<sup>[[14](#github.opencontainers.distribution)</sup>)。

这些规范意味着可以使用各种各样的工具与容器进行交互。最常用的规范工具是 Docker 工具，除了操作容器之外，它还提供容器构建工具和一些有限的容器编排。但是，有许多容器运行时:

*   [码头工人](https://www.docker.com/)
*   [Rkt](https://github.com/rkt/rkt)
*   抚养他
*   [LXC](https://discuss.linuxcontainers.org/t/lxc-3-0-0-has-been-released/1449)
*   [“透明集装箱”](https://github.com/clearcontainers/runtime)

以及其他有助于构建或分发图像的工具。

最后，还有对现有标准的扩展，比如[容器网络接口](https://github.com/containernetworking/cni)，它定义了标准还不够清晰的附加行为。

# 实现

虽然这些标准让我们对什么是容器以及它们应该如何工作有了一些了解，但是理解容器实现如何工作可能是有用的。并非所有的容器运行时都以这种方式实现；值得注意的是，kata 容器实现了硬件虚拟化，这在 EC2 中已经提到过。

集装箱解决的问题是:

1.  一个或多个过程的隔离
2.  进程的分布
3.  将该进程连接到其他机器

说完这些，让我们深入到 Docker 实现<sup>[15](#docker.overview)</sup>中。这使用了底层内核公开的一系列技术:

## 内核特性隔离:名称空间

`man namespaces`命令如下定义名称空间:

> 名称空间将全局系统资源包装在一个抽象中，使名称空间中的进程看起来拥有自己的全局资源的独立实例。对全局资源的更改对作为命名空间成员的其他进程可见，但对其他进程不可见。名称空间的一个用途是实现容器。

换句话说，名称空间是系统的一部分，在这个部分中，进程看不到系统的其余部分。

一个进程必须对 Linux 内核进行系统调用来改变它的名字空间。有几个系统调用:

*   `clone`:创建一个新流程。当与`CLONE_NEW*`一起使用时，它创建一个指定类型的名称空间。例如，如果与`CLONE_NEWPID`一起使用，该流程将进入一个新的`pid`名称空间，并成为`pid 1`
*   `setns`:允许调用进程加入一个现有的名称空间，在`/proc/[pid]/ns`下指定
*   `unshare`:将调用进程移动到一个新的名称空间

有一个用户命令也叫做`unshare`，它允许我们试验名称空间。我们可以使用以下命令将自己置于一个单独的进程和网络名称空间中:

```
# Scratch space
$ cd $(mktemp -d)

# Fork is required to spawn new processes, and proc is mounted to give accurate process information
$ sudo unshare \
    --fork \
    --pid \
    --mount-proc \
    --net

# Here we see that we only have access to the loopback interface
root@sw-20160616-01:/tmp/tmp.XBESuNMJJS# ip addr
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

# Here we see that we can only see the first process (bash) and our `ps aux` invocation
root@sw-20160616-01:/tmp/tmp.XBESuNMJJS# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.3  0.0   8304  5092 pts/7    S    05:48   0:00 -bash
root         5  0.0  0.0  10888  3248 pts/7    R+   05:49   0:00 ps aux 
```

Docker 使用以下名称空间来限制在容器中运行的进程查看容器外资源的能力:

*   名称空间:进程隔离(PID:进程 ID)。
*   名称空间:管理网络接口(NET: Networking)。
*   名称空间:管理对 IPC 资源的访问(IPC:进程间通信)。
*   名称空间:管理文件系统挂载点(MNT: Mount)
*   名称空间:隔离内核和版本标识符。(UTS: Unix 分时系统)。

这些在进程之间提供了合理的分隔，这样工作负载就不会相互干扰。然而，有一个值得注意的警告:**我们可以禁用一些这种隔离** <sup>[ [16](#youtube.jfrazelle.containers-crazy) 。</sup>

这是一个非常有用的属性。例如，系统守护进程需要访问主机网络来绑定主机上的端口 <sup>[ [17](#docker.hostnamespace) ]</sup> ，例如在容器中运行 DNS 服务或服务代理。

> **提示**
> 
> Linux 系统中的进程#1 或`init`有一些额外的职责。当进程在 Linux 中终止时，它们不会被自动清理，而是简单地进入终止状态。init 进程的职责是“收获”那些进程，删除它们以便它们的进程 ID 可以被重用 <sup>[ [18](#krallin.tini) ]</sup> 。相应地，在 Linux 命名空间中运行的第一个进程应该是一个`init`进程，而不是像`mysql`那样面向用户的进程。这就是所谓的*僵尸收割问题*。
> 
> **提示**
> 
> 另一个使用名称空间的地方是 Chromium 浏览器<sup>[19](#chrome.sandboxing)</sup>。Chromium 至少使用了`setuid`和`user`名称空间。

## 资源隔离:对照组

`cgroups`的内核文档将 cgroup 定义如下:

> 控制组提供了一种机制，用于将任务集及其所有未来的子任务聚集/划分到具有特定行为的分层组中。

虽然这并没有告诉我们太多。幸运的是它扩张了:

> 就其本身而言，cgroups 的唯一用途是用于简单的作业跟踪。这样做的目的是让其他子系统与通用的 cgroup 支持挂钩，为 cgroup 提供新的属性，例如计算/限制 cgroup 中的进程可以访问的资源。例如，cpusets(参见 Documentation/cgroup-v1/CPU sets . txt)允许您将一组 CPU 和一组内存节点与每个 cgroup 中的任务相关联。

因此，`cgroups`是一组其他系统可以赋予其意义的“作业”。当前使用此`cgroup`系统的系统:

*   [CPU](https://www.kernel.org/doc/Documentation/cgroup-v1/cpusets.txt)
*   [内存](https://www.kernel.org/doc/Documentation/cgroup-v1/memory.txt)
*   [PID](https://www.kernel.org/doc/Documentation/cgroup-v1/pids.txt)
*   [网络优先级](https://www.kernel.org/doc/Documentation/cgroup-v1/net_prio.txt)

以及各种其他的。

通过读写`/proc`文件系统来操作`cgroups`。例如:

```
# Create a cgroup called "me"
$  mkdir /sys/fs/cgroup/memory/me

# Allocate the cgroup a max of 100Mb memory
$ echo '100000000' | sudo tee /sys/fs/cgroup/memory/me/memory.limit_in_bytes

# Move this proess into the cgroup
$ echo $$  | sudo tee /sys/fs/cgroup/memory/me/cgroup.procs
5924 
```

就是这样！这个过程现在应该限制在 100Mb 的总使用量

Docker 在它的`--memory`和`--cpus`参数中使用相同的功能，它被编排系统 Kubernetes 和 Apache Mesos 用来确定在哪里调度工作负载。

> **提示**
> 
> 虽然`cgroups`通常与容器相关联，但是它们已经用于其他工作负载。最好的例子也许是`systemd`，如果在内核 <sup>[ [20](#0pointer.resources) ]</sup> 中启用了 CPU 调度程序，它会自动将所有服务放入一个`cgroup`。`systemd`服务是……一种容器！

## 用户区隔离:seccomp

虽然 namespaces 和`cgroups`都在很大程度上把进程隔离到它们自己的容器中，但是 Docker 在限制进程对 Linux 内核本身的访问方面做得更好。这是通过“带过滤器的安全计算”在支持的操作系统中强制执行的，也被称为`seccomp-bpf`或简称为`seccomp`。

Linux 内核用户空间 API 指南将`seccomp`定义为:

> Seccomp 筛选为进程提供了一种为传入系统调用指定筛选器的方法。如同套接字过滤器一样，过滤器被表示为 Berkeley 分组过滤器(BPF)程序，除了所操作的数据与正在进行的系统调用相关:系统调用号和系统调用参数。

反过来，BPF 是一种小型的内核虚拟机语言，用于许多内核跟踪、联网和其他任务<sup>[21](#lmc.ebpf-intro)</sup>。运行以下命令<sup>[[22](#docker.seccomp)</sup>可以确定系统是否支持 seccomp:

```
$ grep CONFIG_SECCOMP= /boot/config-$(uname -r)

# Our system supports seccomp
CONFIG_SECCOMP=y 
```

实际上，这限制了进程要求内核做某些事情的能力。任何系统调用都可以被限制，docker 通过它的`--security-opt`参数 <sup>[ [22](#docker.seccomp) ]</sup> 允许使用任意的 seccomp“配置文件”:

```
docker run --rm \
  -it \
  --security-opt seccomp=/path/to/seccomp/profile.json \
  hello-world 
```

然而，最有用的是 Docker 提供了一个默认的安全配置文件，它限制了一些更危险的系统调用，这些调用是从容器中运行的进程永远不需要进行的，包括:

*   克隆新名称空间的能力
*   `bpf`:加载和运行`bpf`程序的能力
*   `add_key`:访问内核密匙环的能力
*   加载新的 linux 内核的能力

以及许多其他人。默认情况下被阻止的系统调用的完整列表可以在 Docker 网站上找到[。](https://docs.docker.com/engine/security/seccomp/)

除了`seccomp`之外，还有其他方法可以确保容器按预期运行，包括:

*   Linux 功能<sup>[23](#docker.sec.capabilities)</sup>
*   SELinux
*   表观摩尔
*   审计
*   法尔科 <sup>[ [24](#sysdig.falco.discussion) ]</sup>

每种方法都采取了略微不同的方法来确保流程仅在预期行为内执行。值得花时间研究这些安全决策的权衡，或者干脆将选择委托给有能力的第三方提供商。

另外值得注意的是，即使 Docker 默认启用`seccomp`策略，编排系统如`kubernetes`也可能禁用它 <sup>[ [25](#kubernetes.pod-security-policy) ]</sup> 。

## 分发:联盟文件系统

生成容器 Docker 需要一组“构建指令”。琐碎的图像可能是:

```
# Scrath space
$ cd $(mktemp -d)

# Create a docker file
$ cat <<EOF > Dockerfile
FROM debian:buster

# Create a test directory
RUN mkdir /test

# Create a bunch of spam files
RUN echo $(date) > /test/a
RUN echo $(date) > /test/b
RUN echo $(date) > /test/c  EOF # Build the image
$ docker build .
Sending build context to Docker daemon  4.096kB
Step 1/5 : FROM debian:buster
 ---> ebdc13caae1e
Step 2/5 : RUN mkdir /test
 ---> Running in a9c0fa1a56c7
Removing intermediate container a9c0fa1a56c7
 ---> 6837541a46a5
Step 3/5 : RUN echo Sat 30 Mar 18:05:24 CET 2019 > /test/a
 ---> Running in 8b61ca022296
Removing intermediate container 8b61ca022296
 ---> 3ea076dcea98
Step 4/5 : RUN echo Sat 30 Mar 18:05:24 CET 2019 > /test/b
 ---> Running in 940d5bcaa715
Removing intermediate container 940d5bcaa715
 ---> 07b2f7a4dff8
Step 5/5 : RUN echo Sat 30 Mar 18:05:24 CET 2019 > /test/c
 ---> Running in 251f5d00b55f
Removing intermediate container 251f5d00b55f
 ---> 0122a70ad0a3
Successfully built 0122a70ad0a3 
```

这将创建一个 id 为`0122a70ad0a3`的 docker 映像，其中包含位于`a`、`b`和`c`的`date`的内容。我们可以通过启动容器并检查其内容来验证这一点:

```
$ docker run \
  --rm=true \
  -it \
  0122a70ad0a3 \
  /bin/bash

$ cd /test
$ ls a  b  c
$ cat *

Sat 30 Mar 18:05:24 CET 2019
Sat 30 Mar 18:05:24 CET 2019
Sat 30 Mar 18:05:24 CET 2019 
```

然而，在前面的`docker build`命令中 Docker 创建了几个图像。如果我们在仅执行了`a`和`b`之后运行映像，我们将看不到`c`:

```
$ docker run \
  --rm=true \
  -it \
  07b2f7a4dff8 \
  /bin/bash
$ ls test a  b 
```

Docker 不会为这些映像中的每一个创建一个全新的文件系统。取而代之的是，每一个图像都层叠在彼此之上。如果我们查询 Docker，我们可以看到进入给定图像的每个层:

```
$ docker history 0122a70ad0a3
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
0122a70ad0a3        5 minutes ago       /bin/sh -c echo Sat 30 Mar 18:05:24 CET 2019…   29B
07b2f7a4dff8        5 minutes ago       /bin/sh -c echo Sat 30 Mar 18:05:24 CET 2019…   29B
3ea076dcea98        5 minutes ago       /bin/sh -c echo Sat 30 Mar 18:05:24 CET 2019…   29B
6837541a46a5        5 minutes ago       /bin/sh -c mkdir /test                          0B
ebdc13caae1e        12 months ago       /bin/sh -c #(nop)  CMD ["bash"]                 0B
<missing>           12 months ago       /bin/sh -c #(nop) ADD file:2219cecc89ed69975…   106MB 
```

这使得 docker 可以重用它下载的大量内容。例如，给定我们之前构建的图像，我们可以看到它使用了:

1.  一个叫做`ADD file:…​`的层——这是 106MB 的 Debian Buster 根文件系统
2.  一个用于`a`的层，它将日期呈现到磁盘的 29B 处
3.  一个用于`b`的层，它将日期呈现到磁盘的 29B 处

诸如此类。Docker 将对所有以`FROM: debian:buster`开头的图像重用`Add file:…​` Debian Buster 根。

这使得 Docker 在可能的情况下非常节省空间，在多个不同的执行中重用同一个操作系统映像。

> **提示**
> 
> 尽管 docker 非常节省空间，但磁盘上的 docker 库会变得非常大，通过网络传输大型 Docker 映像会变得非常昂贵。因此，尽可能重复使用图像层，并尽可能选择较小的操作系统或`scratch`(无)图像。

这些层是通过联合文件系统或 UnionFS 实现的。有各种“后端”或文件系统可以实现这种方法:

*   `overlay2`

*   `devicemapper`

*   `aufs`

一般来说，我们机器上的包管理器将包含适当的底层文件系统驱动程序；docker 支持许多:

```
$ docker info | grep Storage
Storage Driver: overlay2 
```

我们可以用我们的覆盖挂载相当容易地复制这个实现 <sup>[ [26](#so.overlay2) ]</sup> :

```
# scratch
cd $(mktemp -d)

# Create some layers
$ mkdir \
  lower \
  upper \
  workdir \
  overlay

# Create some files that represent the layers
$ touch lower/i-am-the-lower
$ touch higher/i-am-the-higher

# Create the layered filesystem at overlay with lower, upper and workdir
$ mount -t overlay \
    -o lowerdir=lower,upperdir=upper,workdir=workdir \
    ./overlay \
    overlay

# List the directory
$ ls overlay/
i-am-the-lower  i-am-the-upper 
```

Docker 甚至嵌套这些层，直到多层文件系统成功实现。

在`overlay2`的情况下，写入的文件被写回到`upper`目录。然而，Docker 通常会在移除容器时处理这些临时文件。

> **提示**
> 
> 一般来说，所有软件都需要访问 Linux 操作系统中静态路径下的共享库。因此，惯例是简单地发布操作系统根文件系统的精简版本，以便用户可以安装并且应用程序可以找到他们期望的库。然而，可以使用一个空的文件系统和一个静态编译的带有`scratch`映像类型的二进制文件。

## 连接性:联网

如前所述，容器利用了 Linux 名称空间。在理解容器网络时，特别感兴趣的是网络名称空间。这个命名空间为进程提供了单独的:

*   (虚拟)以太网设备

*   路由表

*   `iptables`规则

举个例子，

```
# Create a new network namespace
$ sudo unshare --fork --net

# List the ethernet devices with associated ip addresses
$ ip addr
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

# List all iptables rules
root@sw-20160616-01:/home/andrewhowden# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source destination

Chain FORWARD (policy ACCEPT)
target     prot opt source destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source destination

# List all network routes
$ ip route show 
```

默认情况下，容器没有网络连接——甚至连`loopback`适配器也没有启动。我们甚至不能 ping 自己！

```
$ ping 127.0.0.1
PING 127.0.0.1 (127.0.0.1): 56 data bytes
ping: sending packet: Network is unreachable 
```

我们可以通过启动`loopback`适配器来开始设置预期的网络环境:

```
$ ip link set lo up
root@sw-20160616-01:/home/andrewhowden# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever

# Test the loopback adapter
$ ping 127.0.0.1
PING 127.0.0.1 (127.0.0.1): 56 data bytes
64 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.092 ms
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.068 ms 
```

然而，我们无法接触外部世界。在大多数环境中，我们的主机将通过以太网连接到给定的网络，或者通过云提供商为其分配一个 IP，或者在开发或办公机器的情况下，通过 DHCP 请求一个 IP。然而，我们的容器位于自己的网络名称空间中，不知道连接到主机的以太网。为了将容器连接到主机，我们需要使用一个`veth`设备。

`veth`或“虚拟以太网设备”由`man vetTo create a `veth`设备定义，我们可以运行为:

> veth 设备是虚拟以太网设备。它们可以充当网络命名空间之间的隧道，以创建到另一个命名空间中的物理网络设备的桥，但也可以用作独立的网络设备。

这正是我们所需要的！因为`unshare`创建了一个匿名网络名称空间，我们需要确定在该名称空间中启动的进程的`pid`是什么 <sup>[ [27](#so.anon-veth) ][ [28](#igalia.network-namespaces) ]</sup> :

```
$ echo $$
18171 
```

然后我们可以创建`veth`设备:

```
$ sudo ip link add veth0 type veth peer name veth0 netns 18171 
```

我们可以在主机和客户机上看到这些虚拟以太网设备。但是，既没有连接 IP，也没有定义任何路由:

```
# Container

$ ip addr
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: veth0@if7: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 16:34:52:54:a2:a1 brd ff:ff:ff:ff:ff:ff link-netnsid 0
$ ip route show

# No output 
```

要解决这个问题，我们只需添加一个 IP 并定义默认路由:

```
# On the host
$ ip addr add 192.168.24.1 dev veth0

# Within the container
$ ip address add 192.168.24.10 dev veth0 
```

从那里，打开设备:

```
# Both host and container
$ ip link set veth0 up 
```

增加一条路线，使`192.168.24.0/24`通过`veth0`出去；

```
# Both host and guest
ip route add 192.168.24.0/24 dev veth0 
```

瞧！我们可以连接到主机命名空间，也可以连接回来:

```
# Within container
$ ping 192.168.24.1
PING 192.168.24.1 (192.168.24.1): 56 data bytes
64 bytes from 192.168.24.1: icmp_seq=0 ttl=64 time=0.149 ms
64 bytes from 192.168.24.1: icmp_seq=1 ttl=64 time=0.096 ms
64 bytes from 192.168.24.1: icmp_seq=2 ttl=64 time=0.104 ms
64 bytes from 192.168.24.1: icmp_seq=3 ttl=64 time=0.100 ms 
```

然而，这并不能让我们接触到更广阔的互联网。虽然`veth`适配器充当我们的容器和主机之间的虚拟电缆，但是目前没有从我们的容器到互联网的路径:

```
# Within container
$ ping google.com
ping: unknown host 
```

要创建这样一条路径，我们需要修改我们的主机，使它在自己的、独立的网络名称空间和面向 internet 的适配器之间充当“路由器”。

幸运的是，Linux 为此做了很好的设置。首先，我们需要修改 Linux 的正常行为，不再丢弃目的地不是相关 IP 地址的数据包，而是允许将数据包从一个适配器转发到另一个适配器:

```
# Within container
$ echo 1 > /proc/sys/net/ipv4/ip_forward 
```

这意味着当我们从我们的容器内部通过我们的`veth`适配器向我们的主机`veth`适配器请求面向公众的 IP 时，主机适配器不会简单地丢弃这些数据包。

从那里，我们在主机上采用`iptables`规则，将流量从主机`veth`适配器转发到面向互联网的适配器——在本例中为`wlp2s0`:

```
# On the host
# Forward packets from the container to the host adapter
iptables -A FORWARD -i veth0 -o wlp2s0 -j ACCEPT

# Forward packets that have been established via egress from the host adapater back to the contianer
iptables -A FORWARD -i wlp2s0 -o veth0 -m state --state ESTABLISHED,RELATED -j ACCEPT

# Relabel the IPs for the container so return traffic will be routed correctly
iptables -t nat -A POSTROUTING -o wlp2s0 -j MASQUERADE 
```

然后，我们告诉我们的容器将它不知道的其他信息发送到`veth`适配器:

```
# Within the container
$ ip route add default via 192.168.24.1 dev veth0 
```

而且互联网管用！

```
$ # ping google.com
PING google.com (172.217.22.14): 56 data bytes
64 bytes from 172.217.22.14: icmp_seq=0 ttl=55 time=16.456 ms
64 bytes from 172.217.22.14: icmp_seq=1 ttl=55 time=15.102 ms
64 bytes from 172.217.22.14: icmp_seq=2 ttl=55 time=34.369 ms
64 bytes from 172.217.22.14: icmp_seq=3 ttl=55 time=15.319 ms 
```

如前所述，每个容器实现可以不同地实现网络。存在使用前述的`veth`对、`vxlan`、`BPF`或其他云特定实现的实现。然而，在设计容器时，我们需要一些方法来思考我们应该期待什么样的行为。

为了帮助解决这个问题，设计了[“容器网络接口”](https://github.com/containernetworking/cni)工具。这允许跨网络实现定义一致的网络行为，以及像 Kubernetes 这样的模型在几个容器之间共享`lo`适配器。

容器的网络方面是一个正在经历快速创新的领域，但是依赖于:

1.  一个`lo`界面
2.  一个面向公众的`eth0`(或类似的)接口

在场似乎是一个相当稳定的保证。

# 风景回顾

鉴于我们对容器实现的理解，我们现在可以看看一些经典的 docker 讨论。

## 系统更新

容器的一个经常被忽视的部分是保持它们和主机系统更新的必要性。

在现代系统中，简单地在主机系统上启用自动更新是很常见的，只要我们坚持系统软件包管理器并确保更新保持成功，系统将保持自己的最新和稳定。

然而，容器采用了一种非常不同的方法。它们实际上是部署到生产系统中的巨大静态二进制文件。在这种情况下，他们无法进行自我维护。

因此，即使容器运行的软件没有更新，容器也应该定期重建并重新部署到生产系统——以免它们随着时间的推移积累漏洞。

## 初始化容器内

根据我们对容器的理解，有理由考虑“每个容器一个过程”的建议，并确定这是对容器工作方式的过度简化，并且在某些情况下，使用类似`runit`的系统在容器内进行服务管理是有意义的。

这允许在一个容器中执行多个流程，包括:

*   `syslog`
*   `logrotate`
*   `cron`

所以第四。

在 docker 是唯一使用的系统的情况下，考虑在 Docker 中进行服务管理确实是合理的——特别是当遇到共享文件系统或网络状态的限制时。然而，诸如 Kubernetes、Swarm 或 Mesos 等系统已经取代了这些 init 系统的大部分必要性；日志聚合、重启服务或托管服务等任务由这些工具负责。

因此，最好保持容器简单，这样它们可以最大程度地组合并易于调试，将更复杂的行为委托出去。

# 总之

容器是将软件运送到生产系统的绝佳方式。它们解决了一系列有趣的问题，结果花费很少。然而，它们的快速增长意味着行业内对它们到底是如何工作的，它们是否稳定等等有些困惑。容器是新旧 Linux 内核技术(如名称空间、cgroups、seccomp 和其他 Linux 网络工具)的结合，但与任何其他内核技术一样稳定，非常适合生产系统。

走了这么远，少于 3 英镑。

# 参考文献

1.  "码头工人"https://en . Wikipedia . org/wiki/dock _(软件。
2.  《财富 100 强企业中的云原生技术》[https://red monk . com/fryan/2017/09/10/cloud-native-technologies-in-the-fortune-100/](https://redmonk.com/fryan/2017/09/10/cloud-native-technologies-in-the-fortune-100/)，2017 年 9 月。
3.  B.Cantrill，“容器革命:第一个十年后的反思。”[https://www.youtube.com/watch?v=xXWaECk9XqM](https://www.youtube.com/watch?v=xXWaECk9XqM)，2018 年 9 月。
4.  “文件(监狱)。”[https://docs.freebsd.org/44doc/papers/jail/jail.html](https://docs.freebsd.org/44doc/papers/jail/jail.html)。
5.  "一个绝对最小的 chroot . "2011 年 1 月，https://sagar.se/an-absolutely-minimal-chroot.html。
6.  J.贝克等人，“Solaris 操作系统中的虚拟化和命名空间隔离”(PSARC/2002/174)[https://us-east . manta . joyent . com/jmc/public/opensolaris/ARChive/PSARC/2002/174/zones-design . spec . opensolaris . pdf](https://us-east.manta.joyent.com/jmc/public/opensolaris/ARChive/PSARC/2002/174/zones-design.spec.opensolaris.pdf)，2006 年 9 月。
7.  米（meter 的缩写））Kerrisk，“操作中的名称空间，第 1 部分:名称空间概述”2013 年 1 月，https://lwn.net/Articles/531114/。
8.  A.Polvi，“CoreOS 正在构建一个容器运行时，rkt。”https://coreos.com/blog/rocket.html，2014 年 1 月。
9.  “Unix 哲学的基础”[http://www.catb.org/ ESR/writings/Tao up/html/ch01s 06 . html](http://www.catb.org/%C2%A0esr/writings/taoup/html/ch01s06.html)。
10.  页（page 的缩写）Estes 和 M. Brown，“OCI 图像支持来到开源 Docker 注册表”[https://www . open containers . org/blog/2018/10/11/OCI-image-support-comes-to-open-source-docker-registry](https://www.opencontainers.org/blog/2018/10/11/oci-image-support-comes-to-open-source-docker-registry)，2018 年 10 月。
11.  "开放容器倡议运行时规范."[https://github . com/open containers/runtime-spec/blob/74b 670 efb 921 f 9008 DCD fc 96145133 e 5b 66 CCA 5c/spec . MD](https://github.com/opencontainers/runtime-spec/blob/74b670efb921f9008dcdfc96145133e5b66cca5c/spec.md)，2018 年 3 月。
12.  "标准集装箱的 5 个原则."[https://github . com/open containers/runtime-spec/blob/74b 670 efb 921 f 9008 DC DFC 96145133 e 5b 66 CCA 5c/principles . MD](https://github.com/opencontainers/runtime-spec/blob/74b670efb921f9008dcdfc96145133e5b66cca5c/principles.md)，2016 年 12 月。
13.  "开放容器倡议图像规范."[https://github . com/open containers/image-spec/blob/db 4d 6 de 99 a2 ADF 83 a 672147 D5 f 05 a2 e 039 e 68 ab 6/spec . MD](https://github.com/opencontainers/image-spec/blob/db4d6de99a2adf83a672147d5f05a2e039e68ab6/spec.md)，2017 年 6 月。
14.  "开放集装箱倡议分发规范."[https://github . com/open containers/distribution-spec/blob/d 93 CFA 52800990932d 24 f 86 FD 233070 ad 9 ad C5 e 0/spec . MD](https://github.com/opencontainers/distribution-spec/blob/d93cfa52800990932d24f86fd233070ad9adc5e0/spec.md)，2019 年 3 月。
15.  “码头概述。”[https://docs.docker.com/engine/docker-overview/](https://docs.docker.com/engine/docker-overview/)。
16.  J.Frazelle，“容器又名疯狂的用户空间乐趣。”[https://www.youtube.com/watch?v=7mzbIOtcIaQ](https://www.youtube.com/watch?v=7mzbIOtcIaQ)，2018 年 1 月。
17.  "使用主机网络。"[https://docs.docker.com/network/host/](https://docs.docker.com/network/host/)。
18.  克拉林，“天宁:一个天宁，但有效的容器初始化。”[https://github.com/krallin/tini](https://github.com/krallin/tini)，2018 年 11 月。
19.  [https://chromium . Google source . com/chromium/src . git/+/HEAD/docs/Linux _ sandboxing . MD](https://chromium.googlesource.com/chromium/src.git/+/HEAD/docs/linux_sandboxing.md)。
20.  [0 pointer . resources]]l . poeting，《管理员系统设计，第十八部分》2012 年 10 月，http://0pointer.de/blog/projects/resources.html。
21.  A.你好，“开始掌握 eBPF”[https://www . little man . co/articles/coming-to-grips-with-ebpf/](https://www.littleman.co/articles/coming-to-grips-with-ebpf/)，2019 年 3 月。
22.  " docker 的 Seccomp 安全配置文件。"[https://docs.docker.com/engine/security/seccomp/](https://docs.docker.com/engine/security/seccomp/)。
23.  “Linux 内核功能。”[https://docs . docker . com/engine/security/security/# Linux-kernel-capabilities](https://docs.docker.com/engine/security/security/#linux-kernel-capabilities)。
24.  米（meter 的缩写））《SELinux，Seccomp，Sysdig Falco，和你:技术讨论》[https://sysdig . com/blog/selinux-sec comp-Falco-technical-discussion/](https://sysdig.com/blog/selinux-seccomp-falco-technical-discussion/)，2016 年 12 月。
25.  "豆荚安全政策。"[https://kubernetes . io/docs/concepts/policy/pod-security-policy/# sec comp](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp)。
26.  程序员，“示例覆盖用法”[https://askubuntu.com/a/704358](https://askubuntu.com/a/704358)，2015 年 11 月。
27.  “如何将‘匿名’网络命名空间内部的 veth 设备连接到外部的设备？”[https://unix.stackexchange.com/a/396210](https://unix.stackexchange.com/a/396210)，2017 年 10 月。
28.  D.P. García，“网络名称空间”[https://blogs . igalia . com/dpino/2016/04/10/network-namespaces/](https://blogs.igalia.com/dpino/2016/04/10/network-namespaces/)，2016 年 4 月。