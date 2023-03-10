# 学习 Kubernetes，第四部分，自动缩放

> 原文：<https://dev.to/azure/kubernetes-from-the-beginning-part-iv-autoscaling-54l6>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> 第四部分旨在向您展示如何利用自动缩放作为您的缩放策略。我们已经向您展示了使用*期望状态*进行缩放，但是这可能更加强大

*   [第一部分——从第一部分开始，基础知识、部署和 Minikube](https://dev.to/azure/kubernetes-from-the-beginning-part-i-4ifd) 在这一部分中，我们将介绍 Kubernetes 的原因、一些历史和一些基本概念，如部署、节点、单元。
*   [第二部分-介绍服务和标签](https://dev.to/azure/kubernetes-part-ii-revisiting-pods-and-nodes-and-introducing-services-and-labeling-5fi7)在这一部分，我们将加深对 pod 和节点的了解。我们还引入了服务和标签，使用标签来查询我们的工件。
*   [第三部分-缩放](https://dev.to/azure/kubernetes-part-iii-scaling-1mmi)
    在这一部分中，我们来看看使用*期望的*状态的缩放。我们指定我们想要多少个豆荚，让 Kubernetes 来做繁重的工作，确保您的豆荚扩大到所需的数量，同时通过使用一种叫做自我修复的东西来维护它。

*   第四部分-自动缩放**我们到了**

在本文中，我们将涵盖以下内容:

*   **为什么自动缩放**，我们将讨论依赖*自动缩放*比静态定义它更有意义的不同场景，就像我们使用*期望状态*一样
*   **如何**，我们来谈谈水平自动扩展这一概念/功能，它允许我们以灵活的方式进行扩展。
*   **实验室-让我们扩展**，我们将看看如何在`kubectl`中实际设置它，并模拟大量的传入请求。然后，我们将检查结果，并看到 Kubernetes 按照我们的想法行事

## 资源

*   [水平 Pod 自动缩放](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)这详细描述了一切如何工作。在流程和 API 级别上
*   如果你想试用 AKS，Azure Kubernetes 服务，你需要一个免费的 Azure 账户
*   了解 Kubernetes 的最佳资源之一是谷歌的这个官方网站。
*   [Kubernetes 概述](https://azure.microsoft.com/en-gb/topic/kubernetes/?wt.mc_id=devto-blog-chnoring)Kubernetes、其所有部件及其工作原理的概述
*   [云中的 Kubernetes](https://azure.microsoft.com/en-gb/services/kubernetes-service/?wt.mc_id=devto-blog-chnoring)你是否觉得自己已经了解了 Kubernetes 的一切，只是想学习如何使用托管服务？那么这个链接是给你的
*   [AK 上的文件，azure kublers 服务](https://docs.microsoft.com/en-gb/azure/aks/?wt.mc_id=devto-blog-chnoring)azure kublers 服务，一种托管 kublers 服务
*   [AKS 最佳实践](https://docs.microsoft.com/en-us/azure/aks/best-practices?wt.mc_id=devto-blog-chnoring)您已经了解 AKS，并想学习如何更好地使用它？

## 为什么

所以在我们的上一部分，我们讨论了*期望状态*。这是一个很好的策略，直到一些不可预见的事情发生，突然你有一个巨大的流量涌入。这种情况很可能发生在大型销售活动周围的电子商务或售票商等企业，当你发布热门活动的门票时。

像这样的事件是一种反常现象，迫使你迅速扩大规模。然而，硬币的另一面是，在某些时候你需要缩小规模，或者你突然有了你可能需要支付的过剩产能。你真正想要的是以一种*弹性*的方式进行扩展，以便在你需要的时候扩大规模，在流量较少的时候缩小规模。

## 如何

水平自动缩放，是什么意思？

这是 Kubernetes 中的一个概念，可以扩展我们需要的豆荚数量。它可以在`replication controller`、`deployment`或`replica set`上这样做。它通常查看 CPU 利用率，但是可以通过使用一个叫做`custom metrics support`的东西来查看其他东西，所以它是可定制的。

它由两部分 a `resource`和 a `controller`组成。`controller`检查利用率，或者您决定的任何指标，以确保副本的数量符合您的规范。如果需要的话，它会旋转更多的豆荚或者移除它们。默认是每`15`秒检查一次，但是你可以通过查看一个名为`--horizontal-pod-autoscaler-sync-period`的标志来改变。

决定副本数量的底层算法如下:

```
desiredReplicas = ceil[currentReplicas * ( currentMetricValue / desiredMetricValue )] 
```

Enter fullscreen mode Exit fullscreen mode

## 实验室规模

好了，我们需要做的第一件事是扩展我们的部署，使用除了*期望状态*之外的东西。

当我们进行自动缩放时，我们需要指定两件事:

*   **最小/最大**，我们根据我们想要多少个 pod 来定义最小和最大值
*   **CPU** ，在这个版本中我们设置了一定的 CPU 利用率百分比。当它超过这个值时，它会根据需要向外扩展。把这一条想象成一个`IF`子句，如果 CPU 值大于阈值，尝试伸缩

### 设置

在尝试扩展实验之前，我们需要确保启用了正确的附加组件。您可以通过键入:
轻松查看您启用了哪些加载项

```
minikube addons list 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/9f9efec74dc38381bbfc13ddbf2ca49b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SpvLIg9f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dyxueq0x7j2q0ybfgxq4.png)

如果看起来像上面那样，我们都很好。我为什么这么说？为了能够自动伸缩，我们需要的是启用`heapster`和`metrics-server`附加组件。

Heapster 支持容器集群监控和性能分析。

度量服务器通过资源度量 API 提供度量。水平 Pod 自动缩放器使用此 API 来收集指标

我们可以通过以下命令轻松启用它们(我们需要自动缩放来显示正确的数据):

```
minikube addons enable heapster 
```

Enter fullscreen mode Exit fullscreen mode

和

```
minikube addons enable metrics-server 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要做一件事，也就是对`enable` *定制度量*，我们通过用这样一个标志开始`minikube`来做这件事，比如:

```
minikube start --extra-config kubelet.EnableCustomMetrics=true 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们可以走了。

### 运行实验

我们需要做以下事情来运行我们的实验

*   **创建**一个部署
*   **应用**自动缩放
*   **用传入的请求轰炸**部署
*   **观察**自动缩放是如何变化的

#### 创建部署

```
kubectl run php-apache --image=k8s.gcr.io/hpa-example --requests=cpu=200m --expose --port=80 
```

Enter fullscreen mode Exit fullscreen mode

上面我们正在创建一个部署`php-apache`，并在端口`80`上将其公开为一个服务。我们可以看到，我们正在使用图像`k8s.gcr.io/hpa-example`

它应该告诉我们以下内容:

```
service/php-apache created
deployment.apps/php-apache created 
```

Enter fullscreen mode Exit fullscreen mode

#### 自动缩放

接下来我们将使用命令`autoscale`。我们会这样用:

```
kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10 
```

Enter fullscreen mode Exit fullscreen mode

它应该是这样的:

```
horizontalpodautoscaler.autoscaling/php-apache autoscaled 
```

Enter fullscreen mode Exit fullscreen mode

上面我们在部署`php-apache`上应用了自动缩放，正如您所见，我们应用了基于`min-max`和`cpu`的自动缩放，这意味着我们给出了自动缩放应该如何发生的规则:

> 如果 CPU 负载为`>= 50%`创建一个新的 Pod，但最多只能有 10 个 Pod。如果负荷低，逐渐回到一个吊舱

#### 请求连珠炮

下一步是针对我们的部署发送大量请求，并查看我们的自动伸缩功能是否正常工作。那么我们该怎么做呢？

首先，让我们通过键入
来检查我们的*水平吊舱自动定标器*或简称为`hpa`的当前状态

```
kubectl get hpa 
```

Enter fullscreen mode Exit fullscreen mode

这应该会给我们这样的结果:

[![](img/c028b5c44723a2b5c08fb58cb764eacd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LJASy55q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0pxx5qqkkcq2buz6wxfd.png)

以上向我们展示了两条信息。第一个是`TARGETS`列，显示我们的 CPU 利用率`actual usage/trigger value`。下一个有趣的地方是列`REPLICAS`，它向我们显示了副本的数量，目前是`1`。

我们的下一个技巧是打开一个单独的终端标签。我们需要做的是设置一些东西，这样我们就可以发送大量的请求。

接下来我们使用这个命令创建一个容器:

```
kubectl run -i --tty load-generator --image=busybox /bin/sh 
```

Enter fullscreen mode Exit fullscreen mode

这应该会把我们带到容器中的一个提示符处。接下来是:

```
while true; do wget -q -O- http://php-apache.default.svc.cluster.local; done 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令应该会产生类似这样的结果。

[![](img/74317cd62638cd00234ebe6873772c3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X7R31G0o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/55so6diaa2y7h3jai792.png)

这将一直持续下去，直到你点击`CTRL+C`，但是现在就让它去吧。

这在`while true`循环中抛出了大量请求。

> 我认为循环是不好的？

是的，但是我们只运行它一分钟，以便自动缩放可以发生。可以，CPU 会响很多但是不用担心:)

[![](img/fe5eaf01c9a45055d074ebad14395742.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MyzD71he--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5bcbz91175tjpj461hh8.gif)

让这种情况持续一分钟左右，然后在第一个终端选项卡(不是运行请求的那个)中输入以下命令，就像这样:

```
kubectl get hpa 
```

Enter fullscreen mode Exit fullscreen mode

它现在应该显示如下内容:

[![](img/44d3b60136382c2b054247994a7af391.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XxyTf9hi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/szvti1xjqkgp10xa5e73.png)

正如你从上面看到的，`TARGETS`栏看起来不同，现在显示为`339%/50%`,这意味着 CPU 上的当前负载，`REPLICAS`为`7`,这意味着它已经从 1 个副本增加到 7 个副本。如你所见，我们一直在大力轰炸它。

现在转到第二个终端，点击`CTRL+C`，否则将会出现如下情况:

[![](img/1d7dc3d5c260199f131b878d81675611.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t88Wi6P0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/66wm4drplfxlcuspf90k.gif)

Kubernetes 实际上需要几分钟的时间来冷却，使数值恢复正常。首先看一下 pod 的情况，我们看到了以下内容:

```
kubectl get pods 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/87780b08343d0c3dbe8972904ad4747f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--43nWrg-v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4ikq562c45z7k65su7j9.png)

如您所见，我们有 7 个 pod 仍在运行，但让我们等一两分钟，它应该是这样的:

[![](img/ab5aeab25bf2752addddae15e574857c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LKMLmVEA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/39x2p1dg3f2i3nwg9jl4.png)

好了，现在我们恢复正常了。

## 总结

现在我们在这篇文章中做了一些很棒的东西。我们设法设置了自动缩放，用请求轰炸它，希望不会烧坏我们的 CPU)

同时，我们还设法学习了一些新的 Kubernetes 命令，并看到了*自动缩放*在工作中根据我们的规范为我们提供了新的 pod。