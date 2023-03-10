# 星期五爆炸#64

> 原文：<https://dev.to/horia141/friday-blast-64-3kb0>

坚持到 2 <sup>6</sup> 周五爆炸！

[程序员相信的关于日历的谎言(2018)](https://boingboing.net/2018/09/27/corner-cases-everywhere.html) -关于日期、时间和日历复杂性的有趣小文章。这个话题之前已经讨论过了。这东西超级复杂，因为人们一直在改变东西。几乎你能想到的任何东西都有一个反例——甚至是“向前”运行的时间。

[Zendesk 基础设施的历史-持续权衡(2018)](https://medium.com/zendesk-engineering/the-history-of-infrastructure-at-zendesk-constant-tradeoffs-bdfa916ff4c3) - Zendesk 似乎正在做许多有趣的事情，作为“大规模服务”。有趣的是他们对 AWS 的态度和他们所受的限制——这导致他们不使用 AWS，尽管有几次机会*赶时髦*。基本上，它们总是比 AWS 所能提供的要大一些，所以使用其他东西是有意义的。

[《现代面向列的数据库系统的设计与实现(2018)](https://blog.acolyer.org/2018/09/26/the-design-and-implementation-of-modern-column-oriented-database-systems/)——我*应该*在某个时候读的一篇论文的概述。列数据库是分析工作负载的主力。本文涵盖了一些针对它们的设计方法。压缩、位图索引甚至对压缩列的直接操作都是获得良好性能的关键要求。

[Marmaray:Apache Hadoop(2018)的开源通用数据摄取和分发框架和库](https://eng.uber.com/marmaray-hadoop-ingestion-open-source/)——优步的“数据摄取”库。优步的“数据系统”以前也在博客上出现过。这本身就很有趣——优步是一家大型科技公司，但关注范围非常狭窄，所以这些系统往往非常特定于他们。密切关注竞争对手在做什么也很好。主要问题是控制数据是如何被“摄取”和存储的。有多个生产者(Kafka，MySQL，Cassandra 等。)和多个消费者(S3、HDFS、卡珊德拉等)。为了避免一个`NxM`问题，马尔马拉被设计出来。它是 Spark 上的一个系统，控制摄取(读取)和传播(写入)。它也由一个团队集中管理和运营，并作为服务提供给其他团队。