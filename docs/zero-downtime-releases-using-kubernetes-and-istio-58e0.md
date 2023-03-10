# 使用 Kubernetes 和 Istio 实现零停机发布

> 原文：<https://dev.to/peterj/zero-downtime-releases-using-kubernetes-and-istio-58e0>

> 这是即将推出的 **Learn Istio 电子书**的节选——免费获得**[Learn Istio 服务网电子书](https://learnistio.com)的预览，或在此[预订](https://gum.co/learnistio)**

 **[![Learn Istio Service Mesh Book](img/ac437de54d5e970780de65a1f18544b7.png)](https://gum.co/learnistio)

零停机发布背后的想法是发布服务的新版本，而不影响任何用户，即用户甚至不知道服务的新版本何时发布。一个实际的例子是，如果你有一个正在运行的网站，你如何在不关闭网站的情况下发布一个新版本？对于服务，这意味着您可以在发布新服务的同时连续请求该服务，而呼叫者永远不会得到可怕的 503 服务不可用响应。

在本文中，我将解释如何进行零停机部署的两种方法——一种仅使用 Kubernetes，另一种使用 Istio service mesh。

## 使用 Kubernetes(滚动更新)

作为零停机部署的第一个选项，我们将使用“普通”Kubernetes，不涉及任何 Istio。我们将使用一个简单的节点。显示“Hello World！”的 Js web 应用程序消息。

您可以使用`kubectl apply -f helloworld.yaml`部署上面的 YAML。这创建了一个名为 helloworld 的 Kubernetes 服务和一个 Kubernetes 部署，其中有三个副本和一个名为`learnistio/helloworld:1.0.0`的映像——这是您可以在 Kubernetes 中运行的最简单的 YAML。

要访问该网站，您可以从 Kubernetes CLI 使用 port-forward 命令。这个命令在集群内部运行的服务和您的本地机器之间打开一个隧道:

```
kubectl port-forward service/helloworld 3000 3000 
```

Enter fullscreen mode Exit fullscreen mode

在你选择的浏览器中打开`http://localhost:3000`,你会看到一个难看的页面，如下图所示:

[![Ugly looking site](img/432ad46a10cea5889a969b02ca47335a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nwwoNPhz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AXFCou6izw9OUSVVzGBeQMQ.png)

现在，我们想用一个更新的版本来更新这个网站。假设您构建并创建了一个名为`learnistio/helloworld:2.0.0`的映像，我们需要用这个新映像更新部署。不仅如此，我们需要以这样一种方式更新部署，它不会影响任何可能试图访问网站的人。

如前所述，Kubernetes 有一个使用 RollingUpdate(在单个 Kubernetes 部署上设置)策略进行零停机部署的概念，这也是每个部署的默认设置。第二种策略被称为 Recreate，顾名思义，它杀死所有现有的 pod，然后创建新的 pod。

默认滚动更新策略以滚动方式更新窗格。要控制这个过程，可以使用两个设置:maxUnavailable 和 maxSurge。使用第一个设置，您可以指定在更新期间有多少个单元(以单元总数或百分比表示)不可用(默认值为 25%)。因此，如果您有四个副本在运行，并且您进行滚动升级，旧部署将缩减到 75%,同时，新单元将上线。使用 maxSurge 选项，您可以控制可以创建的最大 pod 数量，并且超过所需的 pod 数量。如果我们以具有四个副本的前一示例为例，并将该值设置为 50%，则滚动更新过程将按比例增加新部署，使得新旧单元的总数不会超过所需单元的 150%(即，六个单元)。

为了用新的映像版本更新现有的部署，我们将在 Kubernetes CLI 中使用 set 命令。让我们运行命令来执行滚动更新:

```
kubectl set image deployment helloworld web=learnistio/helloworld:2.0.0 — record 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将名为 web 的容器的图像设置为`learnistio/helloworld:2.0.0`，并在部署注释中记录更改(因此如果需要，您可以回滚更改)。

让我们运行另一个命令来显示滚动更新的进度:

```
$ kubectl rollout status deployment helloworld
Waiting for deployment “helloworld” rollout to finish: 2 out of 3 new replicas have been updated…
helloworld successfully rolled out 
```

Enter fullscreen mode Exit fullscreen mode

在这段时间里，你可以访问网站，你要么得到主页的 v1，要么得到 v2。但是，一旦命令完成，您应该只能从主页的 v2 版本获得响应。

## 使用 Istio

你可能想知道为什么我要用 Istio 来做滚动更新 Kubernetes 选项要简单得多。这是真的，如果零停机版本是您唯一打算使用它的东西，那么您可能不应该使用 Istio。你可以用 Istio 实现同样的行为；但是，您可以更好地控制流量如何以及何时被路由到特定版本。

要将 Istio 用于停机发布，您需要记住以下几点:

### 每个版本一个部署

服务的每个部署都需要进行版本控制——您需要一个名为 version: v1(或类似的名称)的标签，并为部署命名，这样就可以清楚地知道它代表哪个版本(例如 helloworld-v1)。通常，在每个部署中至少有这两个标签:

```
labels:
  app: helloworld
  version: v1 
```

Enter fullscreen mode Exit fullscreen mode

如果有意义的话，你也可以包含一堆其他的标签，但是你应该有一个标签用来识别你的组件和它的版本。

### 通用库伯服务

Kubernetes 服务应该是通用的——不需要选择器中的版本，只需要应用程序/组件名称就足够了

### 保持目标规则最新

从包含您当前正在运行的版本的目标规则开始，并确保它保持同步。没有必要以包含一堆未使用或过时的子集的目标规则结束。

### 定义一个后备版本

如果您使用匹配 and 条件，请始终在虚拟服务中定义服务的“回退”版本。如果你不这样做，任何不符合条件的请求都将在数字天堂结束，不会得到服务。

记住这些指导方针，这里有一个关于如何使用 Istio 进行零停机发布的粗略过程。我们从 Kubernetes 部署 helloworld-v1 开始，这是一个具有一个子集(v1)的目的地规则和一个将所有流量路由到 v1 子集的虚拟服务。以下是 YAML 的目的地规则:**