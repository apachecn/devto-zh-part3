# EnqueueZero Techshack 2019-01

> 原文：<https://dev.to/soasme/enqueuezero-techshack-2019-01-50op>

# enqueue zero Techshack 2019-01

原帖:[https://enqueuezero.com/techshack.weekly/2019-01.html](https://enqueuezero.com/techshack.weekly/2019-01.html)

起亚奥拉！我是**零入队**(【https://enqueuezero.com】T2)的创建者，这是一个向开发者/开发者/系统管理员解释代码原理的网站。本网站自 2018 年起在单人团队下更新。“EnqueueZero Techshack”系列由我写的或在互联网上找到的一堆有趣的帖子组成。更重要的是，我希望这些帖子能让你满意。

为了保持我的动力和这个网站的无广告，感谢您的捐赠！

[![Donate this project using Patreon](img/d8ef9eb3c1d7813b01d7070f13d6bc49.png)](https://www.patreon.com/join/enqueuezero)

喜欢读本周的帖子。

## 概括地说

[enqueuezero.com](https://enqueuezero.com/kubernetes-in-a-nutshell.html)

Kubernetes 是一个运行和协调容器化应用的系统。这些应用程序部署在一个机器集群上。

作为 Kubernetes 用户，您可以定义应用程序应该如何运行。您还可以定义应用程序应该如何与其他应用程序或外部世界进行交互。

Kubernetes 使用共享网络将机器服务器聚集到一个集群中。

*   主服务器控制整个集群。
*   **节点**是集群中的其他服务器。

## 不和谐如何存储数十亿条信息

[blog.discordapp.com](https://blog.discordapp.com/how-discord-stores-billions-of-messages-7fa6ec7ee4c7)

底层数据库是 Cassandra。选择 Cassandra 的原因是:

*   读/写比率大约是 50/50。
*   许多随机读取。
*   大型数据集。
*   私人文本聊天大量不和谐服务器和大型公共不和谐服务器的数据量不均衡。

要求是:

*   线性可扩展性。
*   自动故障转移。
*   低维护。
*   证明有效。
*   可预测的性能。
*   不是 blob 存储区。
*   开源。

数据建模是按时间存储消息。`Message`的主键是`((channel_id, bucket), message_id)`。

```
CREATE TABLE messages (
   channel_id bigint,
   bucket int,
   message_id bigint,
   author_id bigint,
   content text,
   PRIMARY KEY ((channel_id, bucket), message_id)
) WITH CLUSTERING ORDER BY (message_id DESC); 
```

Enter fullscreen mode Exit fullscreen mode

Cassandra 是一个 AP 数据库，这意味着它以一致性和可用性为代价。为了解决这个难题，工程师们决定，如果检测到故障，就删除数据库中的记录。删除操作是通过一种称为“逻辑删除”的写入形式来执行的在读取时，它只是跳过它遇到的墓碑。默认情况下，tombstone 的保留策略是两天。

## 关于 AWS 网络，你需要知道的一切

[开发到](https://dev.to/grahamlyons/everything-you-need-to-know-about-networking-on-aws-4bkf)

```
 +-------+
                                        | ig-1  |
                                        |       |
        vpc-123: 10.0.0.0/16  |         |       |        |
       +----------------------+---------+-------+--------+---------------------+
       |                      |                          |                     |
       |  +-----+             |  +-----+                 |  +-----+            |
       |  | NAT |             |  | NAT |                 |  | NAT |            |
public |  |     |             |  |     |                 |  |     |            |
subnets|  +-----+             |  +-----+                 |  +-----+            |
       |                      |                          |                     |
       |                      |                          |                     |
       |                      |                          |                     |
       |              +-------+                  +-------+             +-------+
       |              | rt-1a |                  | rt-1b |             | rt-1c |
       | 10.0.1.0/24  |       | 10.0.2.0/24      |       | 10.0.3.0/24 |       |
------------+-----------------------------------------------------------------------+
       | 10.0.4.0/24  | rt-2a | 10.0.5.0/24      | rt-2b | 10.0.6.0/24 | rt-2c |
       |              |       |                  |       |             |       |
       |              +-------+                  +-------+             +-------+
private|                      |                          |                     |
subnets|                      |                          |                     |
       |                      |                          |                     |
       |                      |                          |                     |
       |                      |                          |                     |
       |                      |                          |                     |
       |                      |                          |                     |
       |                      |                          |                     |
       +----------------------+--------------------------+---------------------+
       |         AZ 1         |          AZ 2            |        AZ 3         | 
```

Enter fullscreen mode Exit fullscreen mode

*   虚拟私有云或 VPC 是一个私有网络空间，您可以在其中运行基础架构。
    *   它有一个您可以选择的地址空间(CIDR 范围),例如 10.0.0.0/16。它决定了您可以拥有多少个 IP 地址。
*   在 VPC，根据流量是否能从 VPC(互联网)外部到达子网，子网分为公共子网和私有子网。
*   只要一个可用性区域或 AZ 存在，您的服务就应该能够运行。
*   路由表包含有关子网中的 IP 数据包如何到达不同 IP 地址的规则。
*   互联网网关允许与外界通信。
*   NAT 网关是一种位于公共子网中的设备，它接受来自私有子网的发往 Internet 的任何 IP 数据包，将这些数据包发送到它们的目的地，然后将返回的数据包发送回源。
*   安全组可以指定入口(入站)和出口(出站)流量规则，将它们限制在某些来源(入站)和目的地(出站)。

## 支持向量机白痴指南

[web.mit.edu](http://web.mit.edu/6.034/wwwbob/svm-notes-long-08.pdf)

*   支持向量是最接近决策面的数据点。它们是最难分类的数据点。
*   ![Support Vector Machine](img/c8342618fcb92ba45cfcbe1a3e1a5cb6.png)

## 如何用漫画记录一个科技项目？

[jvns.ca](https://jvns.ca/blog/2018/12/09/how-do-you-document-a-tech-project-with-comics/)

*   没有好信息的漫画是没有用的。
*   关注那些不会改变的概念。
*   用很少的信息制作一个图形。
*   制作更有深度的漫画/插图

## 高可靠性基础设施迁移

[youtube.com](https://www.youtube.com/watch?v=obB2IvCv-K0)

主题是关于如何将可用性从 99%提高到 99.99%。

以下是解决方案。

*   理解设计。
*   运行游戏日。
*   对故障进行分类。
*   只有一次事故。
*   进行渐进式的改变。
*   始终有一个回滚计划。

开始不是专家没关系，但是你需要成为专家！

## 将管道引入 Airbnb 的部署流程

[medium.com](https://medium.com/airbnb-engineering/introducing-deploy-pipelines-to-airbnb-fc804ac2a157)

查看这篇关于 Airbnb 如何设计部署流程并最大限度地减少潜在滥用的帖子。

*   为服务所有者可以用来定义其部署过程(即管道)的配置文件制定规范。
*   将管道配置保存在数据库中。
*   给前端一个访问这些数据的方法。
*   在 UI 显示中正确分离和排列目标。
*   禁用不应部署到的目标(即，它具有尚未部署到的依赖项)。
*   最后，实现 UI。

## 学习 eBPF 追踪:教程和示例

[www.brendangregg.com](http://www.brendangregg.com/blog/2019-01-01/learn-ebpf-tracing.html)|[bcc](https://github.com/iovisor/bcc)|[bpftrace](https://github.com/iovisor/bpftrace)|[bcc Python 指南](https://github.com/iovisor/bcc/blob/master/docs/tutorial_bcc_python_developer.md)

查看 Brendan Gregg 关于 eBPF 初学者指南的新博客文章。

*   eBPF 是什么？eBPF 对 Linux 的作用就像 JavaScript V8 对 HTML 的作用一样。通过利用 eBPF，您可以编写运行在磁盘 I/O 等事件上的迷你程序，这些程序运行在内核中的安全虚拟机上。
*   什么是 bcc 和 bpftrace？由于没有人编写 V8 字节码，所以没有多少人直接编写 eBPF 程序。相反，人们编写 bcc 和 bpftrace。
*   默认情况下，包括网飞和脸书在内的公司在所有服务器上都安装了密件抄送，也许您也想这样做。
*   查看 bcc [初学者教程](https://github.com/iovisor/bcc/blob/master/docs/tutorial.md)。![Many more tracing tools exist](img/76ed2f190540ddb84540f33c58c5cb06.png)
*   对于中级游戏玩家，可以从 bcc 转到 bpftrace，bpf trace 有高级语言，学起来容易很多。
*   bpftrace 一行程序的示例:

```
$ bpftrace -e 'tracepoint:syscalls:sys_enter_open { printf("%d %s\n", pid, str(args->filename)); }' 
```

Enter fullscreen mode Exit fullscreen mode

## 内部坞站

[docker-saigon.github.io](http://docker-saigon.github.io/post/Docker-Internals/)

对 Docker 的深入探究，让工程师们对具体细节感兴趣。

*   什么是容器？
    *   容器共享主机内核
    *   容器使用内核能力来分组进程以进行资源控制
    *   容器通过名称空间确保隔离
    *   容器感觉像轻量级虚拟机(占用空间更少，速度更快)，但不是虚拟机！
*   容器与包管理器相比如何？
    *   由于共享库版本差异导致依赖性问题，包管理器令我们失望；将共享库打包到映像中可以解决这个问题。
*   容器与配置管理相比如何？
    *   仍然建议**利用这样的供应工具来引导 Docker 基础设施**，一旦准备好，就让容器运行时层来处理应用层。
*   为什么是 Docker？
    *   因为 Docker 是目前唯一提供映像管理、资源隔离、文件系统隔离、网络隔离、变更管理、共享、流程管理、服务发现(DNS 自 1.10 起)全套服务的生态系统
*   怎么会？内核名称空间。CGroups，iptables，Copy-On-Write UnionFS。
*   容器运行时:LXC、Systemd-nspawn、runC
*   如何挂接各种 Docker 组件？
    *   [坞站引擎 API](https://docs.docker.com/develop/sdk/)
    *   webhooks:坞站注册表
    *   stdout(组合坞站)
    *   gPRC:[https://containerd.tools/](https://containerd.tools/):将 Docker 守护进程扩展成一个更先进的符合 OCI 标准的守护进程来控制 runC。
    *   OCI

## 万物收拢

[EC . haxx . se](https://ec.haxx.se/)|【github.com】T2

毫无疑问，curl 是一个伟大的软件。curl 的作者出版了一本书《curl 的一切》，值得一读。

*   可以学习[代码布局](https://ec.haxx.se/sourcecode-layout.html) : root，src，lib，docs 等。
*   它使用了传统的 C 代码风格:[https://ec.haxx.se/sourcecode-style.html](https://ec.haxx.se/sourcecode-style.html)，制表符上有两个空格，/* comment */，如果超过 80 个字符，则换行，在同一行打开大括号，否则在下一行，括号前没有空格。
*   [网络简化](https://ec.haxx.se/protocols-network.html)。基本概念是机器、URL、DNS、TCP 连接、IP &端口、TLS、传输协议。
*   这个世界充满了新老协议。最新的 curl 支持 DICT，FILE，FTP，FTPS，GOPHER，HTTP，HTTPS，IMAP，IMAPS，LDAP，LDAPS，POP3，POP3S，RTMP，RTSP，SCP，SFTP，SMB，SMBS，SMTP，SMTPS，TELNET，TFTP。
    *   像软件一样，协议规范经常被更新，并且新的协议版本被创建。
    *   协议来来去去。IETF 和 RFC 使其稳定。
*   用法:`curl -v [https://enqueuezero.com](https://enqueuezero.com)`。
*   如何用 curl 进行 HTTP？[https://ec.haxx.se/http.html](https://ec.haxx.se/http.html)。
*   如果使用 curl 作为 C 库而不是命令行实用程序，请尝试`libcurl`。默认简单，更多点播！https://ec.haxx.se/libcurl.htmlT2
    *   我敢肯定，除了 c,[https://ec.haxx.se/bindings.html](https://ec.haxx.se/bindings.html)之外，你还能在其他编程语言上找到 curl 绑定

## GraphQL 服务器设计@中型

[medium.com](https://medium.engineering/graphql-server-design-medium-34862677b4b8)

本文解释了 Medium 的 GraphQL 服务器的结构如何帮助迁移变得更加顺利。

做出了一些决定:

*   通过协议缓冲区管理数据交换协议。要了解 protobuf 的概况，请查看这一部分:[https://enqueuezero.com/data-serialization.html#protobuf](https://enqueuezero.com/data-serialization.html#protobuf)。
*   明确什么数据是给客户的。
*   随着更多 SOA 服务的到来，支持多个后端源。

他们提出了一个分层架构。正如在这篇文章[https://enqueuezero.com/layered-architecture.html](https://enqueuezero.com/layered-architecture.html)中所讨论的，“每一层都有自己的职责，并且只与它下面的一层交互”。下面是建议的图层:

*   模式。这是数据发送到客户端时将采用的形式。
*   仓库。它“存储”最初来自数据源的清理过的数据。传统上，它就像 MVC 中的控制器层，对数据进行按摩和整形。
*   羊倌。它由 GraphQL 服务器获取，用于从多个数据源获取数据

[![GraphQL server](img/b58e43adaa392c665cfdba9b69e0cfa6.png)](/statimg/2019-01-medium-graphql-server.png)