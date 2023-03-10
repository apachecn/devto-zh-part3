# 最大限度地从 kubernetes 集群故障中吸取教训

> 原文：<https://dev.to/tbeijen/maximize-learnings-from-a-kubernetes-cluster-failure-3p53>

> 这篇文章最初是为我的个人博客写的

几个月以来，我们( [NU.nl](https://www.nu.nl) 开发团队)运营了少量的 Kubernetes 集群。我们看到了 Kubernetes 的潜力，以及它如何提高我们的生产力，如何改善我们的 CI/CD 实践。目前，我们在 Kubernetes 上运行我们的日志和构建工具集的一部分，加上一些面向小型(内部)客户的工作负载，计划在我们积累了知识和信心后，将更多的应用程序迁移到那里。

最近，我们的团队在一个集群上遇到了一些问题。没有完全关闭集群那么严重，但肯定会影响一些内部使用的工具和仪表板的用户体验。

巧合的是，大约在同一时间，我参观了慕尼黑的 DevOpsCon 2018，开幕式主题演讲[“保持活力:海底故障管理模式”](https://devopsconference.de/business-company-culture/staying-alive-patterns-for-failure-management-from-the-bottom-of-the-ocean/)与这一事件非常相关。

这个演讲(由 Twitter 的工程经理 Ronnie Chen 主讲)集中在如何让 DevOps 团队更有效地预防和处理故障。其中一个主题是灾难通常是如何由一连串的失败引起的，由此引出了下面这段话:

> 仅将事件归咎于根本原因的事后分析可能仅涵盖导致事件的大约 15%的问题。

正如在[中可以看到的，这份验尸模板列表](https://github.com/dastergon/postmortem-templates)，其中相当多的模板包含“根本原因”(复数)。然而，一连串的事件很容易被忽视，尤其是在很多情况下，消除或修复根本原因会使问题消失。

因此，让我们看看是什么样的失败导致了我们的事故，并最大限度地吸取教训。

## 事发

我们的团队收到了许多服务表现出异常行为的报告:偶尔的错误页面、缓慢的响应和超时。

在试图通过格拉夫纳进行调查时，我们经历了影响格拉夫纳和普罗米修斯的类似行为。从控制台检查集群会导致:

```
$: kubectl get nodes
NAME                                          STATUS     ROLES     AGE       VERSION
ip-10-150-34-78.eu-west-1.compute.internal    Ready      master    43d       v1.10.6
ip-10-150-35-189.eu-west-1.compute.internal   Ready      node      2h        v1.10.6
ip-10-150-36-156.eu-west-1.compute.internal   Ready      node      2h        v1.10.6
ip-10-150-37-179.eu-west-1.compute.internal   NotReady   node      2h        v1.10.6
ip-10-150-37-37.eu-west-1.compute.internal    Ready      master    43d       v1.10.6
ip-10-150-38-190.eu-west-1.compute.internal   Ready      node      4h        v1.10.6
ip-10-150-39-21.eu-west-1.compute.internal    NotReady   node      2h        v1.10.6
ip-10-150-39-64.eu-west-1.compute.internal    Ready      master    43d       v1.10.6 
```

节点`NotReady`，不好。描述各种节点(不仅仅是不健康的节点)显示:

```
$: kubectl describe node ip-10-150-36-156.eu-west-1.compute.internal

<truncated>

Events:
  Type     Reason                   Age                From                                                     Message
  ----     ------                   ----               ----                                                     -------
  Normal   Starting                 36m                kubelet, ip-10-150-36-156.eu-west-1.compute.internal     Starting kubelet.
  Normal   NodeHasSufficientDisk    36m (x2 over 36m)  kubelet, ip-10-150-36-156.eu-west-1.compute.internal     Node ip-10-150-36-156.eu-west-1.compute.internal status is now: NodeHasSufficientDisk
  Normal   NodeHasSufficientMemory  36m (x2 over 36m)  kubelet, ip-10-150-36-156.eu-west-1.compute.internal     Node ip-10-150-36-156.eu-west-1.compute.internal status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure    36m (x2 over 36m)  kubelet, ip-10-150-36-156.eu-west-1.compute.internal     Node ip-10-150-36-156.eu-west-1.compute.internal status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID     36m                kubelet, ip-10-150-36-156.eu-west-1.compute.internal     Node ip-10-150-36-156.eu-west-1.compute.internal status is now: NodeHasSufficientPID
  Normal   NodeNotReady             36m                kubelet, ip-10-150-36-156.eu-west-1.compute.internal     Node ip-10-150-36-156.eu-west-1.compute.internal status is now: NodeNotReady
  Warning  SystemOOM                36m (x4 over 36m)  kubelet, ip-10-150-36-156.eu-west-1.compute.internal     System OOM encountered
  Normal   NodeAllocatableEnforced  36m                kubelet, ip-10-150-36-156.eu-west-1.compute.internal     Updated Node Allocatable limit across pods
  Normal   Starting                 36m                kube-proxy, ip-10-150-36-156.eu-west-1.compute.internal  Starting kube-proxy.
  Normal   NodeReady                36m                kubelet, ip-10-150-36-156.eu-west-1.compute.internal     Node ip-10-150-36-156.eu-west-1.compute.internal status is now: NodeReady 
```

看起来节点的操作系统在`kubelet`能够回收内存之前就已经杀死了进程，正如[在 Kubernetes 文档](https://kubernetes.io/docs/tasks/administer-cluster/out-of-resource/#node-oom-behavior)中描述的那样。

我们集群中的节点是自动扩展组的一部分。因此，考虑到我们有间歇性的中断，当时到达 Grafana 有问题，我们决定逐个终止`NotReady`节点，看看新节点是否会保持稳定。情况并非如此，新节点正确出现，但一些现有节点或新节点很快进入状态`NotReady`。

然而,*确实导致了,*在普罗米修斯和格拉夫纳被安排在一个保持稳定的节点上，所以至少我们有了更多的数据来分析，根本原因很快就变得明显了...

## 根本原因

【Grafana 设置中的一个仪表板显示集群范围的总数以及 pod 内存和 cpu 使用情况的图表。这很快显示了我们问题的根源。

[![Pods memory usage, during and after incident](img/1d89365d1cae0b6dd58c31f775312d09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lSSauQYg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.tibobeijen.nl/img/learning_from_k8s_failure_grafana_pod_memory.png)

那些不知通向何处的线路都是运行着 [ElastAlert](https://github.com/Yelp/elastalert) 的吊舱。对于日志，我们有一个 Elasticsearch 集群正在运行，最近我们一直在试验使用 ElastAlert 来触发基于日志的警报。事件发生前不久引入的一个警报是，如果我们的`Cloudfront-*`索引在一定时间内没有接收到新文档，就会触发该警报。由于 Cloudfront 发行版的吞吐量是每小时几百万个请求，这显然导致了内存使用量的大幅上升。事后看来，[深入挖掘文档](https://elastalert.readthedocs.io/en/latest/ruletypes.html#max-query-size)，我们最好使用`use_count_query`和/或`max_query_size`。

## 级联故障

因此，确定、调查并解决了根本原因。事件结束了，对吗？请记住前面的引言，还有 85%的知识需要学习，所以让我们开始吧:

### 未触发任何警报

显然，我们正在处理警报问题，因为根本原因与 ElastAlert 有关。一些数据(目前)只在 Elasticsearch 中可用，比如日志消息(关键词的出现)或 Kubernetes 集群之外的系统。普罗米修斯还有一个警报管理器，我们还需要设置它。除了这两个来源，我们使用新的遗迹为 APM。不管来源是什么，也可能需要融合，它至少从定义警报规则的*开始。*

*分辨率:*

*   定义与资源使用相关的警报，如 CPU、内存和磁盘空间。
*   继续研究有效结合多种来源的警报策略。

### Grafana 仪表板受集群问题影响

Prometheus 和 Grafana 很容易在 Kubernetes 集群中设置(安装一些舵图，您就可以开始运行了)。然而，如果你不能到达你的集群，你就是盲人。

*分辨率:*

*   考虑将指标导出到集群之外，并将 Grafana 也移出集群。尽管在 robustperception.io 的这篇文章[中有很好的解释，但这并不是没有缺点。一个优势可能是为多个集群的仪表板提供一个单一的访问点。据我所知](https://www.robustperception.io/federation-what-is-it-good-for) [Kublr](https://kublr.com/) 使用类似的设置来监控多个集群。
*   集群外位置可以是 EC2，也可以是单独的 Kubernetes 集群。

### 没有完全受益于我们的麋鹿栈

我们正在运行一个基于 EC2 的 ELK 堆栈，它接收大量的 Cloudfront 日志。还包括来自 Kubernetes 集群的日志和指标，由 filebeat 和 metricbeat daemonsets 导出。因此，我们无法通过集群内 Grafana 访问的数据也存在于 ELK 堆栈中....但是要么没有被恰当地可视化，要么被忽略了。

总的来说，这是一个有点棘手的问题:一方面，对于集中式日志来说，弹性搜索很可能是必需的，而且它也可以做度量，所以*可能是*一站式解决方案。然而，在规模上，这是一个相当大的操作，入职可以真正受益于更多的示例仪表板(imo)。

另一方面，Prometheus 设置简单，似乎是 Kubernetes 生态系统中的默认技术，与 Grafana 的可用仪表板配合使用，非常容易进入。

*分辨率:*

*   要么可视化 ELK 中的重要指标，要么提高 Prometheus/Grafana 的可用性。
*   改进度量策略。

### ElastAlert pod 上无 CPU &内存限制

用于安装 ElastAlert [的舵图允许指定资源请求和限制](https://github.com/helm/charts/blob/d3fd3f11578ebf74749b1b2c994c51d5199b8599/stable/elastalert/values.yaml#L33)，但是这些没有默认值(并不少见)并且被我们忽略了。

为了强制配置资源限制，我们可以让[默认配置，并限制我们的名称空间](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/#create-a-limitrange-and-a-pod)的内存请求。

*分辨率:*

*   通过舵值指定资源限制。
*   将名称空间配置为对内存请求有默认值限制。

### 影响客户工作负载的运营服务

共享同一组资源的客户工作负载和监控/日志记录工具有消耗所有资源的放大效应的风险。流量增加，导致 CPU/内存压力增加，导致更多的日志/指标量，导致更多的 CPU/内存压力，等等。

我们已经计划将所有日志记录、监控和 CI/CD 工具转移到生产集群中的专用节点组。根据我们在这方面的经验，拥有专用的“工具”集群也是一种选择。

*决议(已经计划好了):*

*   将面向客户的工作负载与构建、日志记录和监控工作负载隔离开来。

### 整个团队都不知道已部署的 ElastAlert 变更

尽管新的警报通过了代码审查，但它被合并和部署的事实并不为所有人所知。更重要的是，由于它是由一个团队成员通过命令行安装的，因此没有即时的信息来源显示集群中的哪些应用程序可能已经更新。

*分辨率:*

*   通过自动化部署一切(例如 Jenkins 管道)
*   考虑一种部署新应用程序版本的 [GitOps](https://thenewstack.io/gitops-git-push-all-the-things/) 方法:使用开发人员熟知的工具，记录代码的“未来状态”和变更历史。

### 无烟测试

如果我们已经使用管道部署了 ElastAlert 更新，我们可以在部署之后添加一个“冒烟测试”步骤。这可能表示内存使用过多，或者由于 pod 超过配置的内存限制而导致 pod 重新启动。

*分辨率:*

*   通过包含冒烟测试步骤的管道进行部署。

### 运营 Kubernetes 的知识仅限于团队的一部分

我们的团队(和大多数团队一样)由在不同主题上具有不同专业水平的人组成。有些人有更多的云和 DevOps 经验，有些人是前端或 Django 专家，等等。由于 Kubernetes 是一项相当新的技术，当然对我们的团队来说，知识并不像期望的那样广泛。正如敏捷团队实践的所有技术一样:开发运维不应该局限于单个(部分)团队。幸运的是，经验丰富的团队成员可以帮助几乎没有基础架构经验的待命团队成员。

*决议(已经计划好了):*

*   确保与 Kubernetes 相关的工作(实际上通常是与云基础设施相关的)是团队 sprints 的一部分，由所有团队成员与更有经验的成员一起完成。
*   深入探讨某些主题的研讨会。

## 总结起来

显而易见，解决 ElastAlert 问题本身只是冰山一角。从这个看似简单的事件中，我们可以学到更多东西。本文中列出的大多数要点已经以这样或那样的方式引起了我们的注意，但还是强调了它们的重要性。

将这些学习转化为 scrum(或看板)项目将允许我们以一种集中的方式改进我们的平台和实践，并测量我们的进展。

作为一个团队，学习和提高需要一种允许“无可指责的事后检讨”的公司文化，而不仅仅是关注“事件的数量”或“解决问题的时间”。最后引用一句在 DevOps 会议上听到的话:

> 成功包括从失败走向失败而不丧失热情——温斯顿·丘吉尔