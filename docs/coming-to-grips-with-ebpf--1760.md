# 掌握 eBPF

> 原文：<https://dev.to/andrewhowdencom/coming-to-grips-with-ebpf--1760>

我出于多种目的使用 Linux 已经有相当长的历史了。在与团队在 [Fontis](https://www.fontis.com.au/) 一起工作时，被指派将 Linux 作为一台开发机器之后，好奇心和迫切需要修复这台开发机器的需求相结合，这是好奇心驱动的棒戳术的结果，这意味着我相当快地学习了大量的 Linux 琐事知识。在帮助建立[现场](https://www.sitewards.com/)基础设施工具的同时，我在此基础上做了进一步的构建；一组更加异构的计算机和提供商，但是有一个由 Docker 和 Kubernetes 构建的标准方法。

这种经历的总和意味着我有很大的动力去投资更多与 Linux 相关的技术。在本课程中，我逐渐意识到的一项更有趣的技术是“扩展的伯克利数据包过滤器”，或“eBPF”。

Brendan Greggs 通过 eBPF 的[性能分析精彩视频向我介绍了这项技术。这在实验上有些用处，但是需要最新的内核和各种其他奇怪的东西，这些东西在我们的基础设施中并不一致。然而，同时也有一些有趣的讨论](https://www.youtube.com/watch?v=bj3qdEDbCD4)[关于另一个 eBPF 项目——纤毛](https://twitter.com/jessfraz/status/897819764915142656)。这个项目为 Kubernetes 提供了底层网络，但是它似乎提供了其他网络插件所没有的额外的安全性和可见性；天真地[类似于 Istio](https://istio.io/) 。

最近，我有机会帮助另一个团队解决大型定制 Kubernetes 集群的一些扩展问题。这个集群有大量的服务，这些服务由于其 calico & kube-proxy `iptables`实现的性能问题而更新缓慢。这个特殊的问题用另一种方式解决了，但是导致了对 Calico 和随后的 eBPF 网络工具的研究。

# 什么是 BPF？

最初的“Berkeley 包过滤器”源于 Steve McCanne 和 Van Jacobson 在 1992 年为 Berkeley 软件发行版写的一篇论文。它的目的是通过编译一个过滤掉不应该被复制的包的程序，允许从内核到用户区域的包的有效捕获。这随后被用于`tcpdump`等公用事业。

2011 年，Eric Dumazet 通过添加一个即时(JIT)编译器，将 BPF 字节码编译成一个优化的指令序列，大大提高了这个 BPF 滤波器的性能。后来，在 2014 年，Alexi Starovoitov 利用这种高性能虚拟机，以比其他方式更有效地公开内核跟踪信息，并将 BPF 扩展到最初的包过滤目的之外。Jonathan Corbet 注意到了这一点，并将其发表在 LWN 网络上，暗示最终 BPF 程序不仅可以在内核内部使用，还可以在用户区域编译并加载到内核中。同年晚些时候，Alexi 开始研究`bpf()` syscall，eBPF 的当前概念被抛弃。

eBPF 现在是 BPF 工具的一个扩展，被转换成一个更通用的虚拟机，用于远远超出其最初包过滤目的的角色。这是一个历史的怪癖，它仍然被称为伯克利分组过滤器，但这个名字现在已经卡住了。

因为 eBPF 是原始规范的扩展，所以通常简称为 BPF。旧的语言在编译之前在内核中被转换到新的 eBPF，所以内核中唯一的 BPF 是新的 eBPF。

# BPF 是如何工作的？

BPF 是 64 位指令序列。这些指令一般由中介生成，比如`tcpdump` (libpcap):

```
# See https://blog.cloudflare.com/bpf-the-forgotten-bytecode/
$ sudo tcpdump -i wlp2s0 'ip and tcp' -d
(000) ldh      [12]                           # Load a half-word (2 bytes) from the packet at offset 12.
(001) jeq      #0x800           jt 2    jf 5  # Check if the value is 0x0800, otherwise fail.
                                              # This checks for the IP packet on top of an Ethernet frame.
(002) ldb      [23]                           # Load byte from a packet at offset 23.
                                              # That's the "protocol" field 9 bytes within an IP frame.
(003) jeq      #0x6             jt 4    jf 5  # Check if the value is 0x6, which is the TCP protocol number,
                                              # otherwise fail.
(004) ret      #262144                        # Return fail
(005) ret      #0                             # Return success 
```

但也可以写入 C 的[有限子集并编译。](https://github.com/iovisor/bcc/blob/master/docs/reference_guide.md#bpf-c)

BPF 程序有一组由内核验证器强制执行的保证，使得 BPF 程序在内核中安全运行，没有锁定或破坏内核的风险。验证者确保:

*   程序不循环
*   没有不可及的指令
*   每个寄存器和堆栈状态都是有效的
*   不读取包含未初始化内容的寄存器
*   该程序仅访问适合其 BPF 程序类型的结构
*   (可选)禁止指针运算

BCC 工具库包含一组包装 BPF 程序的工具，可以做一些有用的事情。我们可以使用这些程序中的一个(`dns_matching.py`)来演示`BPF`是如何装备网络的:

```
# Clone the repository

$ git clone https://github.com/iovisor/bcc.git
Cloning into 'bcc'...
Receiving objects: 100% (17648/17648), 8.42 MiB | 1.21 MiB/s, done.
Resolving deltas: 100% (11460/11460), done.

# Pick the DNS matching
$ cd bcc/examples/networking/dns_matching

# Run it!
$ sudo ./dns_matching.py  --domains fishfingers.io

$ sudo ./dns_matching.py  --domains fishfingers.io
>>>> Adding map entry:  fishfingers.io

Try to lookup some domain names using nslookup from another terminal.
For example:  nslookup foo.bar

BPF program will filter-in DNS packets which match with map entries.
Packets received by user space program will be printed here

Hit Ctrl+C to end... 
```

在另一个窗口中，我们可以运行:

```
$ dig fishfingers.io 
```

这将显示在我们的第一个窗口中:

```
Hit Ctrl+C to end...

[<DNS Question: 'fishfingers.io.' qtype=A qclass=IN>] 
```

领域是无意义的，但问题仍然存在。查看[源文件](https://github.com/iovisor/bcc/blob/master/examples/networking/dns_matching/dns_matching.c)我们可以看到用 C 编写的 eBPF 程序:

1.  检查以太网帧的类型
2.  检查它的 UDP
3.  检查其端口 53 是否
4.  检查提供的 DNS 名称是否在有效负载中

就是这样！我们的 eBPF 程序已经成功地在内核中运行，并将数据包复制到 userland python 程序中，随后保存在那里。

虽然这个例子与网络内核系统(BPF 程序类型套接字过滤器)相关联，但是有一系列内核入口点可以执行这些 eBPF 程序。当时一共有 22 种程序类型；不幸的是，他们目前很少记录。

# 野外 eBPF

为了理解 eBPF 在基础设施生态系统中的位置，有必要看看其他公司选择使用它而不是其他更传统的方法来解决问题。

## 防火墙

Linux 防火墙的实际实现使用`iptables`作为其底层实施机制。`iptables`允许配置一组以多种方式处理数据包的 netfilter 表。例如，以下规则会丢弃来自 IP 地址 10.10.10.10 的所有连接:

```
iptables -A INPUT -s 10.10.10.10/32 -j DROP 
```

`iptables`可用于多种数据包处理任务，如网络地址转换(NAT)或数据包转发。然而`iptables`遇到了几个重大问题:

1.  `iptables` [规则按顺序匹配](https://cilium.io/blog/2018/11/20/fb-bpf-firewall/)
2.  `iptables`更新必须通过[在单个交易中重新创建和更新所有规则](https://www.youtube.com/watch?v=4-pawkiazEg)来完成

这两个属性意味着，在大规模、多样化的流量条件下(例如任何足够大的服务—脸书所经历的情况)，或者在对`iptables`规则进行大量更改的系统中，运行`iptables`将会产生不可接受的性能开销，这可能会使整个服务降级或离线。

通过`nftables`，Linux 内核已经对这个子系统进行了改进。该系统旨在改进`iptables`，在架构上类似于 BPF，因为它在内核中实现了一个虚拟机。`nftables`稍老一些，在现有的 Linux 发行版中得到更好的支持，在测试发行版中甚至已经开始完全取代`iptables`。然而，随着 BPF 的出现和优化，`nftables`也许是一项不太值得投资的技术。

那就只剩下 BPF 了。与`iptables`相比，BPF 有几个独特的优势:

*   它是作为虚拟机中的指令集实现的，并且可以进行大量优化
*   它[与“最接近的”规则](https://cilium.io/blog/2018/11/20/fb-bpf-firewall/)匹配，而不是迭代整个规则集。
*   当决定是否丢弃时，它可以自省特定的分组数据
*   可以在 Linux“Express Data Path”(或 XDP)中编译运行；与网络流量交互的最早可能点

这些优势可以产生一些惊人的性能优势。在 CloudFlare 的(人工)测试中，使用 XDP 的 BPF 在丢包方面比次佳解决方案(tc)大约好[5 倍。脸书发现](https://blog.cloudflare.com/how-to-drop-10-million-packets/)[使用 BPF 过滤后，CPU 使用率](https://cilium.io/blog/2018/11/20/fb-bpf-firewall/)更容易预测。

除了性能优势之外，一些应用程序结合使用 BPF 和用户代理(如 Envoy)来[允许或拒绝应用程序协议 HTTP、gRPC、DNS 或 Kafka](http://docs.cilium.io/en/stable/policy/language/#layer-7-examples) 。这种特定于应用程序的过滤只能在服务网格中看到，如 Istio 或 Linkerd，它们比基于 BPF 的解决方案带来更多的性能损失。

因此，基于 BPF 的包过滤比现有的`iptables`
解决方案更灵活、更高效(使用 XDP)。虽然`tc`和`nftables`现在或将来可能会提供相似的性能，但是 BPFs 结合了大量的用例以及效率，这意味着它可能是一个更好的投资场所。

## 内核追踪&仪器仪表

在生产环境中运行 Linux 一段时间后，我们总是会遇到问题。过去，我在调试时遇到过问题:

*   `iptables`性能问题
*   工作负载 CPU 性能
*   软件未加载配置
*   软件停止运行
*   系统莫名其妙地“变慢”

在这种情况下，我们需要进一步挖掘内核和用户之间发生了什么，并探究系统为什么会这样。

这项任务有大量的工具。Brendan Gregg 有一张[出色的图片，展示了许多工具以及它们对](http://www.brendangregg.com/Perf/linux_perf_tools_full.svg)的用处。从上面的列表中，我熟悉:

*   `strace` / `ltrace`
*   `top`
*   `sysdig`
*   `iotop`
*   `df`
*   `perf`

这些工具都有自己独特的权衡，对它们进行深入分析超出了本文的范围。然而，最有用的工具也许是`strace`。`strace`提供进程正在使用的系统调用(对 Linux 内核的调用)的可见性。下面的例子显示了`cat /tmp/foo`将调用的文件系统进程:

```
$ strace -e file cat /tmp/foo
execve("/bin/cat", ["cat", "/tmp/foo"], 0x7fffc2c8c308 /* 56 vars */) = 0
access("/etc/ld.so.preload", R_OK)      = 0
openat(AT_FDCWD, "/etc/ld.so.preload", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libsnoopy.so", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libpthread.so.0", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libdl.so.2", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/tmp/foo", O_RDONLY)  = 3
hi 
```

这允许我们调试一系列问题，包括配置不工作、进程通过网络发送什么、进程接收什么以及给定进程产生什么进程。然而，这是有代价的——`strace`将会[显著减慢这个过程](http://www.brendangregg.com/blog/2014-05-11/strace-wow-much-syscall.html)。突然在系统中引入大的延迟将会惹恼用户，并且可能阻塞和堆积请求，最终中断服务。因此，需要谨慎使用。

然而，跟踪这些系统调用更有效的方法是使用 BPF。有了[`bcc`工具 git 库](https://github.com/iovisor/bcc)，这变得很容易；具体来说就是`trace.py`工具。该工具的界面与`strace`略有不同；也许是因为 BPF 是基于内核中的事件编译和执行的，而不是中断内核接口的进程。但是，它可以复制如下:

```
 $ sudo ./trace.py 'do_sys_open "%s", arg2' | grep 'cat' 
```

然后在另一个窗口中:

```
$ cat /tmp/foo 
```

会屈服

```
13785   13785   cat do_sys_open      /etc/ld.so.preload
13785   13785   cat do_sys_open      /lib/x86_64-linux-gnu/libsnoopy.so
13785   13785   cat do_sys_open      /etc/ld.so.cache
13785   13785   cat do_sys_open      /lib/x86_64-linux-gnu/libc.so.6
13785   13785   cat do_sys_open      /lib/x86_64-linux-gnu/libpthread.so.0
13785   13785   cat do_sys_open      /lib/x86_64-linux-gnu/libdl.so.2
13785   13785   cat do_sys_open      /usr/lib/locale/locale-archive
13785   13785   cat do_sys_open      /tmp/foo 
```

这相当准确地复制了`strace`的功能；前面列出的每个文件都显示在`trace.py`输出中，就像它们在`strace`输出中一样。

BPF 不局限于`strace`喜欢工具。它可以用来反省一系列用户和内核级别的问题，并被打包成 BCC 库中的用户友好工具[。此外，BPF](https://github.com/iovisor/bcc) [现在为 Sysdig](https://sysdig.com/blog/sysdig-and-falco-now-powered-by-ebpf/) 提供支持，这是一个用于深入研究机器，通过分析系统调用来确定其行为的工具。甚至有些工作是将 BPF 程序的结果以普罗米修斯格式导出，用于时间序列数据的聚合。

由于其高性能、灵活性和对最新 Linux 内核的良好支持，BPF 形成了一套新的系统自检工具的基础，这些工具能够提供更灵活、高性能的系统自检。此外，BPF 似乎比内核黑客更简单，否则需要提供这种系统自省，并可能使这种工具的设计民主化，导致该领域的更多创新。

## 网络可见性

鉴于 BPF 在包过滤方面的历史，合理的下一步是从网络中收集统计数据以供以后分析。

已经有许多网络统计数据通过`/proc`子目录公开，可以用很少的开销读取。[普罗米修斯的“节点出口者”](https://github.com/prometheus/node_exporter)写道:

*   `/proc/sys/net/netfilter/`
*   `/proc/net/ip_vs`
*   `/proc/net/ip_vs_stats`
*   `/sys/class/net/`
*   `/proc/net/netstat`
*   `/proc/net/sockstat`
*   `/proc/net/tcp`
*   `/proc/net/tcp6`

然而，尽管如此，仍然有一些关于连接的东西不能直接从`/proc`中读取，也不能通过 CLI 工具集读取(`ss`、`netstat`等)。Julia Evans 和 Brendan Gregg 在 Twitter 上讨论了这样一个案例:某个给定端口上 TCP 连接长度的统计。

这对于调试系统连接到什么，以及它在那个连接上花费了多长时间非常有用。反过来，我们可以用它来确定我们的机器在和谁说话，以及它是否在任何给定的连接上卡住了。

Brendan Gregg 有一个帖子[详细描述了这是如何实现的](http://www.brendangregg.com/blog/2016-11-30/linux-bcc-tcplife.html)，但概括来说，它监听`tcp_set_state()`并查询来自`struct tcp_info`的连接的属性。这种方法有各种各样的限制，但是看起来效果很好。

结果已提交到密件抄送存储库，看起来如下:

```
# Trace remote port 443
$ sudo ./tcplife.py -D 443 
```

然后，在另一个窗口中:

```
$ curl https://www.andrewhowden.com/ > /dev/null 
```

第一个窗口显示:

```
PID   COMM       LADDR           LPORT RADDR           RPORT TX_KB RX_KB MS
7362  curl       10.1.1.247      43074 34.76.108.124   443       0    16 3369.32 
```

表示一个 ID 为 7362 的进程通过端口 443 连接到 34.76.108.124，用了 3369.32 毫秒完成其传输(澳大利亚的互联网在某些地区有点慢)。

这些特殊的调试统计数据基本上不可能通过其他方式收集。此外，应该能够(如果需要的话)以这样一种方式表达这些统计信息，即 Prometheus exporter 将加载它们并导出它们以供收集，从而使网络基本上可以任意地进行自省。

# 利用 BPF

鉴于上述情况，BPF 似乎是一项引人注目的技术，值得投资了解更多。然而，让 BPF 正常工作有一些困难:

## BPF 只在“最近的”仁

在 Linux 内核中，BPF 是一个发展迅速的地区。因此，特征可能不完整，或者可能根本不存在。工具可能无法按预期工作，并且它们的故障情况没有得到很好的记录。因此，如果生产中使用的内核相当现代，那么 BPF 可以提供相当大的效用。如果没有，也许值得等待，直到这个领域的发展放缓，一个具有良好 BPF 兼容性的 LTS 内核发布。

## 很难调试

BPF 目前相当不透明。虽然到处都有一些文档，人们可以去阅读内核源代码，但它不像(例如)`iptables`或其他系统工具那样容易调试。可能很难调试由不正确构造的`bpf`程序产生的网络问题。这里的建议与其他新的或定制的技术相同:确保多个团队成员理解并能调试它，如果他们不能或那些人不可用，选择另一种技术。

## 这是一个实现细节

我怀疑我们与 BPF 的绝大多数互动不会是我们设计的互动。BPF 在分析工具的设计中很有用，但是这个负担对于系统管理员来说可能太重了。因此，开始收获 BPF 的好处，而不是投资于使用这项技术的工具是值得的。其中包括:

*   纤毛
*   密件抄送工具
*   `bpftrace`
*   Sysdig

将来会有更多的工具出现，尽管这是我目前唯一会投资的工具。

# 结论

BPF 是一项古老的技术，随着扩展指令集、JIT 的实现以及在 Linux 内核的不同点执行 BPF 的能力的出现，它被赋予了新的生命。它提供了一种方法，可以在运行时导出关于或修改 Linux 内核行为的信息，而不需要重启或重新加载内核，包括只是为了短暂的系统自检。BPF 可能对网络性能有最直接的影响，因为网络需要处理真正奇怪的流量和复杂性，BPF 为这些问题提供了一些具体的解决方案。因此，在网络的背景下理解 BPF 是一个好的开始，尤其是在投资于`nftables`或`iptables`的情况下。此外，BPF 还对系统和网络可见性提供了一些令人信服的见解，否则很难或不可能实现，尽管这一领域比网络实现更具新生性。

BPF 博士非常酷。

## 参考文献

*   [IOVisor 项目:一堆好的 eBPF 和 XDP 读物和工具](https://www.iovisor.org/)
*   [API 感知网络和安全，由 eBPF 和 XDP 提供支持](https://cilium.io/)
*   [为什么内核社区用 eBPF 代替 IPTables】](https://cilium.io/blog/2018/04/17/why-is-the-kernel-community-replacing-iptables/)
*   [通过 XDP 实现高性能低延迟网络](https://developers.redhat.com/blog/2018/12/06/achieving-high-performance-low-latency-networking-with-xdp-part-1/)
*   [脸书 eBPF 防火墙内部](https://cilium.io/blog/2018/11/20/fb-bpf-firewall/)
*   [纤毛结构](https://docs.cilium.io/en/v1.4/architecture/)
*   [XDP 和 eBPF 简介](https://blogs.igalia.com/dpino/2019/01/07/a-brief-introduction-to-xdp-and-ebpf/)
*   [eBPF:过去、现在和未来](https://ferrisellis.com/posts/ebpf_past_present_future/)
*   [争论 XDP 的价值](https://lwn.net/Articles/708087/)
*   [使用 eBPF 进行网络调试](https://developers.redhat.com/blog/2018/12/03/network-debugging-with-ebpf/)
*   [包过滤器的 JIT](https://lwn.net/Articles/437981/)
*   [NFTables:一个新的包过滤引擎](https://lwn.net/Articles/324989/)
*   [eBPF 和 XDP:参考指南](https://docs.cilium.io/en/v1.4/bpf/)
*   [如何在 XDP 支持下构建内核](https://blogs.igalia.com/dpino/2019/01/02/build-a-kernel/)
*   [Cilium:重新思考微服务时代的 Linux 网络和安全](https://cilium.io/blog/2018/04/24/cilium-security-for-age-of-microservices/)
*   [Cilium 1.4 发行说明](https://cilium.io/blog/2019/02/12/cilium-14/)
*   [网络快速通道的新方法](https://lwn.net/Articles/719850/)
*   [eBPF 的全面介绍](https://lwn.net/Articles/740157/)
*   [Sysdig:现在由 eBPF 提供支持](https://sysdig.com/blog/sysdig-and-falco-now-powered-by-ebpf/)
*   [Linux eBPF 超能力](http://www.brendangregg.com/blog/2016-03-05/linux-bpf-superpowers.html)
*   [BPF:通用内核虚拟机](https://lwn.net/Articles/437981/)
*   BSD 数据包过滤器:用户级数据包捕获的新架构
*   [非官方 eBPF 规范](https://github.com/iovisor/bpf-docs/blob/master/eBPF.md)
*   [Linux Socket Filtering 又名 Berkeley Packet Filter (BPF)](https://www.kernel.org/doc/Documentation/networking/filter.txt)
*   BPF:被遗忘的字节码
*   [TC BPF 手册页](http://man7.org/linux/man-pages/man8/tc-bpf.8.html)
*   [L4Drop: XDP DDoS 缓解](https://blog.cloudflare.com/l4drop-xdp-ebpf-based-ddos-mitigations/)
*   [iptables 和 Linux 防火墙初学者指南](https://www.howtogeek.com/177621/the-beginners-guide-to-iptables-the-linux-firewall/)
*   [维基百科:nfabis](https://en.wikipedia.org/wiki/Nftables)
*   [如何丢弃 1000 万个数据包](https://blog.cloudflare.com/how-to-drop-10-million-packets/)
*   [介绍 p0f 编译器](https://blog.cloudflare.com/introducing-the-p0f-bpf-compiler/)
*   [L4Drop: XDP DDoS 缓解措施](https://blog.cloudflare.com/l4drop-xdp-ebpf-based-ddos-mitigations/)