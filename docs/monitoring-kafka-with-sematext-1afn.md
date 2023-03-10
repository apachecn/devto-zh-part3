# 用语义文本监控卡夫卡

> 原文：<https://dev.to/sematext/monitoring-kafka-with-sematext-1afn>

[![Featured Image](img/c72dbfa9623e6948244b175d8d5e9ce8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pX3phQIo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lcrcxqmbijvdlvnv71ba.jpg)

监视卡夫卡是一项棘手的任务。正如您在第一章[中看到的，Kafka 关键指标监控](https://sematext.com/blog/kafka-metrics-to-monitor/)，Kafka 的设置、调优和操作需要深入了解性能指标，如[消费者延迟](https://sematext.com/blog/kafka-consumer-lag-offsets-monitoring/)，I/O 利用率，垃圾收集等等。 **Sematext 为其他 [Kafka 监控工具](https://sematext.com/blog/kafka-open-source-monitoring-tools/)** 提供了一个极好的替代品，因为它使用起来快捷简单。它还在一个屋檐下托管您的所有基础架构和应用程序指标，以及日志和其他可用于团队共享的数据。

# 语义如何帮助节省时间、工作、&成本

使用 Sematext 进行 Kafka 监控时，您不必做以下几件事:

1.  确定收集哪些指标，忽略哪些指标
2.  给指标贴上有意义的标签
3.  在文档中寻找度量描述，了解每个描述实际上显示了什么
4.  构建图表以将您真正想要的指标分组到同一个图表上，而不是几个单独的图表上
5.  找出每组指标使用的聚合，比如最小值、最大值、平均值或其他值
6.  构建仪表板，将图表与您通常希望一起查看的指标结合起来
7.  设置基本警报规则

以上这些甚至都不是一个完整的故事。你想收集卡夫卡日志吗？如何构建它们？Sematext 会自动为您完成这一切！

在本章中，我们将了解 Sematext 如何在您的基础设施中为 Kafka 和其他技术提供更全面、更易于设置的监控。通过将事件、日志和指标组合在一个集成的[全栈可观察性平台](https://sematext.com/cloud)中，并使用 Sematext [开源监控代理](https://sematext.com/blog/now-open-source-sematext-monitoring-agent/)及其开源集成，您可以监控您的整个基础架构和应用，而不仅仅是您的 Kafka。通过收集、处理和分析日志，您还可以更深入地了解整个软件体系。

# 阿帕奇卡夫卡监控

## 收集阿帕奇卡夫卡指标

Sematext Kafka integration 为 JVM、消费者延迟、控制器、副本、请求等收集了 [168 个不同的 Kafka 指标](https://sematext.com/docs/integration/kafka/)。Sematext 维护和支持[官方 Kafka 监控集成](https://github.com/sematext/sematext-agent-integrations/tree/master/kafka)。关于 [Sematext Kafka 集成](https://sematext.com/integrations/kafka-monitoring/)的另一个很酷的事实是它是可定制的和开源的。总之，您不需要配置代理来收集指标，这将节省大量时间！

## 安装监控代理

设置监控代理不到 5 分钟:

1.  在[集成/概述](https://apps.sematext.com/ui/monitoring-create)中创建一个 Kafka 应用程序(注意，我们有 Sematext 云的实例在美国和欧洲的[数据中心运行)。这将允许您安装代理并控制对监控和日志数据的访问。《Sematext 云指南》中的“什么是 App”这篇短文有更多详细信息。](https://apps.sematext.com/ui/monitoring-create)
2.  命名您的 Kafka 监控应用程序，如果您也想收集 Kafka 日志，请创建一个日志应用程序。
3.  根据 UI 中显示的[设置说明](https://apps.sematext.com/ui/howto/Kafka/overview)安装 Sematext 代理。

[![App Create](img/7329640ce5b6408490d33558b1c8679d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8-VnLF4A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://sematext.com/wp-content/uploads/2019/04/Monitoring-Kafka-with-SC-App-Create.gif)

例如，在 Ubuntu 上，使用以下命令添加 Sematext Linux 包:

```
echo "deb http://pub-repo.sematext.com/ubuntu sematext main" | sudo tee /etc/apt/sources.list.d/sematext.list > /dev/null
wget -O - https://pub-repo.sematext.com/ubuntu/sematext.gpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install spm-client 
```

然后在每个 Kafka 组件上设置 Kafka 监控——代理、生产者和消费者。

在 Kafka Broker 上设置 Kafka 监控就像运行以下命令一样简单:

```
sudo bash /opt/spm/bin/setup-sematext  
    --monitoring-token <your-monitoring-token-goes-here>  
    --app-type kafka  
    --app-subtype kafka-broker  
    --agent-type javaagent  
    --infra-token <your-infra-monitoring-token-goes-here> 
```

用上面的命令准备好 Kafka Broker 之后，你需要在$ Kafka _ HOME/bin/Kafka-server-start . sh 中的 KAFKA_JMX_OPTS 中添加下面一行。如果你没有 KAFKA_JMX_OPTS，你需要创建它:

```
-Dcom.sun.management.jmxremote -javaagent:/opt/spm/spm-monitor/lib/spm-monitor-generic.jar=<your-monitoring-token-goes-here>:kafka-broker:default 
```

在每个 Kafka 生产者上设置 Kafka 监控就像在代理上设置一样简单。您需要运行以下命令(请记住，Sematext Cloud 目前支持基于 Java 和 Scala 的生成器):

```
sudo bash /opt/spm/bin/setup-sematext  
    --monitoring-token <your-monitoring-token-goes-here>  
    --app-type kafka  
    --app-subtype kafka-producer  
    --agent-type javaagent  
    --infra-token <your-infra-monitoring-token-goes-here> 
```

在使用上面的命令准备好每个 Kafka 生成器之后，您需要将下面一行添加到 Kafka 生成器启动参数中:

```
-Dcom.sun.management.jmxremote -javaagent:/opt/spm/spm-monitor/lib/spm-monitor-generic.jar=<your-monitoring-token-goes-here>:kafka-producer:default 
```

最后，需要通过运行以下命令来配置对每个 Kafka 消费者的监控:

```
sudo bash /opt/spm/bin/setup-sematext  
    --monitoring-token <your-monitoring-token-goes-here>  
    --app-type kafka  
    --app-subtype kafka-consumer  
    --agent-type javaagent  
    --infra-token <your-infra-monitoring-token-goes-here> 
```

在使用上面的命令准备好每个 Kafka 消费程序之后，您需要将下面一行添加到 Kafka 消费程序启动参数中:

```
-Dcom.sun.management.jmxremote -javaagent:/opt/spm/spm-monitor/lib/spm-monitor-generic.jar=<your-monitoring-token-goes-here>:kafka-consumer:default 
```

**请记住重启每个代理、消费者和生产者，以使这些更改生效**。去喝一杯，但要快，卡夫卡指数将在一分钟内出现在你的图表中！

## 卡夫卡监控仪表板

当您打开 Kafka 应用程序时，您会发现一组预定义的仪表板，这些仪表板在预定义的图表中组织了 70 多个 Kafka 指标和通用[服务器指标](https://sematext.com/server-monitoring/)，这些指标组合成一组直观组织的监控仪表板。

1.  所有关键 Kafka 指标的图表概览
2.  操作系统指标，如 CPU、内存、网络、磁盘使用等。
3.  卡夫卡指标:
    *   **消费者滞后** - [消费者滞后，静态经纪人滞后](https://sematext.com/blog/kafka-consumer-lag-offsets-monitoring/)
    *   **分区** -代理分区、领导者分区、离线分区、复制分区下
    *   **领袖选举** -经纪人领袖选举，经纪人不洁领袖选举，经纪人领袖选举时间
    *   **活动控制器** -由主机代理活动控制器
    *   **ISR/日志刷新** -代理 ISR 收缩和扩展，代理日志刷新，代理日志刷新次数
    *   **日志清理器** -代理日志清理器缓冲区利用率、代理日志清理器工作时间、代理日志清理器重新复制
    *   **队列/过期时间** -代理响应队列，代理请求队列
    *   **副本** -代理副本最大延迟、代理副本最小获取、代理首选副本不平衡
    *   **请求** -代理请求本地时间、代理请求远程时间、代理请求队列时间、代理响应队列时间、代理响应发送时间、代理请求数量
    *   **主题** -代理主题消息传入，代理主题传入/传出，代理主题被拒绝，代理主题获取/产生请求失败
    *   **主题分区** -代理日志段、代理日志大小、代理日志偏移量增加、代理分区复制不足
    *   **生产者批次/缓冲区** -生产者批次大小、生产者最大批次大小、压缩率、生产者缓冲区可用字节、生产者缓冲池等待比率、生产者缓冲区总字节
    *   **生产者通信** -生产者 I/O 时间、生产者 I/O 比率、生产者 I/O 等待时间、生产者 I/O 等待比率、生产者连接计数、生产者连接创建率、生产者连接关闭率、生产者网络 I/O 率
    *   **生产者请求/记录** -生产者记录排队时间、生产者记录发送率、生产者记录重试率、生产者记录错误率、每个请求的生产者记录、记录大小、进行中的生产者请求、生产者等待线程
    *   **生产者节点** -生产者节点输入字节率、生产者节点输出字节率、生产者节点请求等待时间、生产者节点请求最大等待时间、生产者节点请求率、生产者节点响应率、生产者请求大小、生产者请求最大大小
    *   **生产者主题** -生产者主题压缩率、生产者主题字节率、生产者记录发送率、生产者记录重试率、生产者记录错误率
    *   **消费者提取器** -消费者响应，消费者提取器字节，消费者响应字节
    *   **消费者通信** -消费者 I/O 时间、消费者 I/O 比率、消费者 I/O 等待时间、消费者 I/O 等待比率、消费者连接数、消费者连接创建率、消费者连接关闭率、消费者网络 I/O 率
    *   **消费者提取器** -记录消耗率、每个请求的消费者记录、消费者提取延迟、消费者提取率、消耗的字节率、消费者提取平均大小、消费者节流最大时间
    *   **消费者协调器** -消费者分配的分区、消费者心跳响应最大时间、消费者心跳速率、消费者加入时间、消费者加入最大时间、消费者同步时间、消费者最大同步时间、消费者加入率、消费者同步率
    *   **消费者节点** -消费者节点字节速率、消费者节点输出字节速率、消费者节点请求等待时间、消费者节点请求最大等待时间、消费者节点请求速率、消费者节点响应速率、消费者节点请求大小、消费者节点请求最大大小

[![Kafka Dashboard](img/cff58d471f9337aac0d583107f643c28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QupXMWW6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/06cey440uow9zce9m6qu.png)

## 设置卡夫卡提醒

为了节省您的时间，Sematext 会自动创建一组默认的警报规则，例如磁盘空间不足警报。您可以针对任何指标创建附加警报。查看[提醒&事件指南](https://sematext.com/docs/guide/alerts-and-events-guide/)或观看语义云中的[提醒，了解更多关于创建自定义提醒的详细信息。](https://www.youtube.com/watch?v=WE9xAUud28o)

Sematext 中有三种类型的警报:

1.  **心跳警报**，当 Kafka 经纪人停机时，它会通知您
2.  传统的**基于阈值的警报**，当指标值超过预定义的阈值时会通知您
3.  基于统计**异常检测**的警报，当指标值突然改变并偏离基线时会通知您

让我向您展示如何为 Kafka 指标创建一个警报规则。你在下面看到的**消费者滞后**图显示了一个峰值。我们的基线是一个低的**消费者滞后**值，接近于 **0** ，但是有一个峰值，它跳到了超过 100 的**。**要在指标上创建警报规则，我们需要转到图表右上角的下拉菜单，然后选择“Create alert”**。警报规则继承选定的过滤器。您可以选择各种通知选项，如电子邮件或配置的[通知挂钩](https://sematext.com/docs/alerts/#alert-integrations)，如 PagerDuty、Slack、VictorOps、BigPanda、OpsGenie、Pusher 和通用 webhooks 等。通过异常检测、观察给定时间窗口内的指标变化或使用传统的基于阈值的警报来触发警报。
T13![Create Alert](img/358b9de732a53f3535838902c3dc2abc.png)T15】**

# 卡夫卡日志

## 运送卡夫卡日志

由于在一个平台中拥有[日志和指标](https://sematext.com/metrics-and-logs/)使得故障排除更加简单和快速，所以让我们也发布 Kafka 日志。您可以从许多[日志运送器](https://sematext.com/docs/integration/#logging)中进行选择，但是我们将使用[日志代理](https://sematext.com/logagent/)，因为它是轻量级的，易于设置，并且它可以解析和构造开箱即用的日志。日志解析器提取时间戳、严重性和消息。对于查询跟踪，日志解析器还提取唯一的查询 ID 来对与查询执行相关的日志进行分组。让我们设置它，然后[开始从 Kafka](https://sematext.com/blog/logagent-meets-apache-kafka/) 运送日志！

1.  创建日志应用程序以获取应用程序令牌
2.  安装 Logagent npm 软件包

```
sudo npm i -g @sematext/logagent 
```

如果没有 Node.js，可以轻松安装。例如在 Debian/Ubuntu 上:

```
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs 
```

[通过指定日志令牌和 Kafka 日志文件的路径来安装 Logagent 服务](https://sematext.com/docs/logagent/installation/#install-service-linux-mac-os-x)。您可以使用-g `var/log/kafka/server.log`仅从 Kafka 服务器发送日志，或者使用-g `var/log/kafka/*.log`发送所有与 Kafka 相关的日志。如果你在同一个服务器上运行其他服务，比如 ZooKeeper 或者 Elasticsearch，考虑使用-g `var/log/**/*.log`发送所有日志。默认设置将从/var/log/* */* 发送所有日志。未指定-g 参数时记录日志。

Logagent 检测 init 系统并安装 Systemd 或 Upstart 服务脚本。在 Mac OS X 上，它创建了一个 launchd 服务。简单运行:

```
sudo logagent-setup -i YOUR_LOGS_TOKEN -g ‘var/log/kafka/*.log`
#for EU region: 
#sudo logagent-setup -i LOGS_TOKEN 
#-u logsene-receiver.eu.sematext.com
#-g ‘var/log/kafka/*.log` 
```

安装脚本在/etc/sematext/logagent.conf 中生成配置文件，并将 logagent 作为系统服务启动。如果您在容器中运行 Kafka，请查看如何[为容器日志](https://sematext.com/docs/logagent/installation-docker/)设置 Logagent。

## 日志搜索和仪表盘

一旦您的日志存储在 Sematext 中，您就可以在故障排除时搜索和过滤它们，保存您经常运行的查询，基于这些查询创建警报，以及[创建您自己的日志仪表板](https://sematext.com/product-updates/#/2018/custom-reports-for-monitoring-logs-apps)。
T3![Kafka Logs](img/985911366abe2c73c2cc285d8d6b7667.png)T5】

## 日志搜索语法

如果你知道如何用谷歌搜索，你就会知道如何在 Sematext Cloud 中搜索你的日志。

1.  使用 AND、OR、NOT 运算符，例如(错误或警告)NOT exception
2.  将 AND、OR、NOT 子句分组，例如消息:(异常或错误或超时)和严重性:(错误或警告)
3.  不喜欢布尔人？使用+和–来包含和排除–例如+message:error-message:time out-host:db1 . example . com
4.  明确使用字段引用，例如消息:超时
5.  需要短语搜索吗？使用引号–例如消息:“致命错误”

在浏览日志时，您可能会发现自己一遍又一遍地运行相同的搜索。为了解决这个问题，Sematext 允许您保存查询，这样您就可以快速执行它们，而不必重新键入它们。查看使用[日志进行故障排除指南](https://sematext.com/docs/guide/logs-guide/)以及它如何让您的生活更轻松。

## 卡夫卡日志上的警示

要创建日志警报，首先要运行一个查询，该查询与我们希望获得警报的日志事件相匹配。在搜索框中输入查询，然后单击过时的软盘图标。
[![Kafka Save Query](img/efc78076b806e5222e91e00cac0048b9.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--GqG4qJer--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2019/04/Kafka-Save-Query.png)

与度量警报规则的设置类似，我们可以根据警报查询返回的匹配日志事件的数量来定义基于阈值的警报或异常检测警报。
[![Kafka Save Query](img/54d1af168ddfa0b81ce51b032ead851e.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--vS8E6Jef--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2019/04/Kafka-Save-Query-2.png)

您可以观看一个简短的视频来了解更多关于 Sematext Cloud 中的[警报的信息，或者在 docs](https://www.youtube.com/watch?v=WE9xAUud28o) 中跳转到我们的[警报指南来阅读更多关于为日志和指标创建警报的信息。](https://sematext.com/docs/guide/alerts-and-events-guide/)

## 卡夫卡度量与对数的相关性

典型的故障排除工作流程从检测指标中的峰值开始，然后深入日志以找到问题的根本原因。Sematext 让这变得非常简单快捷。您的指标和日志在同一个屋檐下。日志集中，搜索速度快，强大的[日志搜索语法](https://sematext.com/docs/logs/search-syntax/)使用简单。指标和日志的关联只需点击一下鼠标。
T3![Kafka Logs And Metrics](img/830bfc3284c8c6a5747addd60c2362a8.png)T5】

# 全栈可观测性对于卡夫卡&的朋友来说

卡夫卡最好的朋友是[阿帕奇动物园管理员](https://zookeeper.apache.org/)。卡夫卡需要 ZooKeeper 来操作，处理分区，进行领袖选举。同时监控 ZooKeeper 和 Kafka 是至关重要的，这样可以关联两者的指标，并完全观察 Kafka 的操作。Kafka 也喜欢你的环境中向它发送数据或从中读取数据的每一块。 **Sematext Cloud 允许您轻松监控您的 Kafka 生产者和 Kafka 消费者以及相关的应用程序和基础设施，让您不仅可以了解 Kafka，还可以了解整个系统**。
[![Kafka Custom Dashboard](img/861b24c5c29c147d023492746d4f1c76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jvEVRARH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zea7shid65f67fy5zte0.png)

# 用 Sematext 监视卡夫卡

监控 Kafka 包括确定关键指标，以收集指标和日志，同时以有意义的方式连接一切。在本章中，我们已经向您展示了如何在一个地方监控 Kafka 指标和日志。我们使用现成的和定制的仪表板、指标关联、日志关联、异常检测和警报。通过其他[开源集成](https://sematext.com/blog/now-open-source-sematext-monitoring-agent/)，如 [Elasticsearch](https://sematext.com/docs/integration/elasticsearch/) 或 [ZooKeeper](https://sematext.com/docs/integration/zookeeper/) ，您可以轻松地开始监控 Kafka 以及基础架构中所有技术的指标、日志和分布式请求跟踪。用 Sematext 更深入地了解卡夫卡非常酷，而且节省了大量时间。今天你可以用一个[免费试用](https://apps.sematext.com/ui/registration) 来亲自检验一下。也有一些甜蜜的免费层，如果你是一家初创公司，还有不错的交易！