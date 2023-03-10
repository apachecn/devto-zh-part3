# 无服务器架构模式

> 原文：<https://dev.to/foxteck/serverless-architectural-patterns-5ge1>

[![](img/301ecba85f11e068fb0d726cd15583be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zcgYT0wO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Awz3dUQYEOulqAI-A9H4-wg.png) 

<figcaption>黑色和白色的云彩，大气——摄影:[布莱恩·米纳尔](https://unsplash.com/photos/LzJZOJZtgmc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

我们正在构建越来越复杂的平台，同时努力满足不断变化的业务需求，并将它们按时交付给越来越多的用户。

我们交付的东西是独一无二的。不同服务、技术、特性和团队的组合，它们有自己的环境和竞争优先权。

有时感觉就像你在设计和建造一艘航空母舰，同时在汹涌的海洋中航行，被占领，并受到攻击。

> ![](img/087976fa203bf51faf4528469343326a.png)![](img/608b44b64a25bf4aaa0d2cd97e18a46b.png)爱德华多·罗梅罗@福克斯泰克![](img/4d9c44713c216584b3d48ff3455cbb68.png)“编写软件就像是在波涛汹涌的海洋中设计和建造一艘新型的航空母舰，被占领，被攻击。”2017 年 4 月 26 日 21:14PM[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=857342043017891840)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=857342043017891840)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=857342043017891840)1

持续快速地交付高质量的软件是我们客户的核心商业优势。用现代架构、框架和实践加速开发过程是战略性的。

无服务器模式非常适合快速、连续的软件交付。您不必考虑管理基础架构、配置或规划需求和规模。

借助“功能即服务”模式，我们将代码组织成更小、更简单的单元，易于理解、更改和部署到生产中。允许我们交付商业价值并快速迭代。

> 无服务器是一个伟大的实验，学习和超越你的竞争对手的推动者。

架构模式是在我们的工程组织中推广最佳实践、健壮的解决方案和共享架构愿景的强大方法。

在利用无服务器的不同项目中，我看到了我们可以用来解决现代云架构中常见问题的模式。虽然这不是一个详尽的集合，但它可以用作我们构建的下一个平台的架构级构建块的目录。

### 无服务器架构模式目录

*   简单的 Web 服务。
*   解耦消息传递。
*   强大的 API。
*   聚合器。
*   酒吧/酒吧。
*   陌生人
*   基于队列的负载均衡。
*   大量读取的报告引擎。
*   溪流和管道。
*   扇入和扇出。

#### 简单的 Web 服务

> "客户需要通过公共或内部 API 来消费服务."

简单的 web 服务是 AWS Lambda 作为后端服务的最标准用例。它代表一个 [*n 层*](https://en.wikipedia.org/wiki/Multitier_architecture) 或 [*分层*](https://martinfowler.com/bliki/PresentationDomainDataLayering.html) *架构*的*逻辑*或*域*层。

[![](img/1556e33a45114e2d625dcd62b4426521.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N-1rPFDM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/882/1%2AFBgxD-qR66DTnjHA1ASJEA.png) 

<figcaption>公共 API —通过 HTTP(s)暴露的 Lambda 函数</figcaption>

对于公共 API，API Gateway 通过 HTTPs 公开 lambda 函数。API 网关可以处理授权、认证、路由和版本控制。

对于内部 API，客户端使用 [AWS 的 SDK](https://aws.amazon.com/tools/#sdk) 从客户端应用程序中直接调用 lambda 函数。

[![](img/442123f62cf3f3983e5b78f6c7c18cac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E-hH8qqf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/858/1%2AiPIAqcjx6gwXO2uvdh67Ag.png) 

<figcaption>内部 API——客户端通过自己喜欢的语言的 AWS SDK 调用 Lambda 函数。</figcaption>

AWS Lambda 自动扩展，可以处理波动负载。执行时间受限于 API Gateway 允许的最大值( *29 秒)*，或者如果使用 SDK 调用，则受限于 *15 分钟*的硬限制。

强烈建议函数[无状态](https://read.acloud.guru/lambda-for-alexa-skills-7-tips-from-the-trenches-684c963e6ad1#5c39)，[会话](https://serverless.com/blog/strategies-implementing-user-authentication-serverless-applications/)被[缓存](https://medium.com/@tjholowaychuk/aws-lambda-lifecycle-and-in-memory-caching-c9cd0844e072)，到下游服务的连接被[适当处理](https://www.jeremydaly.com/reuse-database-connections-aws-lambda/)。

#### 解耦消息传递

> “随着打破整体格局或继续为我们的平台构建服务，不同的服务必须进行交互。我们希望避免瓶颈、同步 I/O 和共享状态。”

异步消息传递是大多数服务集成的基础。它被证明是企业架构的最佳策略。它允许构建松散耦合的架构，克服远程服务通信的限制，如延迟和不可靠性。

[![](img/2ab655d6bc439475ed89f932e713b353.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZKJzg5zF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AM0nPEazhCK9DiKurpjw9CQ.png) 

<figcaption>与 SNS 解除消息传递</figcaption>

在上面的例子中，服务 A 触发事件，SNS 将这些事件分发给其他服务。当部署新服务时，他们可以订阅频道，并在事件发生时开始接收消息。

消息传递基础设施是可靠的。提供比共享数据库更好的封装。非常适合基于事件的异步通信。

不同的技术有不同的限制，并提供特定的保证。了解这些权衡是很重要的。AWS 上最常见的消息服务有 [SNS](https://aws.amazon.com/sns/faqs/) 、 [SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-limits.html) 和 [Kinesis](https://docs.aws.amazon.com/streams/latest/dev/service-sizes-and-limits.html) 。

***注意事项*** :除了每个消息服务的已知限制和保证之外，还应该有意识地考虑消息复制、消息排序、有害消息、分片和数据保留。微软的“[异步消息传递入门](https://docs.microsoft.com/en-us/previous-versions/msp-n-p/dn589781(v=pandp.10))”文章详细讨论了[这些主题](http://considerations-for-implementing-asynchronous-messaging)。

#### 健壮的 API

> “随着平台服务数量的增长，客户需要与越来越多的服务进行交互。因为每个服务可能有不同的端点，属于不同的团队，并有不同的发布周期，让客户端管理这些连接可能是一个挑战。”

在这个模式中，我们有一个单一的入口点，它公开了一个定义良好的 API，并根据端点、路由、方法和[客户端特性](https://blog.apisyouwonthate.com/api-versioning-has-no-right-way-f3c75457c0b7#0f4b)将请求路由到下游服务。

[![](img/06028e679b501f2432a33f3a5a35c8a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---KGRBPTo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AkN_ykbVbX1Ln57wnPdtUqg.png) 

<figcaption>一个健壮的 API，由不同的下游服务组成，通过 API 网关暴露。</figcaption>

下游服务可以是 lambda 函数、外部第三方 API、 [Fargate](https://aws.amazon.com/fargate/) 容器、成熟的微服务，甚至内部 API。

[可以利用 API 网关](https://aws.amazon.com/api-gateway/)、 [App 负载均衡器](https://aws.amazon.com/elasticloadbalancing/features/#Details_for_Elastic_Load_Balancing_Products)或 [AppSync](https://aws.amazon.com/appsync/) 作为路由引擎来实现。

[![](img/2181524f98a85e02d9de33a554b1888d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IoIe9gqR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/747/1%2AcY03nWEJaD4RysmM6YLIMw.png) 

<figcaption>AppSync (GraphQL)可以看作是健壮的 API 和聚合器模式的实现。</figcaption>

在客户机和下游服务之间有一个抽象层有助于增量更新、滚动发布和并行版本控制。下游服务可以归不同的团队所有，分离发布周期，减少跨团队协作的需要，并改善 API 生命周期和[可演进性](https://blog.apisyouwonthate.com/api-evolution-for-rest-http-apis-b4296519e564)。

这种模式也被称为 [API 网关](https://microservices.io/patterns/apigateway.html)或[网关路由器](https://docs.microsoft.com/en-us/azure/architecture/patterns/gateway-routing)。

***注意事项*** :网关可能是单点故障。管理资源和更新面向客户端的界面可能会很棘手。如果不通过代码自动化进行管理，它可能会成为跨团队的瓶颈。

#### 聚合器

> “客户端可能需要多次调用各种后端服务来执行单个操作。这些问题会影响性能和规模。”

微服务使得通信开销成为一个普遍问题。应用程序需要与许多较小的服务进行通信，有大量的跨服务调用。

服务集中客户端请求以减少通信开销的影响。它分解并向下游服务发出请求，收集并存储到达的响应，聚合它们，并将其作为一个响应返回给调用者。

[![](img/dea79d3a00f93d91ff1f0ac1fc8cbea9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jeYOHE_b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/934/1%2AcHSxPOODaR1itN4A_W8Mcg.png) 

<figcaption>发票λ功能聚合下游服务。</figcaption>

微软将这种模式称为[网关聚合](https://docs.microsoft.com/en-us/azure/architecture/patterns/gateway-aggregation)。它可以被实现为具有*某种*业务逻辑的服务，能够缓存响应并知道当下游服务失败时该做什么。

***注意事项*** :对 API 的调用应该作为一个操作来工作。聚合器的 API 不应该向客户端公开其背后的服务细节。聚合器可能是一个单点故障，如果它不是*足够靠近*其他服务，它可能会导致性能问题。聚合器负责处理重试、电路中断、缓存、跟踪和日志记录。

#### 发布/订阅

> “服务很少孤立存在。平台在发展，服务在激增。我们需要服务在不产生相互依赖的情况下进行交互。
> 
> 异步消息传递使服务能够向多个感兴趣的消费者宣布事件，而无需耦合。"

在[中，发布者-订阅者](https://docs.microsoft.com/en-us/azure/architecture/patterns/publisher-subscriber)服务通过一个通道将事件发布为*消息*。多个感兴趣的消费者通过订阅这些频道来收听事件。

[![](img/508b1b58ae88839a42c65945393ab8a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v_YCAeFB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1016/1%2A-EPTUG9Umkl2UZTw7XqYDw.png) 

<figcaption>订单服务与视频墙、排行榜和推荐引擎交互。</figcaption>

在示例中，当在移动应用程序中创建订单时，订单服务将发布一个事件。

屏幕墙服务正在侦听 OrderCreated 事件。它将接受订单，分解订单中的项目——哪些*需要像 v60 一样准备*,或者*不喜欢饼干*,并更新屏幕，以便我们的*咖啡师*看到接下来开始冲泡哪种咖啡。

排行榜服务将收到相同的事件，并将更新记录——谁是本月的*咖啡爱好者*，以及什么是*最畅销的咖啡产地、方法*和*美食*。

推荐服务将跟踪谁在喝什么，看起来很搭配的东西，以及你应该尝试的下一种方法。— *提示*，试试日本的虹吸管:

[https://medium . com/media/c5ae 351 F5 c 6 bbf 46 de 15 cc 3c 5 fa 8 f 498/href](https://medium.com/media/c5ae351f5c6bbf46de15cc3c5fa8f498/href)

服务是分离的。他们通过观察环境和对环境做出反应来合作，就像说唱歌手一样。

当新的服务和功能可用时，他们可以订阅、获取事件并独立发展。

团队可以专注于交付价值，提高他们的核心能力，而不必关注整个平台的复杂性。

***考虑:*** 发布者/订阅者是事件驱动架构的绝佳搭配。有很多不同的消息选项: [SNS](https://aws.amazon.com/sns/) ， [Kinesis](https://aws.amazon.com/kinesis/) ， [Azure 的服务总线](https://azure.microsoft.com/en-us/services/service-bus/)， [Google 的云发布/订阅](https://cloud.google.com/pubsub/)， [Kafka](https://kafka.apache.org/) ， [Pulsar](https://pulsar.apache.org/) 等等。这些消息传递服务将负责 pub/sub 的基础设施部分，但是考虑到消息传递的异步特性，前面讨论的所有问题——消息排序、复制、过期、幂等性和最终一致性——都应该在实现中加以考虑。

#### 扼杀者

> “系统会随着时间的推移而成长和发展。随着复杂性的增加，添加新功能会变得非常困难。完全更换一个系统需要时间，从零开始几乎是一个普遍的坏主意。
> 
> 逐渐迁移到新系统，同时让旧系统处理尚未实现的功能是一个更好的途径。
> 
> 但是客户需要了解这两个系统，并在每次迁移一个特性时进行更新。"

[扼杀者模式](https://www.martinfowler.com/bliki/StranglerApplication.html)是一种逐渐迁移遗留系统的技术，随着微服务的指数级流行而流行。在这种模式中，服务作为*的门面*，拦截来自客户端的请求，并将它们路由到遗留服务或新服务。

客户端继续使用相同的界面，不知道迁移将可能的影响和风险降至最低。

[![](img/ae1765ffb0c224bd23e74d0c27ec80ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sntrcYr7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Audq9hy-EMH0qSNZmjpBvlA.png) 

<figcaption>ALB 在遗留 app 和新微服务之间路由请求。</figcaption>

一个应用负载平衡器将客户的请求路由到订单服务，这是该团队实现的第一个微服务。其他一切都继续到遗留应用程序。

Orders 有自己的数据存储，并实现订单的所有业务逻辑。因为遗留应用上的一些功能使用顺序，我们需要将数据推回遗留应用以保持同步(类似于[反腐败层](https://docs.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer))。

[![](img/20859d7456fd55b7b5795ae5affdd2a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--44E7qPCx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALzhxzJ5M0zlNkgFm4SixrA.png) 

<figcaption>新功能—排行榜微服务现已推出。</figcaption>

随着项目的发展，新的特性出现了，我们创建了新的服务。

排行榜服务现已推出。这是一个全新的功能，由全新的*参与团队*创建，因此无需与传统应用程序交互。

团队将继续创建新功能，并将现有功能移植到新服务中。当所有必需的功能都从遗留的整体式应用程序中淘汰后，它就可以退役了。

***注意事项:****外观*与新服务一起不断发展。一些数据存储有可能被新的和遗留的服务使用。新的服务应该以一种容易被截取的方式来构建。在某个时候，迁移应该已经完成，扼杀者*外观*要么消失，要么演变成网关或适配器。

#### 基于队列的负载均衡

> “负载可能是不可预测的，当出现间歇性的高负载时，一些服务无法扩展。需求高峰可能导致过载，淹没下游服务，导致它们失败。
> 
> 在服务之间引入一个队列作为缓冲区可以缓解这些问题。存储消息并允许消费者以自己的速度处理负载。"

借助队列来缓冲服务是非常常见的。在微软的云架构模式中，它被称为[基于队列的负载均衡模式](https://docs.microsoft.com/en-us/azure/architecture/patterns/queue-based-load-leveling)，崔琰称之为[解耦调用](https://read.acloud.guru/applying-the-decoupled-invocation-pattern-with-aws-lambda-2f5f7e78d18)，杰里米·戴利称之为[可扩展 Webhook](https://www.jeremydaly.com/serverless-microservice-patterns-for-aws/#scalablewebhook) 。

队列将*任务*从*服务中分离出来，*创建了一个缓冲区来保存对扩展性较差的后端或第三方服务的请求。

无论请求量有多大，处理负载都是由使用者驱动的。低并发和批量大小可以控制工作负载。

[![](img/d767d21824ba8359f578210045f27ada.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4l--rYJT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-6QcmhOklq1FcD81Fof_iQ.png)

在示例中，我们有一个迁移工作进程，它读取 Elasticsearch 索引的内容。Elasticsearch 它很快。工人可以阅读成千上万的文章，并获取它们的依赖关系(作者、类别、标签、节目、资产等)。)不到一秒钟。

在右侧，我们有一个需要接收所有内容的服务，但是在我们创建一篇文章之前，我们必须以特定的顺序创建它的所有关系，仔细检查它们是否存在，并且需要更新，这比较慢。即使我们横向扩展服务——我们确实这样做了——它背后的关系数据库也会成为瓶颈。

在某个点(大约 100k 到 500k 篇文章)之后，查询数据库会慢下来，因为在[拥有和属于多](http://archive.oreilly.com/oreillyschool/courses/Rails2/many_to_many.html)关系表上有一些锁定。

通过限制批处理大小和并发运行的工作线程数量，我们可以维持一个缓慢但稳定的流程，从而减少数据库中的锁争用。

[![](img/49fb6da1d94ca4f7ee60984a946b7031.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KQkH5O55--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AH_dlsZOyQcka3GbEPiAh-Q.png) 

<figcaption>缓冲繁忙的公共 API 的请求。</figcaption>

另一个常见的例子是使用 SQS 来缓冲 API 请求，以分摊流量峰值——如上图所示。

端点向客户端返回 [202 —接受的](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/202)，带有事务 id 和结果的位置。在客户端，UI 可以向模拟预期行为的用户提供反馈。

该服务可以自行在后台处理请求。即使涉及长时间运行的进程，客户端负载的增加也不会影响系统的吞吐量和响应能力。

***注意事项:*** 当服务需要同步响应(等待回复)时，这种模式没有帮助。值得注意的是，不使用并发限制会降低模式的有效性:AWS Lambda 和 Kinesis 可以快速伸缩，压倒弹性较小或伸缩较慢的下游服务。Zalando 的 API 指南包含了一个关于事件的[完整章节](https://opensource.zalando.com/restful-api-guidelines/#events)，讨论了这种模式的一些重要考虑事项。

#### 读-重报告引擎

> “对于读取量大的应用程序来说，达到下游数据引擎的极限是很常见的，这些引擎不是专门针对客户使用的不同查询模式的。
> 
> 缓存数据并为查询最多的数据创建专门的视图有助于减轻读取量大的服务的负载影响。"

以前的模式有助于解决将数据和事件从一个服务推送到另一个服务的问题，优化写入密集型服务的规模。这种模式针对客户需要的不同的*访问*和*查询模式*优化数据，优化读密集型服务的规模。

大多数应用程序都是读取密集型的。对于我们的大型媒体客户端来说尤其如此，在这些客户端中，生成内容的用户远远少于消费内容的用户。它们之间的比例可以很大，比如 1:100000。

缓存数据和创建最频繁访问模式的专门视图有助于服务有效扩展。

缓存意味着将经常使用的内容临时复制到内存或共享存储库中。[缓存数据](https://docs.microsoft.com/en-us/azure/architecture/patterns/cache-aside)是提高性能和扩展读取最常用的策略之一。

在[物化视图](https://docs.microsoft.com/en-us/azure/architecture/patterns/materialized-view)中，数据以一种为查询和显示而优化的方式被复制和转换。物化视图可以是新的表或完全不同的数据存储，其中数据被映射为以新的格式显示，或者被限制为数据的特定子集。

物化视图也有助于“桥接”不同的存储，以利用它们更强大的功能。

使用[索引表](https://docs.microsoft.com/en-us/azure/architecture/patterns/index-table),数据被复制到一个新的表中，该表使用特定于公共查询的专用索引。专门化的索引可以是组合键、辅键和部分非规范化的键。

[![](img/dcd939616fd4d2c20727b16dfb440035.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ivbT47Je--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhibCn1a_yqdkprm8TkgCbQ.png) 

<figcaption>使用物化视图和索引表进行社交 app 上流行的查询模式。</figcaption>

Dynamo 流和 lambda 函数是创建专门化视图的完美工具。在这个例子中，我们有三个端点——搜索、推文和时间轴。每一种都需要稍微不同的查询模式，其中数据需要以特定的方式进行优化。

/search 查询 Elasticsearch 中的 Tweets 索引。提供语音搜索、错别字、相关术语和建议。没有必要索引原始推文中的所有数据，可能只包括文本、位置、媒体 URL——以获得漂亮的预览和标签。我们使用该流触发 TweetCreated 上的 lambda，该 lambda 剥离所有我们不需要的数据，并对 tweet 进行索引。

/timeline 是根据网络上最有趣的推文，以及我的人脉活动创建的。

我们使用一个 Dynamo 表来保存最热门的 Tweets——一个被索引的表限制在 1000 个被浏览最多的条目。Tweets 通过 TweetViewed 事件的流进行更新。lambda 函数接收事件，查询 Tweets 集合，并保存结果。

在像 [Neptune](https://aws.amazon.com/neptune/) 这样的图形数据库上，获取某人的连接活动更容易。TweetCreated 事件触发的另一个 lambda 在 Neptune 上为我们的连接的推文流创建了一个维护活动的记录。

在微软的云架构中，这是不同模式的混合——数据管道、[缓存备用](https://docs.microsoft.com/en-us/azure/architecture/patterns/cache-aside)、[物化视图](https://docs.microsoft.com/en-us/azure/architecture/patterns/materialized-view)和[索引表](https://docs.microsoft.com/en-us/azure/architecture/patterns/index-table)。

***注意事项:*** 处理缓存可以[硬](https://en.wikipedia.org/wiki/Cache_invalidation)，切记遵循行业[最佳实践](https://en.wikipedia.org/wiki/Cache_invalidation)。维护物化视图和索引表可能会很复杂。复制数据会增加成本、工作量和逻辑性。对于非常大的数据集，很难保持一致性，保持数据同步会降低系统速度。视图和索引表不完全一致。

#### 溪流和管道

> 一个连续的流处理器，可捕获大量事件或数据，并在它们到达时尽快将其分发到不同的服务或数据存储

我们经常看到的一个重要特性是处理数据流，其速度与数据生成的速度一样快，并且可以快速扩展以满足大量事件的需求。下游服务接收流并应用业务逻辑来转换、分析或分发数据。

常见的例子是捕捉用户行为，如点击流和用户界面交互。数据用于分析。物联网传感器的数据等。

[![](img/4b2c5ecc383cb826438cfb86390a094a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--04y0CDd0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0oPw3DvpApBTZ50HNrnw-g.png) 

<figcaption>使用 Kinesis Streams、Analytics 和 Athena 构建数据管道。</figcaption>

在示例中，a [Kinesis 流](https://aws.amazon.com/kinesis/data-streams/)接收来自服务 a 的事件。数据使用 lambda 函数进行转换，存储在 DynamoDB 中以便快速读取，并在 Elasticsearch 中建立索引以获得良好的搜索用户体验。

[Kinesis Analytics](https://aws.amazon.com/kinesis/data-analytics/) 提供快速[实时查询流中的数据](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/streaming-sql-concepts.html)。通过 S3 集成，所有数据都被存储起来，以备将来分析和真正洞察。 [Athena](https://aws.amazon.com/athena/) 提供所有历史数据的查询。

***注意事项:*** 流处理可能会很昂贵，如果数据集很小或者只有几个事件，可能不值得。所有云提供商都提供数据管道、流处理和分析产品，但它们可能无法与不属于其生态系统的服务很好地集成。

#### 扇出和扇入

> “大型作业或任务很容易超过 Lambda 函数的执行时间限制。使用分而治之的策略有助于缓解这个问题。
> 
> 这项工作由不同的 lambda 工作人员分担。每个工人将异步处理作业，并将结果的子集保存在一个公共存储库中。
> 
> 最终结果可以由另一个流程收集和拼接在一起，也可以从存储库本身查询。"

**扇出**和**扇入**是指将一个任务分解成子任务，并发执行多个功能，然后聚合结果。

它们是一起使用的两种模式。扇出模式消息被传递给工作人员，每个工作人员接收原始任务的一个分区子任务。扇入模式收集所有单个工作人员的结果，聚合它，存储它，并发送一个表示工作完成的事件。

[![](img/91b1d309b0a2b688aa87c36b45f5129e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mwrdm2Hq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A8bBnJqOLyZmkiwNeSpUVHQ.png) 

<figcaption>数字资产管理系统(DAM)的后端引擎——无服务器工作负载的经典例子。</figcaption>

调整图片大小是[中的](https://read.acloud.guru/serverless-image-optimization-and-delivery-510b6c311fe5)[之一](https://cloudonaut.io/serverless-image-resizing-at-any-scale/)最常见的 [中的](https://sketchboard.io/blog/serverless-image-resize-with-amazon-lambda) [例子](https://aws.amazon.com/solutions/serverless-image-handler/)关于使用无服务器。这就是扇出方法。

客户端将原始图像上传到资产 S3 存储桶。API 网关有一个处理直接上传到 S3 的集成。

λ函数由 S3 触发。让一个 lambda 函数做所有的工作会导致限制问题。相反，lambda 将资产创建事件推送到 SNS，以便我们的处理 lambda 开始工作。

右边有三个用于调整大小的 lambda 函数。每个都创建不同的图像大小，将结果写入 Renditions 桶。

底部的 lambda 从原始来源读取元数据——位置、作者、日期、相机、大小等。并将新资产添加到 DynamoDB 上的 DAM 的资产表中，但没有将其标记为可供使用。智能自动标记、文本提取和内容审核可以添加到稍后使用 [Rekognition](https://aws.amazon.com/rekognition/image-features/) 处理的 lambdas 中。

[![](img/217e5d6d362f3875e30032063b85336a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qAx3mzoS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQMsQOv8R3JB4XfLx2WJ80Q.png) 

<figcaption>经理 App 通过 DAM API 消耗资产。</figcaption>

对于 DAM 的扇入部分，我们有一个 lambda 函数，它监听 renditions 桶，当桶上有变化时，它检查是否所有的 renditions 都准备好了，并标记资产可供使用。

由于无服务器的事件驱动特性，以及 lambda 函数的资源限制，我们更喜欢这种编排[而不是编排](https://specify.io/concepts/microservices#choreography)。

***注意事项*** :并不是所有的工作负载都可以被分割成足够小的块用于 lambda 函数。两个流程都应该考虑失败，否则任务可能会永远无法完成。利用 lambda 的[重试策略](https://docs.aws.amazon.com/lambda/latest/dg/retries-on-errors.html)和[死信队列](https://docs.aws.amazon.com/lambda/latest/dg/dlq.html)。任何超过 15 分钟的任务都应该使用容器而不是 lambda 函数，坚持使用*编排方法*。

### 更多资源📚

*   微软的 Azure Architecture Center 有一个广泛的云模式列表，一个关于 T2 最佳实践的可靠指南，一个关于 T4 性能反模式的很好的概述和一些例子。
*   AWS 还拥有大量关于[分布式系统组件](https://docs.aws.amazon.com/aws-technical-content/latest/microservices-on-aws/distributed-systems-components.html)、[一般无服务器](https://aws.amazon.com/serverless/)、[无服务器应用程序库](https://aws.amazon.com/serverless/serverlessrepo/)中的示例以及几个[网络研讨会视频](https://aws.amazon.com/about-aws/events/monthlywebinarseries/on-demand/?awsf.ott-on-demand=categories%23serverless&awsf.ott-on-demand-master=categories%23serverless)的资源。
*   杰瑞米·戴利有一个[无服务器模式](https://medium.com/@jeremydaly/serverless-microservice-patterns-for-aws-6dadcd21bc02)的详细列表。他是轻量级 API 框架 [Lambda API](https://www.jeremydaly.com/projects/lambda-api/) 和用于管理 Lambda 函数连接的无服务器 MySQL [模块](https://www.npmjs.com/package/serverless-mysql)的作者。
*   [崔琰](https://medium.com/u/d00f1e6b06a2)有一吨关于[无服务器](https://theburningmonk.com/topics/programming/aws/serverless/)的内容。包括[设计模式](https://theburningmonk.com/topics/programming/aws/serverless/)、[性能](https://theburningmonk.com/topics/programming/performance-programming/)、[的](https://www.protego.io/the-serverless-show-ft-yan-cui-do-we-ever-learn/)[批次](https://www.realworlddevops.com/episodes/the-business-value-of-serverless-with-yan-cui)、[视图](https://www.trek10.com/blog/think-faas-serverless-in-production/)的以及无服务器上的最佳[训练。他是无服务器场景中的必选项。](https://productionreadyserverless.com/)
*   迈克·罗伯特 [写](https://martinfowler.com/articles/serverless.html)[深入讨论](http://thoughtworks.libsyn.com/diving-into-serverless-architecture)无服务器。
*   [萨沙·☁·米勒林](https://medium.com/u/4858a304914)谈论[无服务器架构模式和最佳实践](https://www.infoq.com/presentations/serverless-architecture-patterns)。
*   罗布·格鲁尔在诺德斯特龙的[活动采购，关于](https://medium.com/tech-at-nordstrom/adventures-in-event-sourced-architecture-part-1-cc21d06187c7)[你好零售的帖子！](https://read.acloud.guru/serverless-event-sourcing-at-nordstrom-ea69bd8fb7cc)，还有关于[他们架构的视频](https://www.youtube.com/watch?v=O7PTtm_3Os4)。
*   [斯沃德利](https://medium.com/u/280dd9543ba9)和[福里斯特厚颜无耻](https://medium.com/u/b8d1e8a0e0d8)关于[集装箱 vs 无服务器](https://read.acloud.guru/simon-wardley-is-a-big-fan-of-containers-despite-what-you-might-think-18c9f5352147)的讨论。
*   Gregor Hohpe 的[企业集成模式](https://www.enterpriseintegrationpatterns.com/patterns/messaging/)是 65 种技术独立模式的目录，其中一些模式是他移植到谷歌云中的[无服务器模式](https://www.enterpriseintegrationpatterns.com/ramblings/google_cloud_functions.html)。
*   Twitch 的[为 Twitch Scale 设计的模式](https://www.twitch.tv/videos/335360204?collection=8zVaW4B4YhU1pA)触及了几个有趣的点，关于可以应用于 Scale 的模式。
*   [Capital One Tech](https://medium.com/u/73d9ee9c08c8) [【何时反应 vs .编排】](https://medium.com/capital-one-tech/microservices-when-to-react-vs-orchestrate-c6b18308a14c)是一篇关于编排 vs .编排的优秀帖子。