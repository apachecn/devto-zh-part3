# Kubernetes EC2 自动缩放带来乐趣和利润

> 原文：<https://dev.to/liquid_chickens/kubernetes-ec2-autoscaling-for-fun-and-profit-1mic>

我被分布式系统和抽象平台的难题吸引住了——这些问题只有在大量移动部件协同工作时才会出现(或者不协同工作！).

最近，在平台迁移到 AWS EKS 几周后，我就遇到了这样一个问题。

## 症状

最初的问题表现为应用程序异步工作问题。

1.  异步进程队列开始堆积并触发警报。
2.  对工作进程的调查显示:
    *   工人报告健康
    *   工作线程似乎达到了每个工作线程的最大数量
    *   员工使用最少的计算资源
    *   队列中的一些正在被处理
3.  重新部署异步 worker Kubernetes (k8s) pods 解决了眼前的问题，队列又开始耗尽。

在队列开始堆积的同时，我们的核心应用程序报告了少量失败的数据库请求。这让我们看到了网络和我们的 DB 连接池，pgbouncer，两者看起来都很好。**然而**，在我们看到队列问题的几分钟前，几个 pgbouncer k8s pods 已经迁移到不同的 k8s 节点。

这让我们看到了节点自动缩放。运行迁移的 pgbouncer pods 的节点已自动缩减，强制在另一个节点上重新启动。这是预期的行为。然而，出乎意料的是，我们的异步工作线程与 pgbouncer 的连接没有超时，也没有尝试重新连接。

异步工作线程持有永远不会完成或失败的打开的连接，拖延它们并阻止它们处理新的队列项目。

## 试图修复

为了深入 k8s 的短暂性和无状态性，我们从几个角度来处理这个问题:

1.  [活动探测器](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) w/ a DB 连接健康检查–我们已经为 pgbouncer 和大多数其他组件配置了这些探测器，但没有为我们的异步工作器配置。考虑到这个选项，我们怀疑会有假阴性的问题，所以决定暂时搁置。
2.  异步工作数据库连接超时——这导致了全局应用程序的分支，因为它需要重新配置 pg gem 或 Rails ActiveRecord，这两个选项看起来都很糟糕，测试后发现实际上非常糟糕。
3.  为 pgbouncer 配置一个 k8s [容器生命周期钩子](https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/)。这种做法已经存在，但似乎没有持续发挥作用。
4.  只为 pgbouncer 设置一个专用节点池，并禁用自动缩放。这似乎是 k8s 中大多数运行 pgbouncer 的人都在做的事情，但从哲学上来说感觉不好，所以我们把它放在一边作为最后的手段。

大多数努力集中在生命周期挂钩选项上。确保 pgbouncer 收到的是 SIGINT 而不是 SIGTERM，让它安全地关闭正在运行的连接并拒绝新的连接。这看起来像是要解决问题。

它没有。

## 悲伤……然后是希望

[![](img/8435cf763e4e3297fa7f93ffa55c0a65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qjuPzeDW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://chrisdodds.net/wp-content/uploads/2019/05/proxy.duckduckgo.jpg)

这个问题困扰了我们几个星期，当时我们正在处理更高优先级的项目并进行研究/测试。这个问题总是与自动缩小联系在一起，并且几乎在同一时间发生，但不是每天或每次 pgbouncer 托管节点缩小时都会发生。

在用尽了有吸引力的选项后，我们为 pgbouncer 构建了一个专用的节点池，并开始在 QA 中测试它。同样，这感觉很糟糕——将静态组件添加到动态架构中。

在部署到生产环境之前，我们进行了另一次队列备份。

我们在上次自动缩放事件中查看了 pgbouncer 日志，注意到**SIGINT 或 SIGTERM 都没有通过容器的生命周期 preStop 钩子被调用。然后我们查看了*如何*自动缩放节点，并与发出 SIGINT 的事件进行了比较(并且触发了 preStop 钩子)。**

当 [*k8s 集群自动缩放器*](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler) 负责自动向下缩放一个节点时，发送 SIGINT，pgbouncer 正常关闭。当 *AWS* autoscaling 执行重新平衡(确保在每个可用性区域中运行相同数量的实例)时，SIGINT 或 SIGTERM 都没有被发送到 pgbouncer pod，它很不礼貌地终止了。

**这解释了为什么这个问题一直不一致——它只是在 k8s 集群自动缩放器缩小后发生的，然后*和*AWS 自动缩放器跨可用性区域执行重新平衡，并且恰好选择了一个带有 pgbouncer 的节点。**

事实证明，这是一个已知的问题，虽然记录不多。在 [Kubernetes Autoscaler 文档中发现:](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/cloudprovider/aws/README.md)

> 群集自动缩放器不支持跨多个可用性区域的自动缩放组；相反，您应该为每个可用性分区使用自动扩展组，并启用[–balance-similar-node-groups](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#im-running-cluster-with-nodes-in-multiple-zones-for-ha-purposes-is-that-supported-by-cluster-autoscaler)功能。如果您使用跨多个可用性区域的单个自动扩展组，您会发现 AWS 意外地终止节点，而不会因为[重新平衡功能](https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-benefits.html#arch-AutoScalingMultiAZ)而耗尽节点。

这正是我们想要的。德尔普。

人们使用的变通方法:

1.  文档中的事情——在每个 AZ 中创建单独的 AWS 自动缩放组，并让 k8s 集群自动缩放器使用——balance-similar-node-groups 标志处理平衡。这有点难看，并且在 AWS 端引入了更多的伸缩和负载平衡器复杂性。
2.  创建一个与 AWS 自动扩展生命周期挂钩相关的节点 drain Lambda，该挂钩暂停终止并在被缩减的节点上发出 drain 命令，以让 pod 优雅地迁移。几乎没有关于这个解决方案的文档。[在我们发现根本原因的大约一周前，一个例子被添加到 AWS-Samples repo](https://github.com/aws-samples/amazon-eks-node-drainer) 中。

## “我怎么来了？”

[![](img/a7a8b7dab30ddf1f3a9634503e0a90fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UxbvSIRY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://chrisdodds.net/wp-content/uploads/2019/05/hqdefault.jpg)

这很简单，至少事后看来是这样。

Kubernetes 有很多地方让人感觉很神奇，尤其是当你对它相对陌生的时候。其中一些是通过设计实现的，它旨在抽象出底层基础架构的许多难题/细节。在这种情况下，我过于相信集群自动缩放器的自动逻辑，并假设它比现在更紧密地集成到 AWS 中。

这不是抱怨，更多的是证明了 Kubernetes 生态系统总体上是多么令人印象深刻。一般来说**是**自动的，所以很容易做出假设。这只是 automagic 还没有达到的边缘情况。