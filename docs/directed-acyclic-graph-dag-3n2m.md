# 有向无环图

> 原文：<https://dev.to/bitdeal/directed-acyclic-graph-dag-3n2m>

## DAG 是什么？

有向无环图是一种使用拓扑排序的数据结构。DAG 是有向图形结构的实现。DAG 主要用于解决数据处理、寻找最佳导航路线、调度和数据压缩等问题。DAG 图仅涉及事务，因此避免了挖掘过程的需要。因此，DAG 被称为无块数据分类帐，它仅包含事务，并且消除了矿工挖掘新块的需要。
因此，DAG 减少了交易时间并提高了可扩展性，而这似乎是区块链的一个瓶颈。

## DAG 的概念

### 消灭宁敏

比特币区块链涉及采矿和矿工验证交易的过程。DAG 网络消除了挖掘过程，事务直接进入验证本身。因此，DAG 支持自动验证和无挖掘器，从而实现更安全、更快捷和即时的交易。

### 缩短网络宽度

在区块链中，当验证一个交易时，它链接到前一个交易，这可能导致网络变宽。在 DAG 中，验证后，每个事务都链接到网络上的一个新事务和一个现有事务。因此，DAG 倾向于将现有的后续事务链接到新事务。DAG 的目标是将网络带宽保持在一定范围内，能够支持快速事务及其验证。

### Swift 交易

由于 DAG 具有无阻塞的性质，事务直接进入网络，这有助于更快的事务。DAG 网络上的整个交易过程比基于 PoW 和 PoS 的区块链要快得多。
最适合小额支付
DAG 技术的引入是为了以最低的费用实现顺畅、快速的交易。这创造了一种方式，通过这种方式，用户可以发送费用更低且不像比特币或以太坊那样沉重的微支付。

### 重复消费问题

比特币区块链使用未用完的交易输出模型，使得用户只能进行一次交易。在 DAG 中，每个事务的有效性取决于支持它的事务的数量。交易进入网络的速率更低，使得交易更安全、更快捷。

## 基于 DAG 的平台/技术

### IOTA

IOTA 是第一个基于 DAG 的加密货币项目，旨在消除矿工费用的概念。IOTA 最初被设计为物联网(IoT)的主干，但后来它的功能被用于其他无止境的用例。

### 纳米

纳米平台使用了一种奇怪的架构，即类似于闪电网络的块格。NANO 没有保留整个区块链及其被称为侧链的分支机构的历史，而是一种新的网络拓扑结构，其中每个帐户都有自己的区块链，称为帐户链。这使得更快的交易成为可能。
Soure:[T3】BitdealT5】](https://www.bitdeal.net/directed-acyclic-graph-blockchain)