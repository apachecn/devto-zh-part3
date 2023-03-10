# 用语义监控弹性搜索

> 原文：<https://dev.to/sematext/monitoring-elasticsearch-with-sematext-2lcd>

[![Elasticsearch monitoring with sematext](img/97bdc92588b4679d646f407f27cdcd82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e2LCD_uN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2019/03/New_Elasticsearch-monitoring-part-4-1024x560.jpg)

如 [Elasticsearch 关键指标](https://sematext.com/blog/top-10-elasticsearch-metrics-to-watch/)所示，Elasticsearch 的设置、调优和操作需要深入了解性能指标，如索引率、查询率、查询延迟、合并次数等。 [Sematext](https://sematext.com/) 为其他弹性搜索监控工具提供了极佳的替代方案。

监控 Elasticsearch 的开源工具是免费的，但你的时间不是。相对来说，它们相当贵。因此， [Sematext](https://sematext.com/cloud/) [Cloud](https://sematext.com/cloud/) 旨在为您节省时间、精力和头发！

## sema text 如何帮你节省时间

以下是在使用 Sematext 进行弹性搜索**监控时，您需要 **而不是** **做的几件事情:

*   确定收集哪些指标，忽略哪些指标
*   给指标贴上有意义的标签
*   在文档中寻找指标描述，这样你就知道每一个指标实际上显示了什么
*   构建图表以将您真正想要的指标分组到同一个图表上，而不是几个单独的图表上
*   确定每组指标使用哪种聚合(最少？麦克斯。平均？还有别的吗？)
*   构建仪表板，将图表与您通常希望一起查看的指标结合起来
*   设置基本警报规则

以上这些甚至都不是一个完整的故事。你想收集 Elasticsearch 日志吗？如何构建它们？Sematext 会自动为您完成这一切！

在本帖中，我们将探讨 Sematext 如何为您的基础设施中的 Elasticsearch 和其他技术提供更全面且易于设置的监控。通过将事件、日志和指标组合在一个集成的[全栈可观察性平台](https://sematext.com/cloud)中，并使用 Sematext [开源监控代理](https://sematext.com/blog/now-open-source-sematext-monitoring-agent/)及其集成(也是开源的)，您可以监控您的整个基础架构和应用，而不仅仅是您的弹性搜索集群。通过收集、处理和分析日志，您还可以更深入地了解整个软件体系。

## **弹力搜索监控**

### **收集弹性搜索指标**

sema text Elasticsearch integration 为 JVM、索引性能、集群健康、查询性能等收集了超过 100 个不同的 elastic search 指标。Sematext 维护和支持[官方弹性搜索监控集成](https://github.com/sematext/sematext-agent-integrations/tree/master/clickhouse)。此外，Sematext Elasticsearch 集成是可定制和开源的。

底线:您不需要为度量收集配置代理，这是第一个巨大的时间节省！

### **安装监控代理**

设置监控代理不到 5 分钟:

1.  在[集成/概述](https://apps.sematext.com/ui/monitoring-create)(或 [Sematext Cloud Europe](https://apps.eu.sematext.com/ui/monitoring-create) )中创建一个 Elasticsearch 应用。这将允许您安装代理并控制对监控和日志数据的访问。短片[sema text Cloud](https://www.youtube.com/watch?v=TR_qXdR8DVk&index=14&list=PLT_fd32OFYpfLBFZz_HiafnqjdlTth1NS)中的 App 是什么有更多细节。
2.  命名你的 Elasticsearch 监控应用程序，如果你也想收集 Elasticsearch 日志，创建一个日志应用程序。
3.  根据 UI 中显示的[设置说明](https://apps.sematext.com/ui/howto/ClickHouse/overview)安装 Sematext 代理。

[![app creation](img/2e59812c64001cd5ec18799337b0bb3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LF0Ilbqb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://sematext.com/wp-content/uploads/2019/04/create-elasticsearch-app.gif)

Sematext Cloud 中的应用创建和设置说明

例如，在 Ubuntu 上，使用以下命令添加 Sematext Linux 包:

```
echo "deb http://pub-repo.sematext.com/ubuntu sematext main" | sudo tee /etc/apt/sources.list.d/sematext.list > /dev/null
wget -O - https://pub-repo.sematext.com/ubuntu/sematext.gpg.key | sudo apt-key add -
sudo apt-get updatesudo apt-get install spm-client 
```

然后通过提供 Elasticsearch 服务器连接详情来设置 Elasticsearch 监控:

```
sudo bash /opt/spm/bin/setup-sematext --monitoring-token --app-type elasticsearch \
--agent-type standalone \
--SPM_MONITOR_ES_NODE_HOSTPORT 'localhost:9200' \
--infra-token 
```

如果您使用 HTTPS 和基本认证来保护 Elasticsearch，您可以在命令中添加以下参数:

```
--SPM_MONITOR_ES_NODE_BASICAUTH_USERNAME userName
--SPM_MONITOR_ES_NODE_BASICAUTH_PASSWORD passWord 
```

此外，您需要将 HTTPS 指定为协议 SPM_MONITOR_ES_NODE_HOSTPORT，如完整的 setup 命令所示:

```
sudo bash /opt/spm/bin/setup-sematext --monitoring-token <your-token-goes-here> \
--app-type elasticsearch \
--agent-type standalone \
--SPM_MONITOR_ES_NODE_HOSTPORT 'https://localhost:9200' \
--infra-token <your-token-goes-here> \
--SPM_MONITOR_ES_NODE_BASICAUTH_USERNAME userName \
--SPM_MONITOR_ES_NODE_BASICAUTH_PASSWORD passWord 
```

去喝一杯，但是要快！弹性搜索指标将在一分钟内开始出现在您的图表中。

### **弹力搜索监控仪表盘**

当您打开 Elasticsearch 应用程序时，您会发现一组预定义的仪表板，这些仪表板将 100 多个 Elasticsearch 指标和常规[服务器监控](https://sematext.com/server-monitoring/)以预定义图表的形式组织成一组直观组织的监控仪表板:

*   所有关键弹性搜索指标的图表概述
*   操作系统指标，如 CPU、内存、网络、磁盘使用等。
*   垃圾收集、JVM 内存、JVM 线程和 JVM 打开文件的 Java 虚拟机指标
*   Elasticsearch metrics
    *   **集群健康**:弹性搜索节点的数量和分片状态(活动，重定位，初始化，..)
    *   **碎片统计**:碎片的数量，每个索引的碎片状态
    *   **索引统计**:索引文档的数量、磁盘大小、索引率、合并率、合并文档
    *   **搜索**:请求速率、查询和获取延迟、实时-获取延迟
    *   **线程池**:每个池的线程数量，线程池大小
    *   **断路器**:现场数据统计，请求大小统计
    *   **连接**:连接的套接字、节点-节点-传输统计、TCP 套接字和流量统计

## T2】

[*语义云弹性搜索关键指标*](http://www.sematext.com/cloud)

### **为弹性搜索指标设置预警**

为了节省您的时间，Sematext 会自动创建一组默认的警报规则，例如磁盘空间不足警报。您可以针对任何指标创建附加警报。更多详细信息，请查看语义云中的[警报。](https://www.youtube.com/watch?v=WE9xAUud28o)

### **对弹性搜索指标发出警报**

Sematext 中有三种类型的警报:

*   **心跳警报**，当 Elasticsearch 数据库服务器关闭时，它会通知您
*   传统的**基于阈值的警报**，当指标值超过预定义的阈值时会通知您
*   基于统计**异常检测**的警报，当指标值突然改变并偏离基线时会通知您

让我们看看如何在下面的动画中为 Elasticsearch 指标创建一些警报规则。请求查询计数图表显示峰值。我们通常有多达 100 个请求，但我们看到它可以跳到 600 多个请求。要在指标上创建警报规则，我们需要转到图表右上角的下拉菜单，然后选择“Create alert”。警报规则应用当前视图中的过滤器，您可以选择各种通知选项，如电子邮件或配置的[通知挂钩](https://sematext.com/docs/alerts/#alert-integrations)(page duty、Slack、VictorOps、BigPanda、OpsGenie、Pusher、generic webhooks 等。).通过异常检测、观察给定时间窗口内的指标变化或使用传统的基于阈值的警报来触发警报。

[![elasticsearch create alert](img/9c7dd39e73bf3e45177916feda3e9baa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4gYeW3Ur--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://sematext.com/wp-content/uploads/2019/03/elasticsearch-create-alert.gif)

*为弹性搜索请求查询计数指标创建警报*

## **弹性搜索日志**

### **航运弹性搜索日志**

由于在一个平台中拥有[日志和指标](https://sematext.com/metrics-and-logs/)使得故障排除更加简单和快速，所以让我们也发布弹性搜索日志。您可以使用[许多日志运送器](https://sematext.com/docs/integration/#logging)，但是我们将使用[日志代理](https://sematext.com/logagent/)，因为它是轻量级的，易于设置，并且因为它可以解析和构造开箱即用的日志。日志解析器提取时间戳、严重性和消息。对于查询跟踪，日志解析器还提取唯一的查询 ID 来对与查询执行相关的日志进行分组。

1.  [创建一个日志应用](https://apps.sematext.com/ui/integrations)来获得一个应用令牌
2.  安装 Logagent npm 软件包

```
sudo npm i -g @sematext/logagent 
```

你没有 Node.js，可以轻松安装。例如在 Debian/Ubuntu 上:

```
curl -sL https://deb.nodesource.com/setup\_10.x | sudo -E bash -
sudo apt-get install -y nodejs 
```

1.  通过指定日志令牌和 Elasticsearch 日志文件的路径来安装 Logagent 服务。您可以使用`-g '/var/log/ **/elasticsearch*.log'`仅从 Elasticsearch 服务器发送日志。如果在同一台服务器上运行其他服务，请考虑使用`-g '/var/log/** /*.log'`发送所有日志。当未指定-g 参数时，默认设置是从`/var/log/**/*.log`发送所有日志。Logagent 检测 init 系统并安装 Systemd 或 Upstart 服务脚本。在 Mac OS X 上，它创建了一个 launchd 服务。只需运行:

```
sudo logagent-setup -i YOUR_LOGS_TOKEN -g '/var/log/**/elasticsearch*.log' 
```

欧盟地区使用:

```
sudo logagent-setup -i LOGS_TOKEN \
-u logsene-receiver.eu.sematext.com \
-g '/var/log/**/elasticsearch*.log' 
```

安装脚本在/etc/sematext/logagent.conf 中生成配置文件，并将 logagent 作为系统服务启动。

注意，如果在容器中运行 Elasticsearch，[为容器日志](https://sematext.com/docs/logagent/installation-docker/)设置 Logagent。

### **日志搜索和仪表盘**

一旦 Sematext 中有了日志，您就可以在故障排除时搜索它们，保存您经常运行的查询或[创建您自己的日志仪表板](https://sematext.com/product-updates/#/2018/custom-reports-for-monitoring-logs-apps)。

[![logs search](img/a5ae3b2ae32fa39458417c8888e6261a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yx6wMf3P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2019/03/clickhouse-logs-search.png)

*搜索弹性搜索日志*

**日志搜索语法**

如果你知道如何用谷歌搜索，你就会知道如何在 Sematext Cloud 中搜索你的日志。

*   使用 AND、OR、NOT 运算符，例如(错误或警告)NOT exception
*   分组 AND、OR、NOT 子句–例如，消息:(异常或错误或超时)和严重性:(错误或警告)
*   不喜欢布尔人？使用+和–来包含和排除–例如+message:error-message:time out-host:db1 . example . com
*   明确使用字段引用，例如消息:超时
*   需要短语搜索吗？使用引号–例如消息:“致命错误”

在浏览日志时，你可能会发现自己一次又一次地运行相同的搜索。为了解决这个问题，Sematext 允许您保存查询，这样您就可以快速地重新执行它们，而不必重新键入它们。请观看[如何使用日志进行故障排除](https://www.youtube.com/watch?v=glwZ8OCV0kc)来简化您的工作。

### **弹性搜索日志预警**

要在日志上创建警报，我们首先运行一个查询，该查询与我们希望获得警报的日志事件完全匹配。要创建警报，只需点击软盘图标。

[![alerts on elasticsearch logs](img/cb8807820af25c34aae25effa27e4360.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OHSyEQ1f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2019/03/pasted-image-0-2.png)

与度量警报规则的设置类似，我们可以根据警报查询返回的匹配日志事件的数量来定义基于阈值的警报或异常检测警报。

[![alert screen](img/c08270ceaab1a12a5f78292f82e0fda0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G_Vj_iRL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2019/03/Screen-Shot-2019-02-13-at-12.32.04.png)

更多详情请查看语义云中的[警报。](https://www.youtube.com/watch?v=WE9xAUud28o)

### **弹性搜索指标和对数相关性**

典型的故障排除工作流程从检测指标中的峰值开始，然后深入日志以找到问题的根本原因。Sematext 让这变得非常简单快捷。您的指标和日志在同一个屋檐下。日志集中，搜索速度快，强大的[日志搜索语法](https://sematext.com/docs/logs/search-syntax/)使用简单。指标和日志的关联只需点击一下鼠标。

[![logs and metrics correlation](img/01f77fea4ecf47a3e1bd9bc7ba541d3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TDkppEfb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2019/03/pasted-image-0-1.png)

*在单一视图中弹性搜索日志和指标*

## **用语义监控弹性搜索**

全面监控 Elasticsearch 包括确定 Elasticsearch 的关键指标、收集指标和日志，然后以有意义的方式连接一切。在这篇文章中，我们向您展示了如何在一个地方监控 Elasticsearch 指标和日志。我们使用 OOTB 和定制的仪表板、指标关联、日志关联、异常检测和警报。借助其他[开源集成](https://sematext.com/blog/now-open-source-sematext-monitoring-agent/)，如 [Apache Kafka](https://sematext.com/integrations/kafka-monitoring/) ，您可以轻松地开始监控 Elasticsearch 以及来自基础架构中所有其他技术的指标、日志和分布式请求跟踪。通过[免费 Sematext 试用](https://apps.sematext.com/ui/registration)，深入了解 Elasticsearch。

用语义监测弹性搜索的帖子[最早出现在](https://sematext.com/blog/monitoring-elasticsearch-with-sematext/)[语义](https://sematext.com)上。