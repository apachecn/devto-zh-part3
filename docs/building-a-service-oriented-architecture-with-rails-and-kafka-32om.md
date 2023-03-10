# 用 Rails 和 Kafka 构建面向服务的架构

> 原文：<https://dev.to/heroku/building-a-service-oriented-architecture-with-rails-and-kafka-32om>

*这篇博客文章改编自 Stella Cotton 在 RailsConf 2018 上的演讲，题为“[所以你有了一个卡夫卡](https://www.youtube.com/watch?v=Rzl4O1oaVy8)*

[https://www.youtube.com/embed/Rzl4O1oaVy8](https://www.youtube.com/embed/Rzl4O1oaVy8)

近年来，将软件设计成服务的集合，而不是一个单一的整体代码库，已经成为构建应用程序的一种流行方式。在这篇文章中，我们将学习 Kafka 的基础知识，以及如何使用它的事件驱动过程来支持 Rails 服务。我们还将讨论事件驱动的 Rails 服务在监控和伸缩方面可能面临的实际考虑和操作挑战。

## 卡夫卡是什么？

假设你想知道更多关于你的用户在你的平台上参与的信息:他们访问的页面，他们点击的按钮，等等。一个足够受欢迎的应用程序可以产生数十亿个事件，至少可以说，将如此大量的数据发送到分析服务可能是一个挑战。

进入 [Kafka](https://kafka.apache.org/) ，它是需要实时数据流的 web 应用程序不可或缺的一部分。Kafka 在生成事件的*生产者*和读取这些事件的*消费者*之间提供容错通信。任何一个应用程序中都可以有多个生产者和消费者。在 Kafka 中，每个事件都持续一段配置好的时间，因此多个消费者可以一遍又一遍地阅读同一个事件。Kafka 集群由几个*代理*组成，对于任何运行 Kafka 的实例来说，这只是一个花哨的名字。

[![Slide16](img/4ac060998175ea9da50b97e63f9a973b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A7kl-nQ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1547087126-shaved-slide16.png)

Kafka 的关键性能特征之一是它可以处理极高吞吐量的事件。传统的企业排队系统，如 AMQP，让事件基础设施本身跟踪每个消费者已经处理的事件。随着用户数量的增加，基础设施将承受更大的负载，因为它需要跟踪越来越多的状态。甚至与消费者达成协议也不是小事。一旦消息通过网络发送，代理应该将消息标记为`"done"`吗？如果消费者宕机，需要代理重新发送事件，会发生什么情况？

另一方面，Kafka 经纪人不跟踪任何消费者。消费者服务本身负责告诉 Kafka 它在事件处理流中的位置，以及它想从 Kafka 那里得到什么。消费者可以从中间开始，向卡夫卡提供一个特定事件的偏移来阅读，或者它可以从最开始甚至最结尾开始。消费者读取事件数据的能力是 O(1)的常数时间；随着更多事件的到来，从流中查找信息的时间不会改变。

Kafka 还具有可扩展和容错的特性。它作为一个集群在一个或多个服务器上运行，这些服务器可以通过添加更多的机器来横向扩展。数据本身被写入磁盘，然后跨多个代理进行复制。关于可伸缩性的具体数字，像网飞、LinkedIn 和微软这样的公司每天都通过他们的 Kafka 集群发送超过一万亿条消息！

## 在铁轨上架起卡夫卡

Heroku 提供了一个 Kafka 集群插件，可以在任何环境中使用。对于 Ruby 应用程序，我们推荐使用 [ruby-kafka](https://github.com/zendesk/ruby-kafka) gem 作为真实世界的用例。一个最简单的实现只需要你为你的经纪人提供主机名:

```
# config/initializers/kafka_producer.rb
require "kafka"

# Configure the Kafka client with the broker hosts and the Rails
# logger.
$kafka = Kafka.new(["kafka1:9092", "kafka2:9092"], logger: Rails.logger)

# Set up an asynchronous producer that delivers its buffered messages
# every ten seconds:
$kafka_producer = $kafka.async_producer(
  delivery_interval: 10,
)

# Make sure to shut down the producer when exiting.
at_exit { $kafka_producer.shutdown } 
```

设置好 Rails 初始化器后，您可以开始使用 gem 发送事件有效载荷。由于发送事件的异步行为，我们可以在 web 执行的线程之外编写一个事件，就像这样:

```
class OrdersController < ApplicationController
  def create
    @comment = Order.create!(params)

    $kafka_producer.produce(order.to_json, topic: "user_event", partition_key: user.id)
  end
end 
```

我们将在下面更多地讨论 Kafka 的序列化格式，但是在这个场景中，我们使用的是传统的 JSON。`topic`关键字参数指的是卡夫卡要写事件的日志。主题本身被划分成*分区*，这允许您将特定主题中的数据“分割”到多个代理中，以实现可伸缩性和可靠性。每个主题有两个或更多的分区是一个好主意，这样如果一个分区出现故障，您的事件仍然可以被写入和使用。Kafka 保证事件在一个分区内按顺序传递，而不是在整个主题内。如果事件的顺序很重要，那么传入 partition_key 将确保特定类型的所有事件都进入同一个分区。

## 卡夫卡为您服务

使 Kafka 对事件管道系统有价值的一些属性也使它成为服务间 RPC 的一个非常有趣的容错替代品。让我们用一个电子商务应用程序的例子来说明这在实践中意味着什么:

```
def create_order
  create_order_record
  charge_credit_card # call to Payments Service
  send_confirmation_email # call to Email Service
end 
```

假设当用户下订单时，这个`create_order`方法将被执行。它将创建一个订单记录，从用户的信用卡中收费，并发送一封确认电子邮件。最后两个步骤已经被提取到服务中。

[![Slide 50](img/c8bcc45c679a2db4ed5154b7c68738dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C0Q5Iq6j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1547087153-shaved-slide50.png)

这种设置的一个挑战是上游服务负责监控下游的可用性。如果电子邮件系统有一天真的很糟糕，上游服务负责知道该电子邮件服务是否可用。如果它不可用，它还需要负责重试任何失败的请求。在这种情况下，卡夫卡的事件流会有什么帮助呢？让我们来看看:

[![Slide 52](img/12e7fba32fbadb9d67bd2765bcebdace.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yj2FVJKP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1547087167-shaved-slide52.png)

在这个面向事件的世界中，上游服务可以向 Kafka 写入一个事件，表明订单已创建。因为 Kafka 有“至少一次”的保证，所以该事件将被写入 Kafka 至少一次，并且将可供下游消费者读取。如果电子邮件服务关闭，事件仍然会持续存在，以供其使用。当下游电子邮件服务恢复在线时，它可以继续按顺序处理它错过的事件。

面向 RPC 的体系结构的另一个挑战是，在日益复杂的系统中，集成一个新的下游服务也意味着改变一个上游服务。假设您想要集成一个新的服务，该服务在订单创建时启动一个履行流程。在 RPC 世界中，上游服务需要向新的实现服务添加新的 API 调用。但是在面向事件的世界中，一旦在 Kafka 中创建了事件，您就可以在执行服务中添加一个新的消费者来消费订单。

[![Slide 54](img/c9d18b6efd79c6ee8cf27a38f4b78bb2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kPej2l6i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1547087179-shaved-slide54.png)

## 在面向服务的架构中整合事件

在一篇名为“[你所说的“事件驱动”](https://martinfowler.com/articles/201701-event-driven.html)是什么意思”的博客文章中，Martin Fowler 讨论了围绕“事件驱动应用”的困惑当开发人员讨论这些系统时，他们实际上是在谈论不同种类的应用程序。为了让大家共同理解什么是事件驱动系统*，他已经开始定义一些架构模式。*

 *让我们来快速了解一下这些模式是什么！如果你想了解更多，请查看[他在 GOTO Chicago 2017](https://www.youtube.com/watch?v=STKCRSUsyP0) 上的主题演讲，深入探讨这些问题。

### 事件通知

福勒谈到的第一种模式叫做*事件通知*。在这个场景中，一个服务简单地用最少的信息通知下游服务发生了一个事件:

```
{  "event":  "order_created",  "published_at":  "2016-03-15T16:35:04Z"  } 
```

如果下游服务需要关于所发生的事情的更多信息，它将需要向上游发出网络调用来检索它。

### 事件承载状态转移

第二种模式称为*事件携带状态转移*。在这种设计中，上游服务用额外的信息来扩充事件，这样下游消费者可以保留该数据的本地副本，而不必通过网络调用从上游服务中检索数据:

```
{  "event":  "order_created",  "order":  {  "order_id":  98765,  "size":  "medium",  "color":  "blue"  },  "published_at":  "2016-03-15T16:35:04Z"  } 
```

### 事件源

福勒的第三个设计是基于事件的架构。这种实现表明，不仅服务之间的每一次通信都是由一个事件引发的，而且通过存储一个事件的表示，您可以删除所有的数据库，并且仍然可以通过重放该事件流来完全重建应用程序的状态。换句话说，每个有效负载都封装了您的系统在任何时刻的确切状态。

这种方法的一个巨大挑战是，随着时间的推移，代码会发生变化。未来对下游服务的 API 调用可能会返回一组与以前不同的数据，这使得重新计算当时的状态变得很困难。

### 命令查询责任分离

最后提到的模式是*命令查询责任分离*，或 CQRS。这里的想法是，您可能需要对记录执行的操作——创建、读取、更新——被划分到不同的域中。这意味着一个服务负责写，另一个服务负责读。在面向事件的架构中，您经常会看到事件系统隐藏在图中实际编写命令的地方。

[![Slide 72](img/30634507cd093352157115e67dff8bb9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--13kR0Gqg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1547087195-shaved-slide72.png)

writer 服务将读取事件流，处理命令，并将它们存储到写数据库中。任何查询都发生在只读数据库上。将读取和写入逻辑分离到两个不同的服务中会增加复杂性，但它确实允许您分别为这些系统优化性能。

## 实际考虑

让我们谈谈在将 Kafka 集成到面向服务的应用程序中时可能会遇到的一些实际问题。

首先要考虑的是慢消费者。在事件驱动的系统中，您的服务需要能够像上游服务产生事件一样快地处理事件。否则，它们会慢慢落后，没有任何问题的迹象，因为不会有任何超时或呼叫失败。可以识别超时的一个地方是与 Kafka 代理的套接字连接。如果服务处理事件的速度不够快，连接就会超时，重新建立连接会增加额外的时间成本，因为创建这些套接字的成本很高。

如果一个消费者很慢，你如何加快它的速度？对于卡夫卡来说，你可以增加你的消费群体中的消费者数量，这样你就可以并行处理更多的事件。您希望每个服务至少运行两个消费者进程，这样，如果一个发生故障，任何其他故障分区都可以被重新分配。本质上，您可以在尽可能多的消费者之间并行化工作，因为您有主题分区。(和任何扩展问题一样，你不能永远只添加消费者；最终，您将触及共享资源(如数据库)的扩展极限。)

当一个事件被添加到队列中时，拥有关于您落后多远的度量和警报也是非常有价值的。ruby-kafka 配备了 ActiveSupport 通知，但是它也有自动包含的 StatsD 和 Datadog 报告器。您可以使用这些来报告在添加事件时您是否落后了。ruby-kafka gem 甚至为[提供了监控](https://github.com/zendesk/ruby-kafka#what-to-monitor)的推荐指标列表！

用 Kafka 构建系统的另一个方面是为失败设计你的消费者。卡夫卡保证至少发一次事件；消息根本不会被发送出去。但是你需要设计消费者来期待重复的事件。一种方法是始终依靠`UPSERT`向数据库添加新记录。如果已经存在具有相同属性的记录，则该调用基本上是无效的。或者，您可以为每个事件包含一个唯一的标识符，并跳过对以前已经看到的事件的操作。

## 有效载荷格式

卡夫卡的一个令人惊讶的方面是它对数据非常宽容的态度。你可以用字节向它发送任何东西，它会简单地发送给消费者，不需要任何验证。这个特性使得它的使用非常灵活，因为您不需要采用特定的格式。但是当上游服务决定改变它产生的事件时会发生什么呢？如果您只是更改事件负载，那么您的一个下游消费者很有可能会崩溃。

在开始采用事件驱动的体系结构之前，选择一种数据格式，并评估它如何帮助您注册模式并随着时间的推移不断发展。在真正实现模式之前，考虑模式的验证和演化要容易得多。

一种可以使用的格式当然是 JSON，web 的格式。它是人类可读的，基本上在每种编程语言中都得到支持。但是也有一些缺点。首先，JSON 有效负载的实际大小可能非常大。有效负载要求您发送灵活的键值对，但也经常在每个事件中重复。有效载荷中没有内置的文档，因此，给定一个值，您可能不知道它是什么意思。模式演变也是一个挑战，因为如果需要重命名字段，没有内置的支持来将一个键别名化为另一个键。

构建 Apache Kafka 的团队 Confluent 推荐使用 [Avro](https://avro.apache.org/docs/current/) 作为数据序列化系统。数据以二进制形式发送，所以它是*而不是*人类可读的。但是好处是有更健壮的模式支持。一个完整的 Avro 对象包括它的模式*和*数据。Avro 支持简单类型，比如整数，也支持复杂类型，比如日期。它还将文档嵌入到模式中，这允许您理解字段在系统中的作用。它提供了内置的工具，帮助您随着时间的推移以向后兼容的方式发展您的模式。

avro-builder 是由 Salsify 创建的一个宝石，它提供了一个非常红宝石般的 DSL 来帮助你创建你的模式。如果你想了解更多关于 avro 的信息，[sals ify 工程博客](https://www.salsify.com/blog/engineering/adventures-in-avro)有一篇关于 Avro 和 avro-builder 的非常棒的文章！

## 更多信息

如果你想了解更多关于我们如何运行托管的 Kafka 产品，或者我们如何在 Heroku 内部使用 Kafka，我们有两个其他 Heroku 工程师的演讲，你可以看看！

Jeff Chao 在 DataEngConf SF '17 上的演讲题为“[超越 50，000 个分区:Heroku 如何大规模运营并推动 Kafka 的极限](https://www.datacouncil.ai/beyond-50k-partitions-how-heroku-pushes-the-limits-of-kafka-at-scale)”，而 Pavel Pravosud 在 Dreamforce '16 上的演讲则是“[喂养 Kafka:我们如何构建 Heroku 的实时平台事件流](https://www.salesforce.com/video/302281/)”尽情享受吧！*