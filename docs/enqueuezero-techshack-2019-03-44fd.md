# EnqueueZero Techshack 2019-03

> 原文：<https://dev.to/soasme/enqueuezero-techshack-2019-03-44fd>

# enqueue zero Techshack 2019-03

最初发表于[https://enqueuezero.com/techshack.weekly/2019-03.html](https://enqueuezero.com/techshack.weekly/2019-03.html)

## GCP 事件 19001

[status.cloud.google.com](https://status.cloud.google.com/incident/appengine/19001)

总而言之，问题是无法创建应用程序。根本原因是测试环境和生产环境中的配置不同。最终导致所有数据写入元数据存储失败。

解决方案是对元数据存储的模式实施额外的验证，并确保元数据存储配置的测试验证与生产相匹配。虽然没有提到金丝雀部署。

## 解读卡夫卡的一次语义

[dzone.com](https://dzone.com/articles/interpreting-kafkas-exactly-once-semantics)

这篇文章介绍了 Kafka 中的恰好一次交付语义。

有三种交付保证:

1.  最多一次交货。
2.  至少一次交货。
3.  正好一次交货。尤其是最重要也是最难的。

问题 1，消息正在被处理并发送到消息队列，但从未得到确认。而且我们也不想发多次。
问题 2，正在处理消息，然后应用程序崩溃。现在，一旦我们带回应用程序，它重新读取消息，因此消息被双重处理。

为了避免这两个问题，卡夫卡的解决方案是使用以下技巧。

*   幂等生产者。实际上，每个消息都有`idempotence=true`和一个唯一的事务 id。
*   跨分区的事务。读写可以放到一个事务中，所以要么全有，要么全无。
*   设置事务隔离级别`read_commited`。

## 迈向成功的弹性软件设计

[infoq.com](https://www.infoq.com/articles/towards-resilient-software-design)

今天的分布式系统需要有弹性。简而言之，弹性是一种理想的方式，如果发生意外故障，用户根本不会注意到，或者用户至少可以继续使用降级的应用程序。

弹性软件设计的挑战包括:

*   理解商业案例。
*   理解分布式系统是不确定的。
*   不可能达到 100%可用。即使你努力去做，也要付出代价。
*   建立 Ops-Dev 反馈回路。
*   获得正确的服务依赖和功能设计。
*   理解模式。
*   跟上新技术。

## 设计弹性系统:断路器还是重试？

第一部分 | [第二部分](https://engineering.grab.com/designing-resilient-systems-part-2)

这两个职位包括断路器，重试，抖动，回退等。

简短结论:

*   当其中一个服务实例出现问题时，每主机断路器将隐藏问题。
*   仅使用重试将导致级联失败。
*   断路器(每服务)优于重试，重试优于断路器(每主机)。
*   一起使用断路器和重试。如果我们再次尝试 10%的失败请求，90%的请求会在第二次尝试时通过。我们的成功率将从最初的 90%上升到 90% + ( 90% x 10%) = 99%

## 生产方针

[medium.com](https://medium.com/@rakyll/production-guideline-9d5d10c8f1e)

这篇文章包括了一系列对生产服务有用的技巧。

*   构建不应该依赖于外部系统。
*   在设计阶段记录 SLO。记录版本如何影响 SLO。
*   记录外部服务的可用性。
*   通过复制资源或使用硬编码的回退来避免 SPOF。
*   非机密配置可以是命令行标志。
*   如果系统依赖于配置系统，能够回退。
*   不要从产品配置继承开发配置。
*   记录整个发布过程，包括金丝雀发布。
*   如果可能，自动恢复鸭翼。
*   确保回滚可以使用与推出相同的过程。
*   收集指标。
*   区分客户端和服务器端数据。
*   调整提醒以减少工作量。
*   传播跟踪上下文。
*   加密所有请求。
*   使用 VPN、IAM、ACL、审计，...
*   净化用户输入。
*   避免触发大量内部扇出的外部端点。
*   记录服务如何扩展、需要多少资源、配额和资源限制。
*   负载测试。

## 编写程序

[composingprograms.com](https://composingprograms.com/)

这是一个免费的编程和计算机科学在线介绍。它基于《SICP》一书，并使用 Python 3 进行了翻新。

*   编程元素:表达式、组合和抽象！
*   用函数构建抽象的技术是调用函数、组合高阶函数和递归函数。
*   用数据构建抽象的技术是使用列表、面向对象模型、流等。
*   当我们说编程时，我们实际上是在编写另一种语言。第三章解释了如何实现一个解释器语言。
*   编程范例:
    *   隐含序列。它使用了基本的流数据结构。
    *   逻辑编程。它使用事实和逻辑查询作为构建模块。
    *   分布式计算。消息成为客户机-服务器体系结构中对等体之间的基本元素。
    *   分布式数据处理。意思是地图缩小模式，分割然后征服。
    *   并行计算。它是关于线程、多处理、锁定共享状态的。

## 为什么 Kubernetes 上的存储这么难？

[softwareengineeringdaily.com](https://softwareengineeringdaily.com/2019/01/11/why-is-storage-on-kubernetes-is-so-hard/)

Kubernetes 很好，因为它提供了管理容器的动态架构。然而，持久存储解决方案无法承受这种动态行为。我们不能动态地创建和销毁持久存储，因为数据就在其中。

在生产中，开发人员通常依赖外部存储。Kubernetes 提供批量插件。
以前，批量插件都是手工制作成 Kubernetes 核心代码库，不够灵活。
随着 CSI 和 Flexvolume 的推出，卷插件可以部署在集群上，而无需更改代码库。

现在，我们可以使用外部供应商提供的 PV、PVC、StatefulSet 和 StorageClass 管理卷。如果你喜欢云原生 OSS 解决方案，试试 Ceph 和 Rook。

## 开源我们的 Kubernetes 工具

[engineering.tumblr.com](https://engineering.tumblr.com/post/182013497734/open-sourcing-our-kubernetes-tools)

Tumblr 工程团队开源了他们的几个 Kubernetes 工具。

*   [k8s-边车注射器](https://github.com/tumblr/k8s-sidecar-injector)。通过定义一个配置映射，将一个记录器容器注入到您的 pod 中是有帮助的。
*   [k8s-配置-喷油器](https://github.com/tumblr/k8s-config-projector)。它应该由 CI 来管理。它从配置报告中提取代码和数据，并填充到 ConfigMap 中。
*   [k8s-secret-injector](https://github.com/tumblr/k8s-secret-projector) ，同样，这个工具将凭证信息注入 secret。

## Kubernetes 安全最佳实践

[cncf.io](https://www.cncf.io/blog/2019/01/14/9-kubernetes-security-best-practices-everyone-must-follow/)

*   升级至最新版本
*   启用基于角色的访问控制(RBAC)
*   使用命名空间建立安全边界
*   分离敏感工作负载
*   安全的云元数据访问
*   创建和定义群集网络策略
*   运行集群范围的 Pod 安全策略
*   强化节点安全性
    *   确保主机安全且配置正确。
    *   控制对敏感端口的网络访问。
    *   尽量减少对 Kubernetes 节点的管理访问。
*   打开审核日志记录

## 在 Dropbox 上自动化数据中心运营

[blogs.dropbox.com](https://blogs.dropbox.com/tech/2019/01/automating-datacenter-operations-at-dropbox/)

Dropbox 的工程师发明了 Pirlo 系统，以自动化他们在数据中心运营方面的工作。

从高层次来看，Pirlo 由一个内部构建的分布式 MySQL 支持的作业队列组成，该队列使用了 Dropbox 产品中的许多原语，如 gRPC、服务发现和我们的托管 MySQL 集群。

*   `job`是 MySQL 数据库中的一个表。
*   SQLAlchemy 是用于操作作业数据的 SQL 库。
*   将 SQLAlchemy 和管理逻辑封装到线程中。
*   工作线程在不同的线程中运行，消耗作业。

Pirlo 服务器验证是另一个 Pirlo 组件，用于处理服务器供应和维修验证。
同样，TOR Starter 是处理交换机供应的 Pirlo 组件。它验证和配置机架中的交换机。
这些组件继承了使用 SQLAlchemy 库和作业表的相同过程，只是根据自己的需要添加了一些字段。

## 论大规模基础设施:分布式系统的连锁故障

[medium.com](https://medium.com/@daniel.p.woods/on-infrastructure-at-scale-a-cascading-failure-of-distributed-systems-7cff2a3cd2df)

关键要点是:

*   更小的星团，更多的星团。
*   共享 Docker 守护进程是一个脆弱的故障点。
*   让每个工作负载都有自己的日志和公制边车是正确的事情