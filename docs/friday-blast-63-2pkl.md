# 星期五爆炸#63

> 原文：<https://dev.to/horia141/friday-blast-63-2pkl>

[可观察性管道(2018)](https://bravenewgeek.com/the-observability-pipeline/) -将指标/日志/跟踪数据视为“另一个数据集”而不是不同的东西的情况。例如，首先将它们发送到一个中央收集点(Kafka 或类似产品),然后将它们分发到所有需要它们的“目标”——记录基础架构、指标和警报，甚至是数据湖，以便它们可以作为业务指标使用。主要的想法是停止这些数据的问题，并引入一些解耦，这样以后更容易切换基础设施提供商。

[扩展验证证书已死(2018)](https://www.troyhunt.com/extended-validation-certificates-are-dead/) -信息很重要-不要在电动汽车上浪费钱，只使用常规证书。这篇文章的其余部分(真的很长)看起来像是在嘲笑 Comodo 网络安全。

[语义网到底怎么了(2018)](https://www.troyhunt.com/extended-validation-certificates-are-dead/)——还记得“语义网”吗？以前是*的事情*。但它从未真正流行起来。我感兴趣的是 T4 的政治是如何进行的——在这项技术被采用甚至成为真正的杀手级用例之前，已经有了一百万个标准。

[在谷歌打字稿(2018)](http://neugierig.org/software/blog/2018/09/typescript-at-google.html)-JavaScript 和整个“前端事物”如何在谷歌和世界其他地方演变。这是一篇有趣的阅读，讲述了这个东西的历史，以及作者的团队如何试图*同步*谷歌和其他人现在使用的东西。在了解开源和构建平台的价值之前(所以在他们认真对待云之前)，IMO Google 已经做了无数次*。他们做的事情比任何人都要早。但他们没有谈，最多也就发布了一三篇论文。世界接受了这些想法，并在技术上迎头赶上，但使用的是他们自己的东西。例子比比皆是:Hadoop vs MapReduce，HBase/Cassandra vs BigTable 等等。现在 GCP 在他们自己的基础设施上提供了 HTable 接口。谢天谢地，他们在 Kubernetes 和 TensorFlow 上做得很好。*

 *[Pixie——向 2 亿多用户实时推荐 30 多亿件商品的系统(2018)](https://blog.acolyer.org/2018/05/23/pixie-a-system-for-recommending-3-billion-items-to-200-million-users-in-real-time/)——Pinterest 一篇关于其推荐引擎的论文综述。这些东西是许多公司的支柱——网飞、Pinterest、脸书等。但是他们通常在 ML 课程中被掩盖。但是它们本身就很有趣，而且很难弄对。所以这是一个关于 Pinterest 团队如何做到这一点的有趣讨论。*