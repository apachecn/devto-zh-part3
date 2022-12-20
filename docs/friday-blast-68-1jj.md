# 星期五爆炸#68

> 原文：<https://dev.to/horia141/friday-blast-68-1jj>

[我们为什么在 RocksDB (2019)](https://www.cockroachlabs.com/blog/cockroachdb-on-rocksd/) 之上构建 cocroach db——cocroach db 团队的一篇颇有见地的帖子，讲述了他们如何使用 [RocksDB](https://rocksdb.org/) 作为单个节点的存储层。RocksDB 是 LevelDB、Sqlite 等系列中的嵌入式 KV-store。其中使用了 [LSM](https://en.wikipedia.org/wiki/Log-structured_merge-tree) 存储器。对我来说，有趣的是这种分离似乎越来越普遍。使用众所周知的存储层，并仅在其上构建增值内容。尤其是因为所述存储层将处理平台特性，如`fsync`bug 等。MySQL 甚至 Postgres 都被这样使用过，但它们显然更重量级。

[探索分布式系统理论:可用性和一致性(2019)](https://hackernoon.com/exploring-distributed-system-theory-availability-and-consistency-e8c59e0875cd)——这是一本将 CAP 定理的抽象(和模糊)本质与你将看到的真实系统联系起来的好书。最精彩的部分是对像 etcd/ZooKeeper 这样的“CP”系统的讨论，以及如何权衡可用性就意味着不能拥有“完美”的可用性——用某种成功数据来响应每一个*查询。但是不要进入“愚蠢的”可用性状态——比如不响应任何查询。像这样的系统通常被部署在`3`或`5`组中。当出现分区时，它们被设计为选择一致性。因此，仲裁之外的节点将无法处理写入——因此它们将不再*可用*。但是系统作为一个整体仍然处于*体面的*运转状态*可用性降低*。*

[开始的结束# YouTube(2018)](https://www.youtube.com/watch?time_continue=7&v=RF5VIwDYIJk)——Andre essen Horowitz 的 Benedict Evans 关于*科技创业公司*(至少他们知道它们)的“未来”的演讲。主要观点是，尽管它已经成功影响了许多领域——最大的赢家是电子商务和广告——但我们甚至还没有触及到可以做些什么的表面。事实上，困难的部分仅仅来自这里，但它有可能影响整个全球经济，从农业到物流和医药。

[Kubernetes in 5 mins # YouTube(2017)](https://www.youtube.com/watch?v=PH-2FfFD2PU)-盒子上写的。K8s 有用之处的简短指南。