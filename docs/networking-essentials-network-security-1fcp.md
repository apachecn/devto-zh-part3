# 网络基础:网络安全

> 原文：<https://dev.to/swyx/networking-essentials-network-security-1fcp>

*这是我学习[免费的 Udacity 计算机网络基础课程](https://www.udacity.com/course/computer-networking--ud436)的第十一篇也是最后一篇课堂笔记。*

* * *

这是关于网络操作和管理的三部分迷你系列的第三部分。

*   [软件定义的网络](https://dev.to/swyx/networking-essentials-software-defined-networking-35n9)
*   [交通工程](https://dev.to/swyx/networking-essentials-traffic-engineering-13c4)
*   [网络安全](https://dev.to/swyx/networking-essentials-network-security-1fcp)

* * *

## 为什么要网络安全？

我们在本系列中讨论了针对基础设施各个部分的各种各样的攻击(*为了简洁起见，我不会在这里将它们联系起来，但是您可以滚动到底部查看我们讨论的相关主题*):

*   **路由(BGP)**:2010 年，[中国意外劫持 170 个国家的 5 万个 IP 前缀 18 分钟](https://arstechnica.com/information-technology/2010/11/how-china-swallowed-15-of-net-traffic-for-18-minutes/)。这突出了 BGP 的脆弱性，其中任何 AS 都可以向相邻 AS 通告 IP 前缀，并且它们将从表面上接受它并将其转播给互联网的其余部分。
*   **命名(DNS)**:**反射攻击**是一种分布式拒绝服务攻击——产生大量针对受害者的流量。**网络钓鱼**也是一种常见的攻击，试图在流氓网站上获取用户的个人信息。

互联网的设计从根本上说是不安全的。它是为了简单而不是安全而设计，一台主机是“默认开启”的，或者任何其他主机都可以到达，所以如果您有一台不安全的主机，它就很容易受到攻击。这在互联网是一个由可信网络组成的小型网络时是没问题的，但现在不再是了。它的分散性也使得很难协调防御。

## 资源枯竭攻击

正如我们在第一篇帖子中了解到的，**分组交换**允许多台主机使用**统计复用**共享同一条链路，很容易实现高利用率，但也很容易使链路过载。这些攻击的目标是系统的**可用性**。

## 保密性和真实性攻击

我们还希望我们的系统能够提供**保密性**、**真实性**和**完整性**，例如，当我们执行一项银行交易时，我们希望它是保密的，并确保信息确实来自我们的银行，并且没有在传输中被修改([中间人攻击](https://en.wikipedia.org/wiki/Man-in-the-middle_attack))。

## 攻击的负面影响

损害**可用性**、**保密性**、**真实性**和**完整性**会导致:

*   窃取机密信息
*   未经授权使用资源
*   虚假信息
*   合法服务的中断

## 路由安全

重点关注 BGP 和控制平面安全性，这涉及对路由协议所通告的消息的认证:

*   **会话认证**:保护路由器之间的点对点通信
*   **路径认证**:保护 AS 路径
*   **源认证**:保护*源* AS，保证通告前缀的源 AS 实际上是该前缀的所有者。(例如防止路由劫持)

## 起源认证:路由劫持

对于成功的 MITM 攻击，所有发往合法目的地的流量都需要路由到攻击者，而攻击者与合法位置的原始连接保持不变。

这是由**作为路径中毒**来完成的——为了让攻击者到合法位置的路由得以保留，它将路径主机作为广告预先添加到自己的路径主机上，以便它们忽略攻击者的消息，从而努力避免循环。

这使得 MITM 很成功，然而在跑`traceroute`的时候迂回的路线可能会被发现。MITM 甚至可以被隐藏起来，因为从数据包到达 TTL 0 时起,`traceroute`就只包含“ICMP 超时消息”——这通常会被沿途的每个路由器递减。如果攻击者网络中的路由器*从不递减 TTL* ...那么就不会生成任何消息，攻击者也不会出现在`traceroute`中。

## 会话认证

两个 as 之间的会话是 TCP 会话，所以我们只使用 [TCP 的 MD5 认证选项](https://tools.ietf.org/html/rfc2385)。在连接中交换的每个消息不仅包含消息，还包含带有共享密钥的消息的 [MD5 散列](https://en.wikipedia.org/wiki/MD5)(在 AS1 和 AS2 运营商之间手动“带外”(也称为离线)分发)。

另一种保护连接的方法是用 TTL 发送数据包< 254\. Because most eBGP sessions are only a single hop, and an attacker would be remote, it is not possible for the recipient AS to accept packets from a remote attacker. This is called [TTL 黑客防御](https://bird.network.cz/pipermail/bird-users/2014-April/004276.html)。

## 来源和路径认证:BGPsec

为了保证来源和路径认证，[BGP sec 提案](https://en.wikipedia.org/wiki/BGPsec)被引入。它由两部分组成:

1.  **来源证明**(又名地址证明):将 IP 前缀绑定到其所有者的签名证书。它必须由“可信方”签名，如路由注册中心或前缀分配组织
2.  **路径证明**:当 as 路径从一个 AS 通告到下一个 AS 时，伴随 AS 路径的一组签名。假设沿着路径 AS1-AS2-AS3，每个 AS 都有一个公钥-私钥对。每个 AS 用它自己的私钥签署一条路径，并且每个其他 AS 可以用公钥检查它。当一个 AS 从另一个 AS 重新广播一个路径时，它转发该原始路径的签名，以及将其自身添加到该路径的新签名。因此，每个 AS 可以独立地验证路径的每一步都没有被篡改，因为它具有由路径上的每个私钥签名的累积信息。

BGPsec 将防止各种可能的相关攻击:

*   路径劫持
*   路径缩短
*   路径修改

然而，还有其他原因没有考虑到:

*   路径抑制(如果 AS-失败-通告新的路由，或者路由撤销)
*   重放攻击(重新发布撤回的路线)
*   没有办法保证数据流量*实际上沿着*通告的路径(！！！任何路由协议都还没有解决的重大弱点)

## DNS 安全

关于 DNS 工作原理的一个小复习:

*   您的浏览器请求一个新的未知域名
*   ISP 的存根解析器向缓存解析器发出查询
*   缓存解析器在缓存中没有任何东西，所以它将查询转发给一个权威名称服务器
*   ANS 通常具有主复制和从复制以实现弹性
*   ANS 还包含[区域文件](https://en.wikipedia.org/wiki/Zone_file)和一个[动态更新系统](https://en.wikipedia.org/wiki/Domain_Name_System#Authoritative_name_server)

以下是该系统中的一些漏洞:

*   存根解析器和缓存解析器之间的 MITM 攻击
    *   辩护:DNSSEC
*   **缓存中毒**攻击缓存解析器，在真正的回复从 ANS 返回之前向其发送回复。*这是一个大问题，所以我们将在下面花更多的时间。*
    *   防御:0x20
*   主机或从机应答的欺骗
    *   辩护:DNSSEC
*   “DNS 反射”:使用 DNS 发起大规模 DDoS 攻击

DNS 解析器从根本上来说是易受攻击的，因为它们的查询产生了一种竞争条件——它们信任第一个响应者，不管是谁。基本 DNS 协议没有验证响应的方法。DNS 还使用 UDP，它是“无连接的”(没有像 TCP 那样的会话)，因此解析器除了查询 ID 之外，无法映射它收到的查询响应。

## DNS 缓存中毒及如何防御

在理想情况下，存根解析器将它的`A`查询发送到缓存(或递归)解析器，缓存解析器将它发送到 ANS，ANS 用 IP 一路回复。

**攻击**

要攻击它，攻击者只需要大量猜测不同查询 id 的多个回复，并让其中一个匹配。只要假消息在合法消息之前收到，它就会被接受并缓存！一旦消息被缓存，DNS 就无法删除它，所以它将继续用这些假数据进行响应，直到条目在缓存中过期。这是一篇关于前缀劫持攻击的相关论文。

另一个漏洞存在于`A` vs `NS`查询系统中。`A`记录请求特定的子域，而`NS`记录请求整个区域。攻击者可以生成一系列伪造的 A 记录查询，这些查询显然不像`fakesubdomain.google.com`那样存在，然后用整个区域的`NS`记录作为响应，并且它仍然拥有`google.com`。这就是所谓的[卡明斯基袭击](https://duo.com/blog/the-great-dns-vulnerability-of-2008-by-dan-kaminsky)。

**防御**

查询 ID 是一级防御，当然也是可以猜到的。通过将其随机化，可以使其更难猜测，但它只有 16 位(32，000)，因此整个空间都是可猜测的(并且很可能在几百个回复内匹配)。我们可以通过随机化查询的源端口，向 ID 随机化添加额外的 16 位熵，但是这可能是资源密集型的，并且会被 NAT 抵消。

**0x20** 防御是指 DNS 不区分大小写，所以不使用 **0x20** 字符(控制大小写)，所以我们可以用它来引入额外的熵。所以我们可以像`www.GoOgLE.com`一样大写一个请求，大小写的模式将形成一个编码的“通道”。在解析器和 ANS 之间就共享密钥达成一致将有助于生成正确的大写和小写序列。这使得注入虚假回复更加困难。

**DNSSEC 协议**通过为每个 DNS 回复返回的响应添加签名，在 DNS 之上添加认证。因此，当一个解析器向根服务器发送一个`A google.com`查询时，它用对`.com`服务器的引用以及 IP 地址的根签名和`.com`服务器的公钥来响应。因此，如果解析器拥有根服务器的公钥，它现在也可以将其信任传递给`.com`服务器，后者向`google.com`服务器发送一个签名的引用。换句话说，层次结构中的每一级现在不仅用下一级的数据来响应，而且用包含该引用的 IP 地址以及该引用的公钥的签名来响应。

## DNS 放大攻击

查询和响应的大小不对称。攻击者的查询可以欺骗一个受害者作为查询源，而且只有 60 个字节。但是，DNS 解析器的响应会是 3000 字节左右！这就是这次攻击中的“放大”。使用多个攻击者发送欺骗请求将生成多个大量回复，所有回复都发往受害者，从而形成 DDoS 攻击。

为了防止这种情况，我们必须通过禁用 DNS 解析器解析来自互联网上任意位置的查询的能力来防止 IP 地址欺骗，这限制了它的有用性。

## 我们系列的结束

希望这是对网络安全如何工作的一个很好的高层次概述，并且这整个系列在某种程度上对您是一个有用的介绍。提醒一下，以下是我们讨论过的所有主题:

*   [架构和原理](https://dev.to/swyx/networking-essentials-architecture-and-principles-2g5e)
*   [切换](https://dev.to/swyx/networking-essentials-switching-3eba)
*   [路由](https://dev.to/swyx/networking-essentials-routing-5gb7/)
*   [命名/寻址/转发](https://dev.to/swyx/networking-essentials-naming-addressing-and-forwarding-13kk)
*   [DNS](https://dev.to/swyx/networking-essentials-dns-1dl7)
*   [拥塞控制和流式传输](https://dev.to/swyx/networking-essentials-congestion-control-26n2)
*   [速率限制和流量整形](https://dev.to/swyx/networking-essentials-rate-limiting-and-traffic-shaping-43ii)
*   [内容分发](https://dev.to/swyx/networking-essentials-content-distribution-jag)
*   [软件定义网络](https://dev.to/swyx/networking-essentials-software-defined-networking-35n9)
*   [交通工程](https://dev.to/swyx/networking-essentials-traffic-engineering-13c4)
*   [网络安全](https://dev.to/swyx/networking-essentials-network-security-1fcp)

你怎么想呢?我还应该涉及哪些话题？[在推特上让我知道@swyx！](https://twitter.com/swyx)