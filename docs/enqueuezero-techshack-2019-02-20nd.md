# EnqueueZero Techshack 2019-02

> 原文：<https://dev.to/soasme/enqueuezero-techshack-2019-02-20nd>

# enqueue zero Techshack 2019-02

原帖:原帖:[https://enqueuezero.com/techshack.weekly/2019-02.html](https://enqueuezero.com/techshack.weekly/2019-02.html)

起亚奥拉！我是**零入队**(【https://enqueuezero.com】T2)的创建者，这是一个向开发者/开发者/系统管理员解释代码原理的网站。本网站自 2018 年起在单人团队下更新。“EnqueueZero Techshack”系列由我写的或在互联网上找到的一堆有趣的帖子组成。更重要的是，我希望这些帖子能让你满意。

为了保持我的动力和这个网站的无广告，感谢您的捐赠！

[![Donate this project using Patreon](img/d8ef9eb3c1d7813b01d7070f13d6bc49.png)](https://www.patreon.com/join/enqueuezero)

喜欢读本周的帖子。

## 潜入深度学习

[d2l.ai](https://d2l.ai/index.html) | [zh.d2l.ai](https://zh.d2l.ai/)

这是一本保持深度学习的好书。讲解了[深度学习基础知识](https://d2l.ai/chapter_deep-learning-basics/index.html)[CNN](https://d2l.ai/chapter_convolutional-neural-networks/index.html)[RNN](https://d2l.ai/chapter_recurrent-neural-networks/index.html)[NLP](https://d2l.ai/chapter_natural-language-processing/index.html)等。每章都附有数学、数字和代码。

## 船长的建筑

[opensource.zalando.com](https://opensource.zalando.com/skipper/reference/architecture/)

[Skipper](https://opensource.zalando.com/skipper/reference/architecture/) 是用于服务组合的 HTTP 路由器和反向代理。

*   它以服务器包`skipper`和客户端包`eskip`的形式发布。
*   `skipper`包可以从`eskip`配置文件和 Kubernetes 入口资源对象加载路由。
*   `skipper`的内部包`proxy`动态改变`skipper`填充的路由表。
*   路由是路由表中的一个条目，由用于路由 HTTP 请求的`predicates`、用于修改请求和响应的`filters`以及后端(或`<shunt>`或`<loopback>`组成。

[![Skipper Architecture](img/9d35983dd11d81a73627a2c0adb70a3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--04Wp5fas--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://opensource.zalando.com/skipper/img/architecture.svg)

对于每个请求，

*   代理创建一个上下文，用 Opentracing API Span 增强它。
*   代理检查速率限制并在路由表中查找路由。
*   Skipper 应用请求过滤器。
*   Skipper 检查路由本地费率限制、断路器并进行后端调用。
    *   如果出现 TCP/TLS 连接错误，Skipper 会自动重试。
*   Skipper 应用响应过滤器。

skipper 不会将请求转发到后端的两种特殊情况。

*   分流路由(`<shunt>` ): skipper 仅通过使用过滤器单独服务请求。
*   Loopback route( `<loopback>`):根据过滤器对请求所做的更改，请求被传递到路由表以寻找另一条路由。

[![Request](img/1ac96ced9aa50320f241052e7b00c6cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eULqU5C2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://opensource.zalando.com/skipper/img/req-and-resp-processing.svg)

## 创建漂亮图表的工具

[news.ycombinator.com](https://news.ycombinator.com/item?id=18788244)

线程中提到的工具:yeD、draw.io、visio、plantuml、simplediagrams、AsciiFlow、Gliffy、Omnigraffle、Graphviz、ipe、Dia、gravit.io、limnu.com、LucidChart、plotdevice.io、monodraw、美人鱼、whimsical.co、LaTeX/TikZ、...

> 这是 HN 的最佳状态，没有人回答原来的问题。

## 理解解析器组合子

[fsharpforfunandprofit.com 1](https://fsharpforfunandprofit.com/posts/understanding-parser-combinators/)|【fsharpforfunandprofit.com】2|[3](https://fsharpforfunandprofit.com/posts/understanding-parser-combinators-3/)|[fsharpforfunandprofit.com 4](https://fsharpforfunandprofit.com/posts/understanding-parser-combinators-4/)

本系列提供了通过解析器组合子创建基本 JSON 解析器库的分步教程。虽然实现是在 F#中，但概念在其他编程语言中是相同的。

*   步骤 1:解析器根据解析器是否能成功地使用字符给出结果(true，“剩余字符”)或(false，“原始字符”)。
*   步骤 2:解析器给出结果(“成功消息”、“剩余字符”)或(“错误消息”、“原始字符”)。
*   步骤 3:解析器给出结果 Success(charToMatch，remaining)或 Failure(message)。
*   第 4 步:将解析器函数转换成可定制的实现，以支持更多的字符。
*   步骤 5:将解析函数封装在一个类型中。
*   步骤 6:实现然后组合器。比如`parserA andThen parserB` =先处理 char A，再处理 char B。
*   步骤 7:类似地，实现“orElse”组合子。
*   第八步:使用`mapP`支持一次解析多个字符，并将它们聚合成一个字符串。例如，`run parseThreeDigitsAsInt "123A"`给出了`Success(123, "A")`
*   第 9 步:使用`sequence`将一系列解析器转换成一个解析器。比如`let combined = sequence [parseThreeDigitsAsInt; parserA]`可以解析“123AZ”到`Success([123, 'A'], "Z")`。
*   第 10 步:使用`many` / `many1`多次匹配一个解析器，非常像 regex `(pattern)*`和`(pattern)+`。
*   第十一步:扔掉一些结果。例如，在解析`'abc'`时，我们想要结果`String("abc")`，而不是`String("'abc'")`。
*   步骤 12:如果输入不正确，添加错误消息。
*   步骤 13:应用上面介绍的所有模块，定义 JSON 解析规则[https://www.json.org/](https://www.json.org/)

## 多个节点，一个分布式系统

[medium.com](https://medium.com/baseds/many-nodes-one-distributed-system-9921f85205c4)

*   不与其他进程通信的单个进程或计算机**不是**分布式系统。
*   分布式系统涉及多个实体以某种方式相互通信，同时也执行它们自己的操作。
*   如果系统是一组分布式服务器，那么这些组件可以被称为“服务器”；如果系统包括相互对话的进程，那么实体可能只是“进程”。不管它是什么，我们称之为节点，分布式系统中的个体实体。
*   分布式系统中的节点运行它们自己的操作——这些操作很快。但是节点之间也相互通信——通信很慢，这恰好是分布式计算中最大的问题之一。

## gRPC 在浏览器中的状态

[grpc.io](https://grpc.io/blog/state-of-grpc-web)

*   gRPC 在浏览器中的挑战:没有办法强制使用 HTTP/2。此外，浏览器不支持访问原始 HTTP/2 帧。
*   如何救援？在浏览器端和 gRPC 服务器端之间放置一个 gRPC-Web 作为代理。代理可以支持 HTTP/1 和 HTTP/2。
*   gPRC-web 有两种实现:[impossible-eng/grpc-web](https://github.com/improbable-eng/grpc-web)和 [grpc/grpc-web](https://github.com/grpc/grpc-web) 。

[![](img/f52ffbcdfd446996aa70ac2b6465900f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xgrGFhzl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://grpc.io/img/grpc-web-proxy.png)

## 如何加快代码审查

[sergeyzhuk.me](https://sergeyzhuk.me/2018/12/29/code_review/)

复习策略:

*   功能变化:业务需求和设计的实现。
*   向后兼容性和设计改进。
*   简单重构:可读性改进。因为这些变化大部分可能是由 IDE 完成的。
*   重命名/移除类:名称空间结构是否变得更好了。
*   移除未使用的代码:向后兼容性。
*   代码风格修正:大多数情况下是即时合并。
*   格式化修复在大多数情况下即时合并。
*   简单的重构。代码变得可读性更强了？-合并。
*   重命名/移动类。这个类被移到了一个更好的命名空间。-合并。
*   移除未使用的(死的)代码-合并。
*   代码风格或格式修正-合并，人们不应该审查它们，这是 linters 的任务。

不要用混合类别的变更创建巨大的拉式请求。

*   优化你的代码以便阅读。代码被阅读的次数比它被编写的次数多得多。
*   描述建议的更改，以便为请求中的差异提供上下文。
*   在提交请求之前，您应该自己检查您的代码。审查你自己的请求，因为它不是你的。有时你可能会发现你错过了什么。这将减少拒绝/修复的循环。

## Monorepo:请做 v/s 请不要

[做](https://medium.com/@adamhjk/monorepo-please-do-3657e08a4b70) v/s [不要](https://medium.com/@mattklein123/monorepos-please-dont-e9a279be011b)

Monorepo 是一种软件开发策略，许多项目的代码存储在同一个库中。

*   尽管我们受益于更容易的协作/代码共享/单一构建/无依赖性管理，但它产生了紧密耦合，更重要的是，在规模上，monorepo 必须解决 polyrepo 必须解决的每一个问题，这带来了鼓励紧密耦合的负面影响，以及解决 VCS 可伸缩性的额外巨大努力。
*   polyrepo 的默认行为是隔离——这是关键所在。monorepo 的默认行为是共享责任和可见性——这是关键所在。

## 网飞 Play API:构建进化架构

[infoq.com](https://www.infoq.com/news/2019/01/netflix-evolution-architecture)

本次讲座的主要收获包括:

*   具有单一身份/职责的服务更容易维护和升级。
*   当构建服务时，如果需要彻底的考虑或快速的实验，工程师应该花时间确定核心决策。
*   工程师应该开始问为什么服务存在，以便确定它的责任。
*   确定你的 1 型和 2 型决策；花 80%的时间讨论和调整第一类决策。
    *   >第 1 类决策非常重要，具有长期影响，因此这些决策必须有条不紊地做出，并与他人协商。
    *   >第二类决策很容易改变，不具有长期影响，因此这些决策应该由“高判断力的个人或小团体”迅速做出。

## 日志、指标和图表，天啊！

[grafana.com](https://grafana.com/blog/2016/01/05/logs-and-metrics-and-graphs-oh-my/)

*   监控的目标是能够检测、调试和解决出现的任何问题。
*   监控意味着了解系统内部发生了什么，有多少流量，性能如何，有多少错误。
*   日志来自 Unix 系统、应用程序。
*   指标是从应用程序中收集的变量。
*   这两种方法都允许创建高级图表，以了解您的应用程序服务了多少个请求，服务的速度有多快，有多少错误。两者都允许你切片和切块。
*   度量为您提供了这个工具的聚合视图，这意味着它们可以从整体的角度来看您的系统。
*   日志为您提供了少量指标的视图，这意味着您可以深入查看特定事件。
*   日志和度量是互补的。

## 快递:Dropbox 迁移到 gRPC

[blogs.dropbox.com](https://blogs.dropbox.com/tech/2019/01/courier-dropbox-migration-to-grpc/)

Dropbox 的核心 SOA 框架是 Courier，一个基于 gRPC 的协议。

*   它将每个服务的身份编码到 TLS 证书中。另外，它带来了访问控制列表(ACL)和速率限制。
*   通过使用服务标识，可以在仪表板中搜索服务的日志、统计信息和跟踪信息。每个服务都有负载、错误和延迟的粒度属性。
*   每个 gRPC 请求都包含一个截止日期，表示客户端等待回复的时间。
*   它引入了一个 LIFO 队列作为自动断路器。队列不仅受到大小的限制，更重要的是，它还受到时间的限制。一个请求只能在队列中停留这么长时间。
*   它修改了 gRPC-core 和 gRPC-python 以支持会话恢复，从而根本不进行 TLS 握手，这使得请求更快。
*   从旧的 RPC 框架迁移到 Courier。
    1.  编写一个支持这两者的通用接口。
    2.  迁移到新的。
    3.  将客户端切换到使用 Courier RPC
    4.  清理遗留代码。
*   吸取的教训
    *   可观察性是一个特征。在故障排除过程中，开箱即用的所有指标和故障是非常宝贵的。
    *   标准化和一致性很重要。它们降低了认知负荷，简化了操作和代码维护。
    *   尽量减少开发人员需要编写的样板代码的数量。Codegen 是你的朋友。
    *   尽可能简化迁移。迁移可能会比开发本身花费更多的时间。此外，迁移仅在执行清理后完成。
    *   RPC 框架可以是一个添加基础设施范围的可靠性改进的地方，例如，强制截止时间、过载保护等。常见的可靠性问题可以通过按季度汇总事件报告来确定。