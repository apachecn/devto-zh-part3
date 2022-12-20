# 请不要从单片应用程序转移到单片 Kubernetes 集群？

> 原文：<https://dev.to/azure/important-points-of-kubernetes-cluster-design-4b2h>

这是我从经验中得到的建议信息。

## ***我建议您不要在一个 Kubernetes 集群上创建巨大的节点。***

根据以下文档，如果使用 Azure Kubernetes 服务(AKS)，最大节点数为 100。
[Azure Kubernetes 服务(AKS)中的配额、虚拟机大小限制和区域可用性](https://docs.microsoft.com/azure/aks/quotas-skus-regions?WT.mc_id=devto-blog-yoterada)

但是，我不建议您创建具有最大节点大小的集群。因为这等同于再次在容器环境中创建一个新的整体(Kubernetes)。

可以想象，如果您创建了它，您可能会面临以下问题。

1.  ***影响增加***
    如果您在 100 个节点上创建了 150 个微服务，并且出现了系统故障或其他性能影响，那么所有 150 个服务都可能受到影响。
    与单一应用程序(10-20 个服务)相比，单一 kubernetes 集群(150 个服务)的影响范围更大。

2.  ***增加升级时间***
    每 3-4 个月有一次 kubernetes 发布。
    AKS(Kubernetes)有一项生命周期终止政策，要求至少每 1 或 2 年更新或重新安装一次 Kubernetes 集群。
    [考试:Azure Kubernetes 服务:Kubernetes 1.9 停售通知](https://azure.microsoft.com/en-us/updates/azure-kubernetes-service-kubernetes-1-9-end-of-life-notice/?WT.mc_id=devto-blog-yoterada)
    为了非常快速地更新或创建新的 Kubernetes 集群，节点大小的数量非常重要。例如，如果您创建了 100 个集群节点，那么创建 30 个节点所需的时间将会更长。

3.  ***难以维护***
    我会推荐你创建非常简单的结构和配置(简单是最好的！！).
    如果你创建了简单的结构和配置，你很容易创建相同的环境。然而，如果您创建了复杂的结构，有时可能很难创建相同的环境，即使您以代码的形式创建了基础设施。
    我推荐你要随时容易创造或破坏环境。

***最后***
我建议您创建 Kubernetes 集群，用于 20-30 个节点这样的小型服务级别。然而，事实上，规模取决于您的系统。例如，您需要创建一组相关的微服务，并将其部署到集群中。