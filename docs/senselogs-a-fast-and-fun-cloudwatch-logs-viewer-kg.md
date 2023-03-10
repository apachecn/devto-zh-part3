# 更好的 CloudWatch 日志查看器

> 原文：<https://dev.to/sensedeep/senselogs-a-fast-and-fun-cloudwatch-logs-viewer-kg>

AWS CloudWatch Logs 是一个方便、统一的日志记录解决方案，因此您可以在一个地方收集和访问所有操作和性能数据。它具有成本效益和灵活性，然而，标准的 AWS 日志查看器是**基本的和缓慢的**。

作为一名开发人员，我需要监控和扫描应用程序日志文件的尾部，以便快速定位错误。我需要能够扫描异常日志条目，并在上下文中查看日志事件序列。AWS 日志控制台虽然可用，但速度很慢，而且仔细查看日志数据非常耗时。逐页加载事件日志数据最多需要 2-4 秒。

我想要一个非常快速、简单的日志查看器，它可以让我立即轻松地看到应用程序错误和状态。SenseDeep 现在有结果了。一个快速、易用、完全基于浏览器的 CloudWatch 日志查看器。

## AWS 云观察日志查看器

标准的 CloudWatch 日志查看器很简单，有一些很大的局限性。

[![](img/9b2d075741b480752203daa475e60acc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I-Ql4fk_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.sensedeep.cimg/blog/cloud-watch-logs-ui.png)

*   CWL 查看器不会自动聚合日志流。CWL 日志由逻辑上代表单个日志的一个或多个日志流组成。AWS 通常会在现有流超过一定大小时创建新的日志流，或者重新启动服务，因此流往往会成倍增加。这些流以离散文件的形式呈现，您需要逐个检查。虽然有一个 API 来聚合事件(filterLogEvents)，但 CWL 并不提供日志文件的统一视图。

*   当新事件到达时，查看器不会自动刷新日志视图。CWL 日志查看器提供事件的静态分页视图。您可以单击一个链接来获取更多事件，但是如果没有手动干预，显示屏不会自动显示新的日志事件。

*   查看器加载日志事件的速度很慢，刷新速度也很慢(逐页)。每次单击可能需要 2-5 秒来刷新下一页或下一批日志事件。这使得滚动浏览许多日志事件作为一种扫描日志错误的方式变得非常乏味和不切实际。

*   查看器不理解包含 JSON 记录的事件。在日志视图中，您可以单击一个事件，并将其内联扩展为 JSON 文本，但是 CWL 不理解事件记录的 JSON 结构，也不会将这些转换为可以显示为表格标题的顶级字段。

SenseDeep 解决了 CWL 的这些缺点，使开发人员能够快速轻松地洞察他们的应用程序日志。

## 感知深度

SenseDeep 有一个非常棒的 AWS CloudWatch 日志查看器，运行速度非常快。

[![](img/fce4f5b3c37e6229fc701e665e1d0ddd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PIszHwdB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.sensedeep.cimg/blog/sensedeep-viewer.png)

SenseDeep 将来自多个 AWS 日志流的日志聚合到一个统一的视图中进行分析。它可以实时跟踪事件、平滑滚动、快速导航和即时查询。SenseDeep 支持通过强大的全文搜索无限滚动日志数据。

|  | CloudWatch 日志 | 感官深度 |
| --- | --- | --- |
| 捕获日志数据 | -好的 |  |
| 保留日志数据 | -好的 |  |
| 管理保留策略 | -好的 |  |
| 日志查看器 | 基础 | 先进的 |
| 该显示下一页了 | > 1-2 秒 | 小于 1/100 秒 |
| 浏览 1000 个事件的时间 | > 20 秒 | 不到 1/2 秒 |
| 自动聚合日志流 |  | -好的 |
| 透明事件预缓存 |  | -好的 |
| 实时刷新和实时结尾 |  | -好的 |
| 无限平滑滚动 |  | -好的 |
| 了解结构化事件 |  | -好的 |
| 过滤和查询事件 | 独立的 CloudWatch 洞察 | 集成的 Javascript 表达式 |
| 保存日志视图和视图格式 |  | -好的 |

## 入门

没有要安装的内容。只需在浏览器中导航至:

[https://app.sensedeep.com/](https://app.sensedeep.com/)

要了解有关 SenseDeep 以及如何使用该应用程序的更多信息，请转到:

[https://www.sensedeep.com/](https://www.sensedeep.com/)