# EnqueueZero Techshack 2018-51

> 原文：<https://dev.to/soasme/enqueuezero-updates-2018-51-2ga4>

# enqueue zero Techshack 2018-51

起亚奥拉！我是**零入队**([https://enqueuezero.com](https://enqueuezero.com))的创建者，这是一个向开发者/开发者/系统管理员解释代码原则的网站。本网站自 2018 年起在单人团队下更新。这个系列由我写的或者在网上找到的一些有趣的帖子组成。更重要的是，我希望这些帖子能让你满意。

为了帮助我更有动力和保持这个网站无广告，感谢您的捐助！

[![Donate this project using Patreon](img/d8ef9eb3c1d7813b01d7070f13d6bc49.png)](https://www.patreon.com/join/enqueuezero)

喜欢读本周的帖子。

## 状态站点

[questionzero . com](https://enqueuezero.com/status-site.html)

状态站点是一个单独的网站，列出了构成产品的特定组件状态。

它显示下面两条信息:

*   显示业务的每个功能的状态。
*   显示每天组织的事件列表。如果什么都没发生，显示“没有事件报告”否则，显示事件的详细信息，例如事件的检测时间、事件的处理方式以及事件的解决时间。

它有以下目标:

*   展示平台的可靠性。
*   展示平台从故障中恢复的情况。
*   展示性能的演变过程。

状态站点显示产品组件的状态。它将对用户透明。用户确切地知道哪里有停机时间，员工将根据他们知道的最新信息采取行动。

## 可用性

[questionzero . com](https://enqueuezero.com/availability.html)

可用性清楚地定义了系统在向客户提供服务方面的成功程度。提高服务的可用性很难。请注意，重要的不是交付商定的 SLA，而是交付给满意的客户。如果数字不错，但是客户不满意，那么您需要重新考虑整个事情，设置正确的测量目标，并再次验证。

## 脸书用 Zstandard 改进大规模压缩的 5 种方法

[code.fb.com](https://code.fb.com/core-data/zstandard/)

*   使用案例:压缩数百 GB 的数据进行备份\结合使用`-T`、`--long`、`--adapt`非常适合服务器备份。
    *   多线程压缩。使用`-T0`选项启用所有可用内核。
    *   [远程模式](https://github.com/facebook/zstd/releases/tag/v1.3.2) ( `--long`)。远程模式是一个串行预处理器，它查找大的匹配，并将其余的传递给并行 zstd 后端。![Long Range Mode](img/784e40d41293b47c8bf7c2d62ac65496.png)
    *   自动液位确定(`--adapt`)。自适应模式测量输入和输出文件或管道的速度，并调整压缩级别以匹配瓶颈。
*   用例:压缩仓库中的数据？\
    *   将块大小从默认的 32KB 增加到 256KB 会产生显著的增量压缩优势。
*   用例:压缩文件系统。
    *   在只读情况下增加了对 SquashFS 的 zstd 支持。
    *   在写入时复制的情况下增加了对 Btrfs 的 zstd 支持。
*   用例:数据库。
    *   混合压缩: [MyRocks](http://myrocks.io/) 是 RocksDB 上的 MySQL， [Rocksandra](https://instagram-engineering.com/open-sourcing-a-10x-reduction-in-apache-cassandra-tail-latency-d64f86b43589) 是 RocksDB 上的 Cassandra，ZippyDB 是建立在 RocksDB 之上的内部数据库..
    *   字典压缩:分析样本数据，假设要压缩的其余数据是相似的..一旦捕获了模式，字典就会假设未来的数据将是相似的，并立即开始压缩。
*   使用案例:托管压缩
    *   为了克服字典变得有状态，为预生成状态引入了`zstd --train`。它通过继续使用`compress()` / `decompress()`来简化接口。![Application Dictionary Compression Life-Cycle](img/28c68d3c82fa6433b9e838a9f9339ec2.png)

## setState 怎么知道要做什么？

[反应过度](https://overreacted.io/how-does-setstate-know-what-to-do/)

当你在一个组件中调用 setState 时，你认为会发生什么？\
答:每个渲染器在创建的类上设置一个特殊的字段。这个字段叫做`updater`。

```
// // Inside React DOM
const inst = new YourComponent();
inst.props = props;
inst.updater = ReactDOMUpdater;

// Inside React DOM Server
const inst = new YourComponent();
inst.props = props;
inst.updater = ReactDOMServerUpdater;

// Inside React Native
const inst = new YourComponent();
inst.props = props;
inst.updater = ReactNativeUpdater;

// A bit simplified
setState(partialState, callback) {
  // Use the `updater` field to talk back to the renderer!
  this.updater.enqueueSetState(this, partialState, callback);
} 
```

Enter fullscreen mode Exit fullscreen mode

## Kubernetes 中的调度算法

[github.com](https://github.com/kubernetes/community/blob/master/contributors/devel/scheduler_algorithm.md)

本文档说明了如何为未计划的 Pod 选择节点。这两个步骤是:

1.  筛选不符合 Pod 特定要求的节点。这可能是由于以下原因:
    *   没有磁盘、没有卷区域或达到最大卷数。
    *   没有 CPU，没有内存。
    *   主机端口已被占用。
    *   主机名不匹配，标签不匹配。
    *   节点报告内存/磁盘压力。
2.  对剩余的节点进行排序，找出最合适的节点。
    *   优先化由一组优先级函数来执行。
    *   优先级函数返回 0 到 10，表示从最不优选到最优选。
    *   `finalScoreNodeA = (weight1 * priorityFunc1) + (weight2 * priorityFunc2) + ...`
    *   算法:
        *   最少请求
        *   平衡资源
        *   最小化同一节点上的 pod 数量。
        *   首选已提取图像的节点。
        *   节点关联性

## Kubernetes 调度程序

[github.com](https://github.com/kubernetes/community/blob/master/contributors/devel/scheduler.md)

Kubernetes 调度程序作为一个进程与其他主组件(如 API 服务器)一起运行。

谓词是一组逐个应用的策略，用于过滤掉不合适的节点。

优先级是一组逐个应用于排序节点的策略(通过了谓词的过滤)。

```
For given pod:

    +---------------------------------------------+
    |               Schedulable nodes:            |
    |                                             |
    | +--------+    +--------+      +--------+    |
    | | node 1 |    | node 2 |      | node 3 |    |
    | +--------+    +--------+      +--------+    |
    |                                             |
    +-------------------+-------------------------+
                        |
                        |
                        v
    +-------------------+-------------------------+

    Pred. filters: node 3 doesn't have enough resource

    +-------------------+-------------------------+
                        |
                        |
                        v
    +-------------------+-------------------------+
    |             remaining nodes:                |
    |   +--------+                 +--------+     |
    |   | node 1 |                 | node 2 |     |
    |   +--------+                 +--------+     |
    |                                             |
    +-------------------+-------------------------+
                        |
                        |
                        v
    +-------------------+-------------------------+

    Priority function:    node 1: p=2
                          node 2: p=5

    +-------------------+-------------------------+
                        |
                        |
                        v
            select max{node priority} = node 2 
```

Enter fullscreen mode Exit fullscreen mode

## 干原理:用实例说明其成本

[web-techno.net](http://web-techno.net/dry-principle-explained/)

*   知识复制总是干违反原则的事。
*   代码复制并不一定意味着违反 DRY 原则。
    *   试图到处应用 DRY 会有两个结果:不必要的耦合和不必要的复杂性。
*   DRY 声明你不应该复制知识，而不是说你应该编码以便能够重用一切。
*   干是原则，不是规矩。任何事物的发展都是有成本的。干也不例外。显然，您不应该到处重复您的业务逻辑，但是您也不应该因为不想重复您的代码而将每件事情都紧密耦合。
*   注意不要提取重复的代码，让一切都依赖于它。

## 事件，Kubernetes 的 DNA

[mgasch.com](https://www.mgasch.com/post/k8sevents/)

*   Kubernetes:对来自 API 服务器的事件做出反应的自治进程。
*   流程:控制循环(observe -> analyze -> act)，或者事件的生产者和/或消费者(消费者也可以是生产者，反之亦然)，包括调度器、部署控制器、端点控制器、Kubelet 等。
*   事件:发生的不可改变的事实，例如“pod 已创建”。
*   API 服务器:一个不可变的(复制的)日志(或队列)，每个日志代表一个事件流。
*   对象和名称空间:API 服务器处理的专用(虚拟)事件队列。

[![API Server Event Queues](img/9f2c54b3400aa3f987dc2bfeb58d1059.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hZmsYj5b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i020nf95rlrzirivl3zk.png)

## 什么是 HTTP/3？

[开发到](https://dev.to/grigorkh/what-is-http3--4pib)

HTTP/3 = HTTP-over-QUIC。

QUIC(快速 UDP 互联网连接)是一种新的传输方式，与 TCP 相比，它减少了延迟。它使用更少的握手，改进前向纠错和拥塞控制，并避免 HoL 阻塞。

[![](img/b59eef7c0f3c45387b546cdf0c73f81e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YtOShaSm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/practicaldev/image/fetch/s--0hlAeTAi--/c_limit%252Cf_auto%252Cfl_progressive%252Cq_66%252Cw_880/https://cdn-images-1.medium.com/max/800/1%252Ar6NNOhOGncUfvHXKHUM39w.gif)

在过去，互联网糟透了，所以 TCP 协议设置了很大的超时和窗口。如今一切都变了，除了超时仍然是 20 秒。
如果您终止一个挂起 5 秒钟的 TCP 套接字，您的操作系统仍然会让它保持打开，直到 20 秒到期，从而消耗系统资源。

## 微服务是给人类的，不是给机器的

[medium.com](https://medium.com/@odedia/microservices-are-for-humans-not-machines-721a6a56344f)

这篇文章探讨了迁移到微服务架构的组织和文化优势。

其优势在于能够根据特定的负载需求，独立扩展每个微服务。缺点是分布式系统很难。

从组织的角度来看，它导致更小的(和更易维护的)代码仓库，团队之间更清晰的边界，等等。

微服务架构的目标之一就是让开发团队取得更好的成果。

作者认为从单片开始，在有意义的时候转移到微服务。

## 服务网格是否意味着中间件的终结？

[www.cloudops.com](https://www.cloudops.com/2018/09/does-the-service-mesh-spell-the-end-for-middleware/)

简单的答案是直到 DevOps 工具和实践的框架到位。

*   中间件的工作原理是将来自不同应用程序的消息传送到集中的通信池中。然后，这些消息通过一个函数管道传送，以用户注册服务结束。消息在企业服务总线(ESB)上传输。
*   消息在服务网格中传输，但是消息传递功能是与接收消息的服务一起执行的。每个实例都连接到一个代理，该代理在服务网格之间来回传递消息。

服务网格正变得越来越成熟，而中间件可能是在单一应用程序之间传输信息的理想选择。从中间件迁移到服务网格可能需要彻底改变应用程序和基础设施的方式。

## 用状态图可视化系统

[幻灯片](https://www.slideshare.net/bcantrill/visualizing-systems-with-statemaps)

一些有趣的观点:

*   系统在静默和不可见的情况下出错。
*   我们通过延迟等方式放大信号。
*   对一个系统的观察是必要的。
*   仪表数据的聚合对于缩放至关重要。
*   系统数据的可视化方式不止一种，而是多种。可视化是系统可观测性的一个重要方面。
*   状态图:一段时间内实体状态的可视化。它对 JSON 的有效负载进行操作，其中每一行对应于一个实体的状态转换。例如:

```
 {time: 1545005553, entity: 1, state: 0}
  {time: 1545005554, entity: 1, state: 1} 
```

Enter fullscreen mode Exit fullscreen mode

JSON 元数据头中描述了状态。例如:

```
 {
    title: "",
    states: {
        state1: {value: 0, color: #fff},
        state1: {value: 0, color: #fff},
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

## Airbnb SOA 迁移

[陈述和文字记录](https://www.infoq.com/presentations/airbnb-soa-migration)

*   由于复杂的模块、业务的增长和事件数量的增加，他们决定迁移到 SOA。
*   为什么是 SOA？SOA 是一个松散耦合的服务网络。它在许多组织中得到了很好的验证。
*   两条设计原则:
    *   服务应该对自己的数据拥有读写权限。
    *   数据突变应该通过标准事件发布。
*   开始使用:
    *   自定义 ActiveRecord 适配器。
    *   将查询重新路由到服务。
    *   将服务组织成数据服务、中间层服务等，并设计它们的调用方向。
    *   增量迁移。
    *   让框架自动生成代码，进行标准测试和部署，通过 Diffy 实用程序重放生产流量。
    *   为了服务接口的稳定性，编写节俭的 IDL。
*   想法:
    *   SOA 是一项很高的投资成本。
    *   服务编排变得复杂。Kubernetes 为扩展服务而来。
    *   这是一个漫长的承诺和旅程。通过慢慢地、仔细地、一点一点地比较，确保你不会破坏功能。

## 应用数据缓存的演变:从 RAM 到 SSD

[网飞理工大学博客](https://medium.com/netflix-techblog/evolution-of-application-data-caching-from-ram-to-ssd-a33d6fa7a690)

*   背景:
    *   在所有地区之间快速安全地移动数据会大大增加成本和复杂性。
*   目标:
    *   提供了一种全局缓存解决方案，不仅速度快，而且成本低。
*   解决方案:
    *   用于缓存的固态硬盘。在固态硬盘上存储 1 TB 数据的成本远低于在 RAM 中存储相同数量的数据。
    *   将所有数据存储在 SSD (RocksDB)中，将活动/热数据存储在 RAM (Memcached)中。问题是当覆盖 RocksDB 上的现有数据时(对于压缩)，大型集群的性能很差。
    *   Memcached 外部存储(extstore):所有元数据(键和其他元数据)都存储在 RAM 中，而实际数据存储在 flash 中。
    *   将一些基于大 RAM 的 memcached 集群转移到相对较小的 extstore 集群中。
*   结论:
    *   所有生产集群都在运行 extstore。

## 实现网飞媒体数据库

[网飞理工大学博客](https://medium.com/netflix-techblog/implementing-the-netflix-media-database-53b5a840b42a)

*   概述:
    *   这篇文章讨论了 NMDB 的主要特点以及设计如何解决这些问题。
*   系统要求:
    *   支持结构化数据
    *   多租户和访问控制
    *   与遗留系统的集成
    *   SLA 保证
    *   查询的灵活性

## 洛基

[GitHub 项目](https://github.com/grafana/loki)|
T3 设计文件。

*   背景:
    *   度量(时间序列)仪表板和日志之间的工程需要额外的上下文切换。
*   它如何解决问题:
    *   将代理`promtail`安装到每个服务器或 pod，收集日志并将其发送到 loki。
    *   中央服务器`loki`索引日志流的元数据并存储它们。
    *   Grafana 作为仪表板，用于在类似普罗米修斯的查询中查询到`loki`的日志，例如`{app="mysql",name="mysql-backup"}`。
*   挑战:
    *   获取与时间序列/指标相关的元数据一致的可靠元数据。
    *   解决方案:使用与 Prometheus 相同的服务发现和标签重新标记库。
    *   选择块大小。它会是 4kb/s、8kb/s 还是更高？
    *   解决方案:这取决于操作成本与存储成本、块索引负载、构建块的内存成本与丢失风险、压缩效率。
    *   组块比普罗米修斯/皮层组块大很多数量级
    *   解决方案:支持流和迭代。

## Docker App

[简介](https://blog.docker.com/2018/12/docker-app-and-cnab/) |
[GitHub 项目](https://github.com/docker/app)

*   背景:
    *   您有几个想要部署应用程序的环境，它们的配置略有不同。
    *   你有很多类似的应用。
    *   撰写文件不容易共享。
*   解决方案:
    *   让您的 Docker 编写应用程序可重用，并在 Docker Hub 上共享它们
*   用法:

```
 # create a `docker-compose.yaml` and `hello.dockerapp`
  $ docker-app init --single-file hello

  # generate final docker-compose.yaml
  $ docker-app render | docker-compose -f - up

  # share the .dockerapp
  $ docker-app push --namespace ${myNamespace} --tag latest 
```

Enter fullscreen mode Exit fullscreen mode

阅读源代码:

[装载机](https://github.com/docker/app/blob/master/loader/loader.go)

```
metadata, compose, settings = readFile(filePath).Split("---")
return NewApp([metadata, compose, settings], options) 
```

Enter fullscreen mode Exit fullscreen mode

[渲染](https://github.com/docker/app/blob/master/render/render.go) :

```
# pseudo code
composeConfigFiles = Load(app.MetadataRaw(), settings.WithPrefix("app"))
    .FromFlatten(env)
    .Merge(app.Settings(), metaPrefixed, envSettings)

render(composeConfigFiles) 
```

Enter fullscreen mode Exit fullscreen mode

[模式检查](https://github.com/docker/app/blob/master/specification/schema.go)

```
gojsonschema.Validate(
    gojsonschema.NewStringLoader(string(schemaData)),
    gojsonschema.NewGoLoader(config)
) 
```

Enter fullscreen mode Exit fullscreen mode

## 什么是 CNAB？

[www.morethanseven.net](https://www.morethanseven.net/2018/12/15/what-is-cnab/)

*   多工具链安装程序模式的形式化。这里的多工具链尤其指 DSL/特定于云的/YAML 选择的矩阵。
*   基础设施即代码堆栈打包部分的商品化。这就像木偶和铁匠铺，厨师和超市，Ansible 和 Ansible 银河，Terraform 和模块注册表，你能想到的。CNAB 将其商品化，并为任何实现类似功能的人提供工具。

## CNAB 规格

[规格](https://github.com/deislabs/cnab-spec)

目标:捆绑、安装和管理分布式应用程序，这些应用程序在设计上与云无关。

概念:

*   CNAB:云原生应用捆绑包，一种格式。
*   bundle.json:它定义了包的元数据。
*   bundle.cnab:带有签名信息的 bundle.json。
*   瘦包:只有包定义的包。
*   厚包(thick bundle):一个包，包含包的定义，以及所有图像的解码数据。
*   调用映像:包的安装程序。它将依赖项安装到主机环境或平台。
*   执行映像:应用程序的依赖映像。
*   运行工具:调用映像的入口点。
*   声明:捆绑包的安装历史。
*   仓库:储存包的仓库。

## rak channel/scheduler

[GitHub 项目 URL](https://github.com/rakanalh/scheduler)

这是 Golang 的任务调度程序。它在预定义的时间后执行回调(goroutines)。

基本用法:

```
// Instantiate
noOpStorage := storage.NewNoOpStorage()
s := scheduler.New(storage)

// Define your own function.
func MyFunc(arg1 string, arg2 string)

// Run after 5 seconds
taskID := s.RunAfter(5*time.Second, MyFunc, "Hello", "World")

// Run every minute.
taskID := s.RunEvery(1 * time.Minute, MyFunc, "Hello", "World") 
```

Enter fullscreen mode Exit fullscreen mode

阅读源代码:

数据结构`Task`定义了将要执行的内容。从任务中，我们可以指示如何运行任务，并推导出任务下次运行的时间。

```
type Schedule struct {
    IsRecurring bool
    LastRun     time.Time
    NextRun     time.Time
    Duration    time.Duration
}

type Task struct {
    Schedule
    Func   FunctionMeta
    Params []Param
} 
```

Enter fullscreen mode Exit fullscreen mode

包`storage`添加、获取、删除任务，在特定的存储器上执行。Noop 的意思是保持在内存中。

数据结构`Scheduler`是调度所有任务的运行时。

```
type Scheduler struct {
    funcRegistry *task.FuncRegistry
    stopChan     chan bool
    tasks        map[task.ID]*task.Task
    taskStore    storeBridge
} 
```

Enter fullscreen mode Exit fullscreen mode

`Scheduler`的入口点是一个永远运行的循环。它尝试每秒运行所有挂起的作业。挂起的作业是从存储中选择那些应该在这一秒钟运行的作业。

```
// entryPoint:
go func() {
    ticker := time.NewTicker(1 * time.Second)
    for {
        select {
        case <-ticker.C:
            scheduler.runPending()
        case <-sigChan:
            scheduler.stopChan <- true
        case <-scheduler.stopChan:
            close(scheduler.stopChan)
        }
    }
}

...

// runPending:
for _, task := range scheduler.tasks {
    if task.IsDue() {
        go task.Run();
        if !task.IsRecurring {
            _ = scheduler.taskStore.Remove(task)
            delete(scheduler.tasks, task.Hash())
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重构大型函数

[robert.muth.org](https://robert.muth.org/refactoring-large-functions.html)

分解函数的策略:

*   闭包和循环是很好的重构对象。
*   像`do_x()`、`do_y()`这样的模式是可以重构的。

```
 // do x
  ...

  // do y
  ... 
```

Enter fullscreen mode Exit fullscreen mode

*   长参数列表:通过创建结构来绑定传递参数。
*   复杂 if:将谓词移到函数中。
*   早期编程。

```
 if !xxx:
      return

  if !yyy:
      return

  // main code 
```

Enter fullscreen mode Exit fullscreen mode

*   重构是一个很好的机会，可以确保函数中的所有代码都以大致相同的抽象层次结束。
*   每次都做些小改变。

原帖:[https://enqueuezero.com/readings/2018-51.html](https://enqueuezero.com/readings/2018-51.html)