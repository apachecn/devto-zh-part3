# 阿帕奇卡夫卡:快速入门

> 原文：<https://dev.to/devshawn/apache-kafka-quick-start-5amd>

*这篇文章最初发表在我的[个人博客](https://devshawn.com/blog/apache-kafka-quick-start/)* 上。

Apache Kafka 是一个分布式流平台，可以充当消息代理、流处理管道的心脏，甚至可以充当企业数据同步系统的主干。卡夫卡不仅是一个高可用、容错的系统；与 RabbitMQ 或 ActiveMQ 等其他消息代理相比，它还能处理更高的吞吐量。

在本教程中，您将安装 Apache Kafka，在集群中运行三个代理，并学习如何从集群中生成和使用消息。本教程假设您没有安装 Kafka 或 ZooKeeper。

## 先决条件

要完成本教程，您需要:

*   UNIX 环境(Mac 或 Linux)
*   安装了 Java 8+

*注意* : Java 7 支持在 2.0.0 中被删除。Java 11 支持是在 2.1.0 中添加的。

## 安装

从 Apache Kafka 网站下载 Apache Kafka 及其相关的二进制文件。在本文发表时，最新版本是[阿帕奇卡夫卡 2.1.1](https://www.apache.org/dyn/closer.cgi?path=/kafka/2.1.1/kafka_2.12-2.1.1.tgz) 。从上一个链接下载后，从下载位置提取`.tgz`文件。

```
tar -xzf kafka_2.11-2.1.0.tgz
cd kafka_2.11-2.1.0 
```

## 系统架构

让我们来看看一个简单的 Apache Kafka 设置的架构。

*   **Kafka Cluster** :形成分布式系统的一群 Kafka 经纪人
*   **Kafka Broker**:Kafka 的一个实例，保存数据主题
*   ZooKeeper:存储和管理配置的集中系统
*   **生产者**:向 Kafka 主题发送消息的客户端
*   **消费者**:从 Kafka 主题中读取消息的客户端

Kafka 利用 ZooKeeper 来管理和协调集群中的代理。生产者和消费者是与 Kafka 交互的主要组件，一旦我们有了一个正在运行的 Kafka broker，我们就会看到这一点。在本教程中，我们将运行**三个**卡夫卡经纪人和**一个**动物园管理员节点。

<figure>[![Apache Kafka: Installation & Quick Start](img/781ea40df0ce28a484da74d8d135cf73.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dZ45M3k4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://devshawn.com/conteimg/2019/05/getting-started-diagram.png) 

<figcaption>本教程中使用的集成架构图</figcaption>

</figure>

上图显示了本教程中使用的系统和工具的架构。它有助于演示 Kafka brokers 如何利用 ZooKeeper，我们将使用的命令行工具与哪些组件进行交互，并显示正在运行的服务的端口。

## 启动动物园管理员

ZooKeeper 是一个集中式服务，用于维护命名和配置数据，并在分布式系统中提供灵活和健壮的同步。Kafka **需要** ZooKeeper，所以我们必须在启动 Kafka 之前启动一个 ZooKeeper 的实例。

方便的是，Apache Kafka 的下载包括一个运行 ZooKeeper 实例的简单方法。在`bin`目录中，有一个名为`zookeeper-server-start.sh`的文件。要启动 ZooKeeper，从下载的根目录运行下面的命令:

```
bin/zookeeper-server-start.sh config/zookeeper.properties 
```

在您的终端中，ZooKeeper 日志将开始流动，您将很快看到一行声明 ZooKeeper 正在端口`2181`上运行。这是 ZooKeeper 的默认端口，可以在`config/zookeeper.properties`中更改。

> *注意【ZooKeeper 保存其状态的默认目录设置为`/tmp/zookeeper`。如果你重启你的机器，所有的 ZooKeeper 数据将会丢失。*

最后，打开一个新的终端窗口，让 ZooKeeper 继续在原来的终端中运行。确保你`cd`到你解压的 Kafka 下载的根目录。

## 建立一个卡夫卡集群

官方的 Kafka 快速入门指南只运行一个代理——这不是真正的分布式系统或集群；所以我们要经营**三个**经纪人！:)

让我们检查位于`config/server.properties`的 Kafka 代理的配置文件。您可以通过运行:
在新的终端窗口中查看配置文件

```
cat config/server.properties 
```

有相当多的配置，但我们关心的主要属性如下:

*   `broker.id=0`:经纪人的唯一标识
*   `listeners=PLAINTEXT://:9092`:代理的协议和端口
*   `logs.dir=/tmp/kafka`:数据在代理中的存储位置

所有这三个配置属性**对于每个代理来说必须**是唯一的。默认情况下，您可以看到默认的代理 id 是`0`，默认的 Kafka 端口是`9092`。因为我们将启动 3 个代理，所以让我们为每个代理复制这个文件，并保留`server.properties`作为参考。我们可以通过运行:
来做到这一点

```
cp config/server.properties config/server-1.properties
cp config/server.properties config/server-2.properties
cp config/server.properties config/server-3.properties 
```

接下来，我们需要修改上面列出的属性，使其对于每个代理都是唯一的。您需要确保取消对`listeners`属性的注释。使用您喜欢的文本编辑器或通过 CLI 程序(如`vim`)修改文件。确保只修改下面的行，而不是用它们替换整个文件！

**server-1.properties**

```
broker.id=1
listeners=PLAINTEXT://:9091
log.dirs=/tmp/kafka-1 
```

**server-2.properties**

```
broker.id=2
listeners=PLAINTEXT://:9092
log.dirs=/tmp/kafka-2 
```

**server-3.properties**

```
broker.id=3
listeners=PLAINTEXT://:9093
log.dirs=/tmp/kafka-3 
```

耶！我们现在有了每个代理的配置文件。每个代理都有一个唯一的 id，监听一个唯一的端口，并将数据存储在一个唯一的位置。

> *注意:*与 ZooKeeper 一样，数据存储在`/tmp`目录中。当你重新启动机器时，所有的数据都将丢失。

## 首发卡夫卡

除了你当前的终端，再打开两个终端窗口和`cd`到你的 Kafka 下载目录。此时应该有四个终端打开；一个运行动物园管理员和三个运行卡夫卡。

要启动 Kafka，您需要运行以下命令，每个命令都在单独的终端中:

```
bin/kafka-server-start.sh config/server-1.properties 
```

```
bin/kafka-server-start.sh config/server-2.properties 
```

```
bin/kafka-server-start.sh config/server-3.properties 
```

您将开始在每个终端中看到您启动的代理的日志。如果您查看您的 ZooKeeper 终端，您还会看到来自连接到 ZooKeeper 的代理的日志。每个终端都应该以类似于
的行结束

```
[2019-03-02 15:28:21,074] INFO [KafkaServer id=1] started (kafka.server.KafkaServer) 
```

恭喜你。您现在有一个 Kafka 集群在运行，在端口`9091`、`9092`和`9093`上公开了一个唯一的代理。

## 创建话题

现在我们有一个 Kafka 集群正在运行，让我们发送一些消息吧！要做到这一点，首先要创造一个话题。Kafka 包括一些命令行工具来完成这个任务，位于`bin`目录中。打开一个新的终端窗口和`cd`到 Kafka 下载目录。

让我们创建一个名为`test`的主题。我们可以通过利用`bin`目录中的`kafka-topics.sh`脚本来做到这一点。

```
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic test 
```

让我们分析一下我们传递给脚本的参数:

*   `--create`:创建主题的标志
*   `--zookeeper`:通过卡夫卡使用的动物园管理员连接
*   `--replication-factor`:设置复制因子
*   `--partitions`:设置分区数量
*   `--topic`:设置主题名称

在上面的命令中，我们创建了一个单独的分区主题。我们还将复制因子设置为`3`。这意味着数据将被复制到我们所有的代理。

> *注意*:主题的最大复制因子是您运行的代理数量。在这种情况下，我们的最大复制因子为 3。

我们现在可以`describe`主题来深入了解我们新创建的主题:

```
bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic test 
```

这将输出类似于:
的内容

```
Topic:test  PartitionCount:1    ReplicationFactor:3 Configs:
    Topic: test Partition: 0    Leader: 2   Replicas: 2,3,1 Isr: 2,3,1 
```

这解释了我们的主题`test`有一个分区，一个复制因子 3，并且没有非默认配置集。它还显示了我们的一个分区，即分区`0`，其领导者是代理`2`，并且我们有`3`个同步副本。你的领导可能与经纪人`2`不同，但你应该有`3`同步副本。

要了解更多关于分区、副本和同步副本的含义，请查看并阅读我的帖子[Apache Kafka:a Introduction](https://devshawn.com/blog/apache-kafka-introduction/)。

## 产生消息

既然有了卡夫卡的话题，那就给它发点消息吧！我们可以使用`bin`目录中的`kafka-console-producer.sh`脚本来完成这项工作。这是一个从命令行生成消息的便利工具。

使用以下命令运行控制台生成器:

```
bin/kafka-console-producer.sh --broker-list localhost:9091,localhost:9092,localhost:9093 --topic test 
```

我们传递带有参数`--broker-list`的 Kafka 经纪人列表和带有参数`--topic`的主题名称。您现在应该有一个以`>`开始的终端行。在这里，你可以输入一条信息，然后按回车键把它发送给卡夫卡。例如:

```
> hello world, this is my first message
> this is a second message 
```

一旦你发送了一些消息，使用`cmd + c`或`ctrl + c`退出控制台生成器。

## 消费信息

我们已经成功地向我们的 Kafka 主题发送了一些消息，所以我们需要做的最后一件事就是阅读这些消息。我们可以通过使用`bin`目录中的`kafka-console-consumer.sh`脚本来做到这一点。这是一个从命令行使用消息的便利工具。

使用以下命令针对我们的主题运行控制台消费者:

```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9091,localhost:9092,localhost:9093 --topic test --from-beginning 
```

我们将`--bootstrap-server`参数设置为逗号分隔的经纪人列表；这可以是一个或所有的经纪人。为了保持一致性，我通常使用所有的经纪人。我们还将参数`--topic`设置为主题名，并传递`--from-beginning`标志来读取主题中的所有消息。如果您没有通过`--from-beginning`，您将只能看到自启动消费程序以来产生的消息。

您应该会在输出中看到之前发送的消息:

```
hello world, this is my first message
this is a second message 
```

要退出消费者，使用`cmd + c`或`ctrl + c`。

## 结论

恭喜你。您已经成功地启动了一个本地 Kafka 集群，创建了一个主题，用一个控制台生产者向它发送消息，用一个控制台消费者从它那里读取消息。有趣的是，您可以在单独的终端窗口中启动控制台生产者和控制台消费者，并产生更多的消息。然后你就能实时看到信息被消费和打印！太棒了。

您可以通过在各自的终端窗口中使用`cmd + c`或`ctrl + c`来停止 Kafka brokers 和 ZooKeeper 节点。我希望这篇教程能帮助你建立一个本地 Kafka 集群，现在你应该准备好继续你的 Kafka 之旅了！