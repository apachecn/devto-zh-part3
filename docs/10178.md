# 前向纠错，关于第 14 代 PowerEdge 和 25 千兆位连接的故事

> 原文：<https://dev.to/ngschmidt/forward-error-correction-a-story-about-generation-14-poweredge-and-25-gigabit-connectivity-b54>

## 25 千兆实现

首先，任何认为第一层很简单的人都是错误的。

### 话虽如此，但 25G/50/100G/QSFP28 服务的不同之处不仅仅是简单地比 10G 快 2.5 倍。802.3by(或者对使用它的人来说是 25g):

*   全双工是强制性的。
*   高能效运行
*   支持高达 28Gbits 速度的可堆叠通道
*   对于那些喜欢它的人来说，Twinax 目前最大长度为 5 米([http://www.ieee802.org/3/by/P802_3by_Objectives.pdf](http://www.ieee802.org/3/by/P802_3by_Objectives.pdf))
*   目前，在交换机级别，25G/100G 硅的成本似乎低于 10G/100G。

### 什么没有改变

*   BER 最小值仍然是 10 <sup>12</sup>
*   仍然支持所有现有的 802.1*协议

也就是说，我一直致力于在服务器上实现 25G 已经有一段时间了，我们屏息等待新服务器(支持 bcnxnet 2x25G 网卡)启动...

并且继续不建立任何链路级连接。

嗯，我们遵循通常的怀疑，试图静态协商速度-双工(这可能是一个平台怪癖)无济于事。

**事实证明，前向纠错是罪魁祸首。在查看 IEEE 关于 802.3by 的文档时，我们发现了这个问题，表明了协商不同 FEC 模式的困难:**

[http://www . IEEE 802 . org/3/by/public/Jan 16/hidaka _ 3by _ 01 _ 0116 . pdf](http://www.ieee802.org/3/by/public/Jan16/hidaka_3by_01_0116.pdf)

第 73 条概述了一组用于 FEC 自动协商的位，这些位将允许(超过 5 位)信令建立 same-same 连接，以就使用哪种模式达成一致——请记住，任何活动连接(所有光纤，5 米以上的双轴)都需要某种形式的 FEC 来检测链路上是否可能出现错误:

F0:提供 10G 外汇券

F1:请求 10G 外汇券

F2:提供 25G RS-FEC(**理想**)

F3:请求 25G RS-FEC

F4:请求 25G Base-R(消防代码)FEC

这对于防止下游故障非常重要——现在我们正在以相当高的速度传输数据，但由于 802.3by 最近在 2016 年才发布(RS-FEC 在 2017 年发布),对各种模式的支持可能有点不平衡。以下是优先考虑可靠性的顺序——如果延迟是主要目标/您使用非常好的电缆，请颠倒列表:

1.  RS-FEC
2.  FC-FEC
3.  FEC 已禁用

目前，第 14 代 Dell Poweredge 似乎支持所有模式，但默认为“禁用”，并且完全无法自动协商。无论如何，使用 Broadcom NICs 板载，您**将需要有意识地在这里选择一个选项，然后将其应用到您的交换机。**

此外，像 Cisco 的 Nexus EX 这样的早期 802.3by 交换机不支持单通道模式下的 RS-FEC，但支持多通道收发器:

[https://www . Cisco . com/c/en/us/products/parallels/switches/nexus-9000-series-switches/data sheet-c78-736651 . html](https://www.cisco.com/c/en/us/products/collateral/switches/nexus-9000-series-switches/datasheet-c78-736651.html)

这也可以通过购买新一代交换机(FX+)来解决，但所有代交换机似乎都可以自动协商，在交换机到交换机领域没有任何问题。

### 什么是 FEC？

嗯，维基百科的文章是一个很好的开始([https://en.wikipedia.org/wiki/Forward_error_correction](https://en.wikipedia.org/wiki/Forward_error_correction))，但是非常模糊。长话短说，您可以选择增加大约 800-2500 亿分之一秒的延迟，以实现对链路表面可靠性的“假设”分析。这很好，尤其是使用 twinax 时，比特错误比使用光纤更常见。FEC 还可以提供目的地之间比特错误的反馈，允许它“训练”或“自我修复”链路，从而实现更高的链路可靠性。

### 这对我意味着什么

在这种情况下，应进行以下设计影响:

*   如果它很重要，请使用多通道插槽:

    *   如果使用光纤，在成本允许的情况下，应该使用 QSFP28 收发器。这将提供 RS-FEC 的价值
    *   如果您有主干交换机，请使用 QSFP28 收发器。
*   如果您现在购买，请阅读您的服务器和交换机的产品介绍，确保支持 RS-FEC，并使用光缆