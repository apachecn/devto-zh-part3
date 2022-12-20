# 面向开发人员的网络–第 3 部分:网络层和物理层

> 原文：<https://dev.to/flaviabastos/networking-for-developers--part-3-network-and-physical-layers-1joh>

> 注意:在这个帖子系列中赶上以前的帖子:
> 
> [第 1 部分:概述](https://wp.me/pa0b0y-2s)
> 
> [第 2 部分:应用层和传输层](https://wp.me/pa0b0y-2G)

在互联网中，网络层使用**互联网协议**，它负责分析数据包中的目的地地址，并将它们导向它们应该去的地方。实际传送数据包的工作属于**物理层**，它是位于硬件之上的最底层。

如果您是配置本地网络(例如，一个小型办公室)的人员，您需要了解子网掩码和网络前缀是如何工作的，以便正确地路由数据包，将它们发送到内部网络中的另一台主机(计算机),或者告诉网关将数据包发送到另一个网络(可能是互联网)。我不会涉及这类信息，因为这不是本系列的重点。

网络层使用的两个主要协议是负责连接和路由的 **ICMP(互联网控制消息协议)**和负责将主机名映射到 IP 地址的 **DNS(域名服务)**。

当实际移动数据时，物理层接管:网络中的所有设备都有一个物理地址，即 MAC(媒体访问控制)地址。这里的数据被打包成帧发送。该帧将具有源 MAC 地址和目的 MAC 地址，因此网络物理层中的设备将知道应该将数据移动到哪个设备。

路由器(网络中的物理设备)可以重新成帧一些数据，以防需要将其发送到不同物理网络中的主机。

排除**网络**层故障的主要工具如下:

#### ifconfig

如果不带参数使用，将显示主机网络接口的状态。这是<u>命令，您应该使用它来找出主机的 IP 地址</u>。输出将至少显示以太网(eth0)或 Wifi (wlan0)接口、localhost (lo)接口(这是一种称为 loopback 的虚拟网络接口)等。

```
ifconfig 
```

Enter fullscreen mode Exit fullscreen mode

#### 平

它会向网络主机发送 ICMP 回应请求。如果接收主机收到数据包并配置为响应，它将发回 ICMP 回应。在这个命令的输出中需要注意的两件事情是 **icmp_seq** ，它理想情况下是按顺序的，所以它告诉你在这个传输中没有数据包丢失；以及**时间**，它告诉你往返花费了多长时间。如果您看到一个高于 1000 毫秒的数字，这意味着连接速度很慢。

您可以使用主机名或 IP 地址:

```
ping www.google.com 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用 CTRL+C 来停止 *ping* 的输出，或者您可以使用-c 选项来限制输出计数，如下所示:

```
ping -c 3 www.google.com 
```

Enter fullscreen mode Exit fullscreen mode

#### traceroute

它跟踪数据包到达远程主机的路径。例如，检测路径的哪一点有阻塞会很有帮助。该命令可以使用主机名或 IP 地址:

```
traceroute www.google.com 
```

Enter fullscreen mode Exit fullscreen mode

#### 主机

这是一个 DNS 查找工具，可以将域名转换成 IP 地址，反之亦然。

```
host www.google.com 
```

Enter fullscreen mode Exit fullscreen mode

对**物理层**进行故障排除的两个有用工具:

#### [工具](#ethtool)

它显示有关网络驱动程序和硬件设置的信息。它将一个网络接口名称作为参数——我们刚刚了解到可以通过使用 *ifconfig*[![😉](img/b26450942c7c42752fe0b02f126abb48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fumfYCPq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f609.png) 找到它。在 *ethtool* 的输出中需要注意的主要内容是检测到的**链接**，它会告诉您硬件实际上已打开并已连接。检查**速度**和**双工**可能也有帮助，前者是硬件能够传输数据的速度，后者将告诉您当前的[双工模式](https://en.wikipedia.org/wiki/Duplex_(telecommunications))。注意，不是所有的接口都有这些额外的信息。

```
ethtool eth0 
```

Enter fullscreen mode Exit fullscreen mode

#### arp

*arp* 代表地址解析协议，用于查找网络中设备的 MAC 地址。这有助于检查网络配置中是否存在冲突。它可以接受一个 IP 地址作为参数，也可以不接受任何参数，在这种情况下将显示所有已知的 MAC 地址。

```
arp 
```

Enter fullscreen mode Exit fullscreen mode

* * *

同样，关于这些层和一般网络，还有很多可以说的。这里有一些资源来扩展我在这个系列中分享的信息(其中大部分来自 [Julia](https://jvns.ca/) ，因为她解释东西很棒！):

*   茱莉亚的[计算机网络杂志](https://wizardzines.com/zines/networking/)(在线免费阅读)和[即将推出的网络工具漫画](https://jvns.ca/blog/2019/02/10/a-few-networking-tool-comics/)(博客文章)
*   Julia 关于 [IP 地址和路由](https://jvns.ca/blog/2018/07/24/ip-addresses-routing/)的博文
*   萨隆邀请茱莉亚参加了 T2 的代码新手播客 T3，向 T4 解释互联网是如何工作的？
*   Julia 在[上的博文什么是网络接口](https://jvns.ca/blog/2017/09/03/network-interfaces/)
*   [互联网杂志](https://shop.bubblesort.io/products/how-does-the-internet-zine)作者[艾米·维博沃](https://twitter.com/sailorhg)(付费)
*   [网络开发者网络](https://www.udacity.com/course/networking-for-web-developers--ud256) Udacity 课程(免费)

> *帖子* [开发者网络-第三部分:网络和物理层](https://wp.me/pa0b0y-2P) _ 最初发表在 _ [flaviabastos.ca](https://flaviabastos.ca/)