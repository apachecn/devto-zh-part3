# 微服务架构的设计模式

> 原文：<https://dev.to/akshaypai/design-patterns-for-micro-service-architecture-2mkg>

想象一下将一个功能单元分解成多个小型服务单元。这正是微服务对传统整体架构的影响。但是，事情并不像看上去那样简单。微服务是所有主要软件开发项目的首选解决方案。

但是，即使它服务于一个主要目的，也有一些需要解决的挑战。在设计微服务架构的过程中，人们会学到一些微服务设计模式，这些模式不仅可以提高性能，还可以简化开发人员的工作。但是在我们深入研究架构设计模式之前，让我们对成功的微服务架构的[基础进行一个抽象的回顾。](https://goo.gl/ofyAQJ)

## 用于设计微服务架构的原则

*   高内聚，松散耦合。
*   无缝 API 集成。
*   每个服务的唯一标识源。
*   实时交通管理
*   最小化数据表以优化负载。
*   对外部和内部 API 进行持续监控。
*   每个微服务的隔离数据存储——这对于保持有限的数据访问和避免“服务耦合”非常重要。基于用户的数据分类非常重要，可以通过命令和查询责任分离(CQRS)来实现。
*   去中心化——设计微服务架构的首要原则是能够将整体架构分解成独立的实体。这些实体被称为微服务。这些微服务独立于其他系统功能工作，所有用户都可以编辑、删除或使用任何功能，而不会影响系统性能。
*   可扩展性——微服务的构建目标是:性能和效率。在现实世界的问题解决中，扩展和大规模系统对任何微服务生态系统的性能都至关重要。可扩展性对于设计微服务架构至关重要。由于多个片段可能在多种技术上运行，处理大量数据可能是一个挑战。但是，正确实施和使用[应用控制器](https://martinfowler.com/eaaCatalog/applicationController.html)可以使微服务架构的可扩展性成为可能。
*   通过 DevOps 集成持续交付——在 DevOps 中工作的人通常会很好地接受微服务架构，因为易于访问和集成多种技术。要设计微服务架构，需要专注于提高系统的性能和效率。这促使 DevOps 更快地交付解决方案。与传统的整体式设计相比，它还提供了某些优势，例如易于部署、可靠的解决方案、可伸缩性和管理。因此，它构成了设计基本原则的主要部分。

但是考虑到这些原则，要实现微服务架构的成功结构，需要克服某些挑战。这些障碍可能看起来很难，但通过一组微服务设计模式及其正确的实现，实现同样的目标是可能的。让我们来看看其中的一些微服务设计模式。

## 有效协作的微服务设计模式

如此多的微服务同时运行，协作成为运行高效微服务架构的必要条件。今天我们将看看设计微服务的协作模式。

### 1。聚合器微服务设计模式

在涉及多个服务的情况下，获取输出并为最终用户组合是必要的。对于用户来说，组合数据需要大量的系统内部知识。当我们设计微服务架构时，分解整体意味着输出来源的划分。因此，为了聚合这些数据，我们使用了聚合器模式。

该解决方案可以通过两个主要部分提交给最终用户。第一个是复合微服务，其次是 API 网关。它们中的任何一个都将聚合数据并将其转发给用户。但是，如果在分解系统时使用了业务功能，那么应该首选复合微服务。

### 2。分支微服务设计模式

它们基本上扩展了聚合器设计模式。在分支微服务中，您可以同时处理来自两个独立的，或者更准确地说是两个互斥的微服务链的请求和响应。

这种设计模式还提供了灵活性，可以根据您的业务需求调用单独的多个链，甚至单个链。在电子商务网站或 web 应用程序的情况下，我们可能需要从属于不同微服务的多个来源检索数据。这就是分支微服务设计模式发挥有效作用的地方。

### 3。前端/ API 网关的后端

对于任何应用程序来说，从每个正在运行的服务中获取数据都是必不可少的。对于微服务架构，从单个服务中提取数据非常重要。但是，从单个 UI 呈现的各种微服务中获取用户拥有的资源可能非常棘手。毕竟，用户界面捕获了终端用户的大量信息。API gateway 通过为体系结构中发生的所有交互生成一个单一的入口点来充当帮助热线。API gateway 还通过客户端授权和向客户端公开相关 API 来帮助建立安全性。我们现在要干嘛？

作为单一联系来源的 API 网关不仅可以充当代理服务器，将请求路由到微服务，还可以聚合来自多个服务的结果，并将输出发送给用户。它可以处理多个协议请求，并在需要时进行转换。(例如 HTTPS 到 AMQP，反之亦然)

## 用于性能监控的微服务设计模式

监控性能是成功的微服务架构的一个重要方面。它有助于计算效率并了解可能会降低系统速度的任何缺点。请记住以下与可观察性相关的模式，以确保稳健的微服务架构设计。

### 1。日志聚合

当我们提到微服务架构时，我们指的是一个精炼而精细的架构，其中一个应用程序由许多微服务组成。这些微服务独立运行，同时支持多种服务以及它们在不同机器上的实例。每个服务都会在日志中生成一个关于其执行的条目。如何跟踪大量与服务相关的日志？这就是日志聚合介入的地方。作为防止混乱的最佳实践，您应该有一个主日志服务。这个主日志记录服务应该负责聚集来自所有微服务实例的日志。这个集中的日志应该是可搜索的，这样更容易监控。

### 2。综合监控也称为语义监控

正如我之前解释的，对于一个成功的微服务架构来说，监控是一项痛苦但不可或缺的任务。随着数百个服务的同时执行，在日志注册表中查明导致失败的根区域变得很麻烦。合成监控助一臂之力。当您执行自动化测试时，综合监控有助于定期将结果映射到生产环境中。如果出现故障，用户会收到警报。使用语义监控，你可以用一个箭头瞄准两件事

*   监控自动化测试用例。
*   根据业务需求检测生产故障。

### 3。API 运行状况检查

微服务架构设计提升了彼此独立的服务，以避免系统中的任何延迟。正如我们所知，API 是在线连接的基础。有必要定期对您的 API 进行健康检查，以发现任何障碍。经常可以观察到，微服务已经启动并运行，但却无法处理请求。这可能是由多种因素造成的:

*   服务器负载
*   用户采用
*   潜伏
*   错误记录
*   市场占有率
*   下载

为了克服这种情况，我们应该确保每个运行的服务都必须有一个特定的健康检查 API 端点。例如:当 HTTP/health 附加在每个服务的末尾时，将返回相应服务实例的健康状态。服务注册中心定期请求健康检查 API 端点执行健康扫描。健康检查将为您提供以下信息:

1.  特定于您的应用程序的逻辑。
2.  主机的状态。
3.  与其他基础设施或任何服务实例的连接状态。

## 将一切归结为业务能力

将整体架构“分解”为微服务的过程需要遵循某些参数。这些参数有不同的基础。今天，我们将看看会留下持久影响的微服务设计模式的分解。

### 1。针对每项业务能力的独特微服务

一个微服务就像它的高内聚和松耦合的结合一样成功。服务需要松散耦合，同时保持相似兴趣的功能在一起。但是我们怎么做呢？我们如何将一个软件系统分解成更小的独立逻辑单元？我们通过定义微服务的范围来支持特定的业务能力。

例如，在每一个组织中，都有不同的部门组成一个整体。这些包括技术、营销、公关、销售、服务和维护。为了描绘一个微服务结构，这些不同的领域将每个都是微服务，而组织将是系统。因此，库存管理部门负责所有的库存。类似地，装运管理将处理所有装运等等。

为了保持效率和预见增长，最好的解决方案是使用业务能力来分解系统。这包括对各种业务领域的分类，这些领域负责在其自身能力中产生价值。

### 2。围绕类似业务能力的微服务

尽管根据业务能力进行了隔离，但微服务通常会面临更大的挑战。服务之间的公共类呢？分解这些被称为“神类”的类需要干预。例如，在电子商务系统的情况下，订单将为若干服务所共用，如订单号、订单管理、订单退货、订单交付等。为了解决这个问题，我们求助于一个常见的微服务设计原则，称为域驱动设计(DDD)。

在领域驱动的设计中，我们使用子域。这些子域模型已经定义了功能范围，这就是所谓的有界上下文。这个有界上下文是用于创建每个微服务的参数，从而克服了公共类的问题。

[![](img/5ca15e434455da9426a2aad75efd7d47.png)](https://goo.gl/oVsgWf)

### 3。扼杀者藤蔓图案

当我们讨论单片架构的分解时，我们经常会忽略将单片系统转换为微服务架构的过程。在不妨碍工作的情况下，转换会非常困难。为了解决这个问题，我们有了基于藤蔓类比的扼杀者模式。以下是马丁·福勒所说的扼杀者模式的含义:

> “这个地区(澳大利亚)的自然奇观之一是巨大的扼杀者藤蔓。它们在无花果树的上部枝条中播种，然后逐渐向下生长，直到扎根于土壤中。许多年后，它们会长成奇异而美丽的形状，同时勒死并杀死作为它们宿主的树。”

在 web 应用程序中，将服务分解成不同的域是可能的，在这种情况下，Strangler 模式非常有用。由于调用是来回的，不同的服务存在于不同的域中。因此，这两个域存在于同一个 URI 上。一旦服务进行了改革，它就会“扼杀”应用程序的现有版本。这个过程一直持续到整块巨石消失。

## 优化数据库存储的微服务设计模式

对于微服务架构来说，松耦合是一个基本原则。这支持独立服务的部署和可伸缩性。多个服务可能需要访问不存储在其单元中的数据。但是由于松散耦合，访问这些数据可能是一个挑战。主要是因为不同的服务有不同的存储需求，在微服务设计中对数据的访问受到限制。因此，我们根据不同的需求来看一些主要的数据库设计模式。

### 1。每个服务的单独数据库

通常应用于领域驱动的设计中，每个服务一个数据库将整个数据库连接到特定的微服务。由于挑战和缺乏可访问性，需要为每个服务设计一个单独的数据库。这些数据只能由微服务访问。该数据库对任何外部微服务的访问都是有限的。其他人访问这些数据的唯一方式是通过微服务 API 网关。

### 2。每个服务的共享数据库

在域驱动设计中，每个服务一个单独的数据库是可行的，但是在将整体架构分解为微服务的方法中，使用单个数据库可能会很困难。因此，在分解过程进行的同时，为有限数量的服务实现共享数据库是明智的。这个数字应该限制为 2 或 3 个服务。这个数字应该保持较低，以允许部署、自治和可伸缩性。

### 3。事件源设计模式

根据马丁·福勒的说法

> 事件源确保对应用程序状态的所有更改都存储为一系列事件。我们不仅可以查询这些事件，还可以使用事件日志来重建过去的状态，并以此为基础自动调整状态以应对追溯性的更改。

这里的问题在于可靠性。对于应用程序状态的变化，您如何依靠架构来做出改变或发布实时事件？事件源有助于解决这种情况，每次业务实体改变其状态时，都会向事件列表添加一个新事件。像客户这样的实体可能由许多事件组成。因此，建议应用程序保存实体当前状态的屏幕截图，以便优化负载。

### 3。命令查询责任分离(CQRS)

在每个服务一个数据库的模型中，由于只能访问一个数据库，因此无法实现查询。对于查询，需求是基于联合数据库系统的。但是我们如何查询呢？

基于 CQRS，要查询每个服务模型的单个数据库，应用程序应该分为两个部分:命令和查询。在这个模型中，command 处理所有与创建、更新和删除相关的请求，而查询则通过物化视图来处理。这些视图通过一系列事件进行更新。反过来，这些事件是使用事件源模式创建的，该模式标记数据中的任何更改。这些变化最终成为事件。

## 面向无缝部署的微服务设计模式

当我们实现微服务时，在调用这些服务的过程中会出现一些问题。当您设计微服务架构时，某些横切模式可以简化工作。

### 1。服务发现

容器的使用导致了 IP 地址的动态分配。这意味着地址可以随时改变。这导致服务中断。除此之外，用户不得不承担记住服务的每个 URL 的负担，这变得紧密耦合。

为了解决这个问题并给用户提供请求的位置，需要使用一个注册中心。在启动时，服务实例可以在注册表中注册，在关闭时可以取消注册。这使得用户能够找到可以查询的确切位置。此外，注册中心的健康检查将确保只有工作实例可用。这也提高了系统性能。

### 2。蓝绿色部署

在微服务设计模式中，有多个微服务。每当要实现更新或部署新版本时，必须关闭所有服务。这导致大量的停机时间，从而影响生产率。为了避免这个问题，当您设计微服务架构时，您应该使用蓝绿色部署模式。

在这种模式中，两个相同的环境并行运行，称为蓝色和绿色。每次只有其中一个处于活动状态并处理所有生产流量。例如，blue 是实时的，处理所有流量。在新部署的情况下，将最新版本上传到绿色环境，将路由器切换到相同的环境，从而实现更新。

## 用于性能监控的微服务设计模式

监控性能是成功的微服务架构的一个重要方面。它有助于计算效率并了解可能会降低系统速度的任何缺点。请记住以下与性能监控相关的模式，以确保稳健的微服务架构设计。

### 1。日志聚合

当我们提到微服务架构时，我们指的是一个精炼而精细的架构，其中一个应用程序由许多微服务组成。这些微服务独立运行，同时支持多种服务以及它们在不同机器上的实例。每个服务都会在日志中生成一个关于其执行的条目。如何跟踪大量与服务相关的日志？这就是日志聚合介入的地方。作为防止混乱的最佳实践，您应该有一个主日志服务。这个主日志记录服务应该负责聚集来自所有微服务实例的日志。这个集中的日志应该是可搜索的，这样更容易监控。

### 2。综合监控也称为语义监控

随着负载和微服务的增加，持续检查系统性能变得非常重要。这包括任何可能形成的模式或解决遇到的问题。但更重要的是，数据是如何收集的？

答案在于使用公制服务。这种度量服务要么是推的形式，要么是拉的形式。顾名思义，像 AppDynamics 这样的推送服务将指标推送到服务，而像 Prometheus 这样的拉取服务从服务中提取数据。

### 3。运行运行状况检查

微服务架构设计提升了彼此独立的服务，以避免系统中的任何延迟。但是，有时系统启动并运行，但由于服务错误而无法处理事务。为了避免对这些错误服务的请求，必须实现负载平衡模式。

为了实现这一点，我们在每个服务的结尾都使用了“/health”。该检查用于发现服务的健康状况。它包括主机的状态、连接和算法逻辑。

## 结论

尽管并非所有的设计模式都适用于给定的微服务，但您可以放心，它们中的大部分将在任何地方使用。这些设计模式有助于开发人员引入一致的标准，并从整体上提高应用程序的可靠性。

这些设计模式的评估、审计、实现和[测试微服务](https://goo.gl/xMc3cg)是微服务架构的一个持续过程。从应用程序的设计阶段到生产中的维护阶段，这些模式将贯穿始终。

[![](img/5ca15e434455da9426a2aad75efd7d47.png)](https://goo.gl/oVsgWf)

原文:[lambdatest.com](https://goo.gl/ohDDnK)

**相关帖子**

1.  [成功微服务设计的 9 个基本要素](https://goo.gl/mypX6u)
2.  [与微服务架构相关的测试挑战](https://goo.gl/BFH2Bg)
3.  [如何测试微服务架构应用](https://goo.gl/8eefXf)
4.  [微服务架构会影响安全测试吗？](https://goo.gl/NDWX5e)