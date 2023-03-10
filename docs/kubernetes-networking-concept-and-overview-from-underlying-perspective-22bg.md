# Kubernetes 网络:基本观点的概念和概述

> 原文：<https://dev.to/dannypsnl/kubernetes-networking-concept-and-overview-from-underlying-perspective-22bg>

Kubernetes 是为了在一个节点集群上运行分布式系统而构建的。了解 kubernetes 网络的概念可以帮助你正确理解如何在 kubernetes 上运行、监控和排除应用程序的故障，更重要的是，你可以通过了解如何对它们进行比较来知道如何为自己选择合适的分布式系统。

为了理解它的网络配置，我们必须从容器和操作系统如何提供这些资源隔离开始。我们从 Linux 的这个概念开始，创建一个模拟环境来学习它如何像容器一样工作。现在，让我们开始吧！

### 1 网络命名空间

在我们开始之前，我的环境是`Ubuntu 18.04 LTS`，这里是内核信息:

```
$ uname -a
Linux test-linux 4.15.0-1032-gcp #34-Ubuntu SMP Wed May 8 13:02:46 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux 
```

#### 1.1 创建新的网络名称空间

```
# create network namespace net0
$ ip netns add net0
# create network namespace net1
$ ip netns add net1
# then check
$ ip netns list
net1
net0 (id: 0) 
```

现在我们有几个网络名称空间可以在上面发出进程，但是进程不能连接到其他网络是没有意义的。为了解决这个问题，我们必须为它们创建一个隧道，在 Linux 中，我们可以使用`veth pair`直接连接两个名称空间。

#### 1.2 创建 veth 对

```
# new veth pair
$ ip link add type veth
# assign veth0 to net0
$ ip link set veth0 netns net0
# assign veth1 to net1
$ ip link set veth1 netns net1
$ ip netns exec net0 ip link set veth0 up
# assign ip 10.0.1.2 to veth0, you can use `ip addr` to check it
$ ip netns exec net0 ip addr add 10.0.1.2/24 dev veth0
$ ip netns exec net1 ip link set veth1 up
# assign ip 10.0.1.3 to veth1
$ ip netns exec net1 ip addr add 10.0.1.3/24 dev veth1 
```

> 注意:重要的一点是`veth pair`不能单独存在，如果你去掉一个，另一个也会被去掉。

现在，`ping`的网络名称空间`net1`从`net0`变为

```
$ ip netns exec net0 ping 10.0.1.3 -c 3 
```

`tcpdump`从目标网络命名空间，当然，你应该在你`ping`它之前运行`tcpdump`。

```
$ ip netns exec net1 tcpdump -v -n -i veth1
tcpdump: listening on veth1, link-type EN10MB (Ethernet), capture size 262144 bytes
13:54:11.800223 IP6 (hlim 255, next-header ICMPv6 (58) payload length: 16) fe80::905d:ccff:fe4a:cd81 > ff02::2: [icmp6 sum ok] ICMP6, router solicitation, length 16
          source link-address option (1), length 8 (1): 92:5d:cc:4a:cd:81
13:54:12.400440 IP (tos 0x0, ttl 64, id 45855, offset 0, flags [DF], proto ICMP (1), length 84)
    10.0.1.2 > 10.0.1.3: ICMP echo request, id 1433, seq 1, length 64
13:54:12.400464 IP (tos 0x0, ttl 64, id 41348, offset 0, flags [none], proto ICMP (1), length 84)
    10.0.1.3 > 10.0.1.2: ICMP echo reply, id 1433, seq 1, length 64
13:54:13.464163 IP (tos 0x0, ttl 64, id 45912, offset 0, flags [DF], proto ICMP (1), length 84)
    10.0.1.2 > 10.0.1.3: ICMP echo request, id 1433, seq 2, length 64
13:54:13.464189 IP (tos 0x0, ttl 64, id 41712, offset 0, flags [none], proto ICMP (1), length 84)
    10.0.1.3 > 10.0.1.2: ICMP echo reply, id 1433, seq 2, length 64
13:54:14.488184 IP (tos 0x0, ttl 64, id 46671, offset 0, flags [DF], proto ICMP (1), length 84)
    10.0.1.2 > 10.0.1.3: ICMP echo request, id 1433, seq 3, length 64
13:54:14.488221 IP (tos 0x0, ttl 64, id 41738, offset 0, flags [none], proto ICMP (1), length 84)
    10.0.1.3 > 10.0.1.2: ICMP echo reply, id 1433, seq 3, length 64 
```

**HTTP** 也可以。

**HTTP** 服务器:

```
$ ip netns exec net1 python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 ...
# After you execute the following command here would show
10.0.1.2 - - [15/May/2019 13:55:41] "GET / HTTP/1.1" 200 - 
```

**HTTP** 客户端:

```
$ ip netns exec net0 curl 10.0.1.3:8000
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
Directory listing for /
</head>
<body>
<h1>Directory listing for /</h1>
<hr>
<ul>
<li><a href=".bash_history">.bash_history</a></li>
<li><a href=".bash_logout">.bash_logout</a></li>
<li><a href=".bashrc">.bashrc</a></li>
<li><a href=".config/">.config/</a></li>
<li><a href=".docker/">.docker/</a></li>
<li><a href=".profile">.profile</a></li>
<li><a href=".theia/">.theia/</a></li>
<li><a href="README-cloudshell.txt">README-cloudshell.txt@</a></li>
</ul>
<hr>
</body>
</html> 
```

虽然`veth pair`可以帮助你连接两个网络名称空间，但是，它不能与更多的。当我们在具有两个以上网络名称空间的环境中工作时，我们需要一种更强大的技术:桥。

#### 1.3 创建虚拟桥

```
# create bridge
$ ip link add br0 type bridge
$ ip link set dev br0 up
# create veth pair for net0, veth0 & veth1
$ ip link add type veth
# create veth pair for net1, veth2 & veth3
$ ip link add type veth
# set up veth pair of net0
$ ip link set dev veth0 netns net0
# You would find veth0 disappeared now by `ip link`
$ ip netns exec net0 ip link set dev veth0 name eth0
$ ip netns exec net0 ip addr add 10.0.1.2/24 dev eth0
$ ip netns exec net0 ip link set dev eth0 up
# bind veth pair of net0 to br0
$ ip link set dev veth1 master br0
$ ip link set dev veth1 up
# set up veth pair of net1
$ ip link set dev veth2 netns net1
$ ip netns exec net1 ip link set dev veth2 name eth0
$ ip netns exec net1 ip addr add 10.0.1.3/24 dev eth0
$ ip netns exec net1 ip link set dev eth0 up
# bind veth pair of net1 to br0
$ ip link set dev veth3 master br0
$ ip link set dev veth3 up 
```

现在，从`net0`ping`10.0.1.3`来检查我们的桥接网络。

```
$ ip netns exec net0 ping 10.0.1.3 -c 3
PING 10.0.1.3 (10.0.1.3) 56(84) bytes of data.
64 bytes from 10.0.1.3: icmp_seq=1 ttl=64 time=0.030 ms
64 bytes from 10.0.1.3: icmp_seq=2 ttl=64 time=0.059 ms
64 bytes from 10.0.1.3: icmp_seq=3 ttl=64 time=0.051 ms

--- 10.0.1.3 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2038ms
rtt min/avg/max/mdev = 0.030/0.046/0.059/0.014 ms 
```

`tcpdump`我们的桥:`br0`

```
$ tcpdump -v -n -i br0
tcpdump: listening on br0, link-type EN10MB (Ethernet), capture size 262144 bytes
12:43:39.619458 IP (tos 0x0, ttl 64, id 63269, offset 0, flags [DF], proto ICMP (1), length 84)
    10.0.1.2 > 10.0.1.3: ICMP echo request, id 3046, seq 1, length 64
12:43:39.619553 IP (tos 0x0, ttl 64, id 54235, offset 0, flags [none], proto ICMP (1), length 84)
    10.0.1.3 > 10.0.1.2: ICMP echo reply, id 3046, seq 1, length 64
12:43:40.635730 IP (tos 0x0, ttl 64, id 63459, offset 0, flags [DF], proto ICMP (1), length 84)
    10.0.1.2 > 10.0.1.3: ICMP echo request, id 3046, seq 2, length 64
12:43:40.635764 IP (tos 0x0, ttl 64, id 54318, offset 0, flags [none], proto ICMP (1), length 84)
    10.0.1.3 > 10.0.1.2: ICMP echo reply, id 3046, seq 2, length 64
12:43:41.659714 IP (tos 0x0, ttl 64, id 63548, offset 0, flags [DF], proto ICMP (1), length 84)
    10.0.1.2 > 10.0.1.3: ICMP echo request, id 3046, seq 3, length 64
12:43:41.659742 IP (tos 0x0, ttl 64, id 54462, offset 0, flags [none], proto ICMP (1), length 84)
    10.0.1.3 > 10.0.1.2: ICMP echo reply, id 3046, seq 3, length 64
12:43:44.859619 ARP, Ethernet (len 6), IPv4 (len 4), Request who-has 10.0.1.2 tell 10.0.1.3, length 28
12:43:44.859638 ARP, Ethernet (len 6), IPv4 (len 4), Request who-has 10.0.1.3 tell 10.0.1.2, length 28
12:43:44.859686 ARP, Ethernet (len 6), IPv4 (len 4), Reply 10.0.1.2 is-at 0a:e0:a1:07:b7:c9, length 28
12:43:44.859689 ARP, Ethernet (len 6), IPv4 (len 4), Reply 10.0.1.3 is-at d2:b6:de:2f:4e:f6, length 28 
```

正如您所想，`br0`将获得从`net0`到`net1`的流量，现在我们的拓扑结构如下:

##### 图 1.1

[![](img/70551a75ee20e5cc25b501250cff9484.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ug8JS1R1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dannypsnl.github.io/assets/img/kube-networking/bridge_mode_and_namespace.svg)

在`tcpdump`的输出的最后，我们可以看到一些 ARP 请求/回复，我们将在下一节讨论它。

要获取更多信息:

*   [wiki: linux 名称空间](https://en.wikipedia.org/wiki/Linux_namespaces)

### 2 ARP

ARP(地址解析协议)是一种用于发现链路层地址的通信协议，例如与给定的互联网层地址相关联的 MAC 地址。

> 注意:在 IPv6(互联网协议版本 6)中，ARP 的功能由 NDP(邻居发现协议)提供。

我们不会展示 ARP 的整个数据包布局，但会提到我们在案例中关心的部分。

工作过程是:

1.  向广播地址发送带有源 MAC、源 IP 和目标 IP 的 ARP 请求数据包
2.  机器认为它有这个目标 IP 会发送 ARP 回复包包含它的 MAC 地址
3.  发送 ARP 请求的机器会将 IP 和 MAC 的映射缓存到 ARP 缓存中，所以下次它不必再次发送 ARP 请求。

> 注意:其他端点会忽略不感兴趣的 ARP 请求

##### 图 2.1

[![](img/83da9c40d4096bc59857a536569fe058.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--locAkzQK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dannypsnl.github.io/assets/img/kube-networking/arp_request_and_reply.svg)

在前面的部分，我们可以看到双方发送 ARP 请求来获取另一个 IP 的信息。

要获取更多信息:

*   [RFC 826](https://tools.ietf.org/html/rfc826)
*   wiki: ARP

### 3 在这下面

**Pod** 是 kubernetes 的单位，所以最基本的联网就是如何从 PodA 连接到 PodB。
我们会得到两种情况:

1.  两个**吊舱**在同一个**节点**
2.  两个 **Pod** 在不同的**节点**

> **节点** can 是 kubernetes 集群拥有的虚拟机或机器。

在下面的讨论中我们都是基于[Kubenet](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet)T3】这个实现的。 **Kubelet** 需要设置`--network-plugin=kubenet`。

#### 3.1 豆荚在同一个节点

在这种情况下，它就像第一部分一样，pod 会连接到同一个网桥，在这种情况下称为:`cbr0`。

> Kubenet 创建一个名为`cbr0`的 Linux 桥，并为每个 pod 创建一个 veth 对，每个 veth 对的主机端都连接到`cbr0`。

既然我们已经讨论过这种方式，我们就不在这里多花时间了，有趣的部分是，
如何允许吊舱开启

##### 图 3.1 两个荚在同一节点

[![](img/52e523c4aafd08b8e2b7bf3993ee3025.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wP8PIF3H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dannypsnl.github.io/assets/img/kube-networking/pod_to_pod_at_the_same_node.svg)

#### 3.2 不同节点处的荚

当容器网络必须与容器的其他主机一起工作时，问题就显现出来了。在传统环境中，我们不关心由多个容器主机组成的集群。
在旧的网络模型中，我们认为任何要连接到外部世界的容器都必须使用主机端口。
但是在集群世界中，端口是有限的资源，这将导致我们不能很好地扩展集群！
这就是为什么我们有 [CNM](https://github.com/docker/libnetwork/blob/master/docs/design.md) 和 [CNI](https://github.com/containernetworking/cni/blob/master/SPEC.md) 这些型号。
我们不会讨论它们的细节，但会提到现实世界网络拓扑的可能方法。

在不同的**节点**的两个 **Pod** 将不能使用同一个网桥，这意味着我们不能直接让数据包在它们之间通过。

##### 图 3.2 节点概念

[![](img/f821933a884c93c8d4ef05f9b43d7744.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ghWPSbxd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dannypsnl.github.io/assets/img/kube-networking/concept_of_nodes.svg)

整个数据包流将会:

1.  PodA 发送 ARP
2.  ARP 将失败，然后网桥`cbr0`会将数据包从主机的默认路由`eth0`发送出去
3.  路由将数据包发送到**默认网关**
4.  **默认网关**通过 CIDR 发送数据包到正确的主机，例如`10.0.2.101`到`10.10.0.3`
5.  虚拟机拥有的 PodB CIDR 将判断此数据包是否应该发送给自己`cbr0`
6.  最后将数据包发送到 PodB

##### 配置带有默认网关的 3.3 CIDR 节点

[![](img/99a27c1d2329b4bfa07738e1c6131710.png)](/assets/img/kube-networking/pod_to_pod_at_different_node_via_default_gateway.svg)

### 4 号吊舱开始服役

我们展示了如何在**pod**和它们的 IP 地址之间路由流量。该模型工作良好，直到我们不得不扩大**吊舱**。为了使 **Kubernetes** 成为一个伟大的系统，我们需要有自动添加/删除资源的能力，这是 **Kubernetes** 的主要特点，现在问题来了，因为我们可以删除 **Pod** ，我们不能信任它的 IP，因为新的 **Pod** 大多不会获得相同的 IP。

为了解决这个问题， **Kubernetes** 提供了一个叫做**服务**的抽象。一个**服务**将是一组选择器和一组与集群 IP 映射的端口，这意味着它将通过选择器选择 **Pods** 作为其后端，并通过端口映射为它们进行负载平衡和转发数据包。所以无论 **Pods** 是如何被创建或删除的，**服务**都会找到那些标签与选择器匹配的 **Pods** ，我们只需要知道**服务**的 IP，而不是知道 **Pods** 的所有 IP。

现在，让我们来看看它是如何工作的。

#### 4.1 iptables 和 netfilter

**Kubernetes** 依靠`netfilter`——内置于 **Linux** 的网络框架。

要了解关于`netfilter`的更多信息，请查看:

*   [维基:网络过滤器](https://en.wikipedia.org/wiki/Netfilter)
*   [netfilter 项目](https://www.netfilter.org/)

`iptables`是基于`netfilter`的用户空间工具之一，它提供了一个基于表格的系统，用于定义操作和转换数据包的规则。在 **Kubernetes** ，`kube-proxy`控制器会通过观察 API 服务器的变化来配置`iptables`规则。该规则监控到**服务**的集群 IP 的流量，并从**pod**的 IP 中挑选一个 IP，然后通过将目标 IP 从集群 IP 更新为挑选的 IP，将流量转发到挑选的 IP。该规则将通过更改集群 IP、添加 **Pod** 、删除 **Pod** 来更新。这意味着已经在机器上完成了负载平衡，将定向到集群 IP 的流量转移到 **Pod** 的实际 IP。

##### 图 4.1 Pod 服务拓扑

[![](img/700da8bd91c3c5fdb7a2da4f7ef5cfc9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dALBvrEV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dannypsnl.github.io/assets/img/kube-networking/pod_to_service.svg)

在目的地 IP 被更新之后，网络模型将退回到 **Pod 到 Pod** 模型。

您可以通过以下方式了解更多关于`iptables`的信息:

*   wiki: iptables
*   男人:iptables

#### 4.2 负载均衡

现在我将创建一些`iptables`规则来模拟静态 IP 的**服务**。假设我们有三个 IP:`10.244.1.2`、`10.244.1.3`、`10.244.1.4`和一个集群 IP: `10.0.0.2`

##### 在集群 IP 后增加一个 IP

```
$ iptables \
  -t nat # nat table
  -A PREROUTING # Append to PREROUTING chain
  -p tcp # protocol TCP
  -d 10.0.0.2 # only for the packet to 10.0.0.2
  --dport 80 # only for port 80
  -j DNAT # DNAT target
  --to-destination 10.244.1.2:8080 # change destination to 10.244.1.2:8080 
```

不幸的是，我们不能只将这个命令应用到我们想要负载平衡的每个 IP 上，因为第一个规则会从其他人那里拿走所有的工作(但我们的工作不会，该死)。这就是为什么`iptables`提供了一个名为`statistic`的模块可以用两种不同的模式工作:

*   `random`:概率
*   `nth`:循环算法

> 注意:负载平衡只在 TCP 协议的连接阶段起作用。一旦建立了连接，该连接将被路由到同一个服务器。

我们在这里只介绍循环法，因为它很容易理解，而且我们想讨论负载平衡而不是负载平衡如何工作。

```
$ export CLUSTER_IP=10.0.0.2
$ export SERVICE_PORT=80
$ iptables \
  -A PREROUTING \
  -p tcp \
  -t nat -d $CLUSTER_IP \
  --dport $SERVICE_PORT \
  -m statistic --mode nth \
  --every 3 --packet 0 \
  -j DNAT \
  --to-destination 10.244.1.2:8080

$ iptables \
  -A PREROUTING \
  -p tcp \
  -t nat -d $CLUSTER_IP \
  --dport $SERVICE_PORT \
  -m statistic --mode nth \
  --every 2 --packet 0 \
  -j DNAT \
  --to-destination 10.244.1.3:8080

$ iptables \
  -A PREROUTING \
  -p tcp \
  -t nat -d $CLUSTER_IP \
  --dport $SERVICE_PORT \
  -j DNAT \
  --to-destination 10.244.1.4:8080 
```

现在我们会有一个问题，我们将数据包发送到集群 IP，直接发送到 **Pod** ，但是由于源 IP 是客户端 **Pod** IP，目标 **Pod** 会将回复发送回客户端 **Pod** 会错误的源 IP(不是集群 IP)。我们来简化一下这些词:

```
# request send
podA -> clusterIP
# After iptables
podA -> podB
# reply send
podB -> podA 
```

我们可以发现连接会被断开，因为目的地 IP 不是`podA`所期望的！

因此，我们还必须更改回复数据包的源 IP:

```
$ iptables \
  -t nat \
  -A POSTROUTING \
  -p tcp \
  -s 10.244.1.2 \
  --sport 8080 \
  -j SNAT \
  --to-source 10.0.0.2:80 
```

现在你终于完全明白了。

要获得有关负载平衡和 NAT(网络地址转换)的更多信息:

*   [wiki: DNAT](https://en.wikipedia.org/wiki/Network_address_translation#DNAT)
*   [维基:SNAT](https://en.wikipedia.org/wiki/Network_address_translation#SNAT)
*   [维基:循环赛](https://en.wikipedia.org/wiki/Round-robin_scheduling)

### 5 互联网服务

#### 5.1 出口

出口是从 Pod 到 internet 的流量，考虑从 Pod 到任何外部服务的数据包。
例如`10.244.1.10 -> 8.8.8.8`

然而，事情并不容易，因为`8.8.8.8`不知道谁是`10.244.1.10`，因为他们不在同一个网络中。所以无论如何，我们需要一个全球 IP，这叫伪装。现在假设我们有一个 IP `219.140.7.218`，我们的目标是在到达`8.8.8.8`之前将`10.244.1.10`改为`219.140.7.218`。然后当`8.8.8.8`发送回复包时，我们将`219.140.7.218`改为`10.244.1.10`来完成整个连接。

但是我们这里有另一个问题，如果我们有几个这样的输出请求怎么办？如何知道哪个 Pod 应该得到回复包？一种简单的方法(不打算介绍所有的 NAT 方法)是给每个连接分配一个端口，这样每个 Pod 输出请求都会得到一个端口，例如:`10.244.1.10:8080 -> 8.8.8.8:53`会被重写为`219.140.7.218:61234`，所以`8.8.8.8`会向`219.140.7.218:61234`发送回复。如果同时`10.244.1.11:8080 -> 8.8.8.8:53`重写为`219.140.7.218:61235 -> 8.8.8.8:53`得到了应答包，这个包就会发送到`61235`，这样我们就可以把这个包正确的重写回`10.244.1.10`和`10.244.1.11`。

#### 5.2 入口

##### 5.2.1 负载均衡器

负载平衡器很容易理解，因为它只是为您的服务提供一个 IP，并做完全相同的内部服务 IP 重写，然后发送到正确的 Pod。

##### 5.2.2 入口控制器

入口控制器是应用层负载平衡器。

示例(参见[https://github . com/dannypsnl/k8s-test-deployeds/tree/master/ingress](https://github.com/dannypsnl/k8s-test-deploys/tree/master/ingress)获取完整示例):

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /hello
        backend:
          serviceName: hello-svc
          servicePort: 80
      - path: /world
        backend:
          serviceName: world-svc
          servicePort: 80 
```

参见示例 yaml 可以发现，我们定义了`http.path` : `/hello`和`/world`，基本上 ingress 控制器会处理 http 请求的根路径`/`，当路径有前缀`/hello`时，将数据包发送到 **hello-svc** ，当路径有前缀`/world`时，将数据包发送到 **world-svc** (理想情况下，ingress-nginx 会在它们的代码中进行 pod 选择，至少 v0.20.0 版仍然是这样)。