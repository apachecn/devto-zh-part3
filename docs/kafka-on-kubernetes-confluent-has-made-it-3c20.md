# 库伯内特斯上的卡夫卡？汇合成功了

> 原文：<https://dev.to/simon_sugob/kafka-on-kubernetes-confluent-has-made-it-3c20>

# 在 Kubernetes 上部署 Kafka 现在更容易了

## 合流算子

Kubernetes 在该公司无数平台上的广泛使用催生了 [Confluent 运营商](https://www.confluent.io/confluent-operator/)—**Confluent 联合创始人兼首席技术官 Neha Narkhede 表示:“在 Kubernetes 上部署 Apache Kafka 和 Confluent 平台的最佳方式”**。 [Alex Woodie](https://www.datanami.com/2018/05/03/want-kafka-on-kubernetes-confluent-has-it-made/) 正确地注意到“许多组织试图让 Kafka 在 Kubernetes 集群管理器上运行，但这是一个具有挑战性的技术壮举，而 Confluent 已经成功了！”

“让每个人都能在 Kubernetes 上运行 Apache Kafka 是我们将流媒体平台置于每个公司核心的使命的重要组成部分。这就是为什么我们期待发布一个实现 [Kubernetes Operator API](https://coreos.com/blog/introducing-operators.html) ，用于在 Kubernetes 上自动提供、管理和操作 Kafka "Neha nark hede 说。

*“这包括为整个融合平台部署预构建的 Docker 映像、Kubernetes 部署模板、使用户能够在 Kubernetes 上部署 Kafka 和融合平台的参考架构，以及用于在 Kubernetes 上自动供应、管理和操作 Apache Kafka 的 Kubernetes 操作员 API 的实施”*–添加了融合的 CTO。

## 卡夫卡+库伯内特的专长=合流算子

[![KAFKA ON KUBERNETES](img/ec7e261343c37fa778a007a1e451e71d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Abz2XBLq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.hiredevops.org/wp-content/uploads/2017/11/kafka-on-kubernetes.png)

Rohit Bakhshi，文章[“Apache Kafka 和 Kubernetes 入门”](https://www.confluent.io/blog/getting-started-apache-kafka-kubernetes/)的作者，很好地总结了这个解决方案:

“我们的目标是让流数据无处不在:Kubernetes 让你可以在任何地方运行你的应用和服务，Kafka 让你可以随时随地访问你的数据”。

尽管 Kubernetes 上的 Apache Kafka 之间的联系可能看起来像是“大数据天堂中的匹配”，但问题在于细节。需要克服的最大障碍是什么？[阅读更多](https://www.datanami.com/2018/05/03/want-kafka-on-kubernetes-confluent-has-it-made/)。

## 你为什么要用合流算子在 Kubernetes 上部署 Kafka？

Neha Narkhede 解释说，“融合操作符直接解决了在 Kubernetes 上运行 Apache Kafka 的挑战，并将在所有融合平台组件上提供以下特性”:

1.  自动化供应
2.  集群管理和运营
3.  跳回
4.  监控[阅读更多](https://www.confluent.io/blog/introducing-the-confluent-operator-apache-kafka-on-kubernetes/)

[![KAFKA ON KUBERNETES](img/462d63a2c4f3d662ddfde461510db8c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CVae1Wxh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.hiredevops.org/wp-content/uploads/2017/11/Kafka-on-kubernetes-HireDevops-Kubernetes-Docker-experts-1-1200x900.png)

## 入门——如何在 Kubernetes 上轻松运行 Apache Kafka

Confluent 提供了两个在 Kubernetes 上安装 Kafka 的资源:

1.  [舵图](https://www.confluent.io/confluent-operator/)-汇合平台组件部署的起点

2.  [在 Kubernetes 上部署 Apache Kafka 的建议](https://www.confluent.io/resources/recommendations-for-deploying-apache-kafka-on-kubernetes)—白皮书

**提示:我们不仅建议您熟悉这些工具，还建议您查看以下两个资源，以全面了解部署部分:**

1.  [有状态、无状态和无服务器——在 Kubernetes 上运行 Apache Kafka](https://videos.confluent.io/watch/yoZcuazDjDDTcj1sRnaD3J)—Heptio CTO 和 Kubernetes 联合创始人 Joe Beda 与 Confluent 首席数据架构师 Gwen Shapira 进行在线交流。
2.  库伯内特上的阿帕奇卡夫卡——你能吗？你应该吗？——本文给出了舵图推出后的第一个反思。

## 你已经可以[注册](http://www.confluent.io/kubernetes)在 Kubernetes 上编排阿帕奇卡夫卡。

也贴[这里](https://www.hiredevops.org/kafka-on-kubernetes-confluent-has-made-it/)