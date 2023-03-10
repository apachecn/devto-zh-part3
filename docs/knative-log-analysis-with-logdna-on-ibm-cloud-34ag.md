# IBM Cloud 上使用 LogDNA 的 Knative 日志分析

> 原文：<https://dev.to/vidyasagarmsc/knative-log-analysis-with-logdna-on-ibm-cloud-34ag>

在这篇文章中，您将了解如何使用 IBM Log Analysis with LogDNA Service 为 IBM Cloud Kubernetes Service 中发布的名为 **Knative-node-app** 的应用程序配置集群级日志记录。参考本文 [*用 Istio 安装 Knative，在 IBM Cloud 上部署 app*](https://dev.to/vidyasagarmsc/install-knative-with-istio-on-ibm-cloud-2lde-temp-slug-9746339)*，*设置 nodejs app。

> IBM Log Analysis with LogDNA 为管理员、DevOps 团队和开发人员提供了过滤、搜索和跟踪日志数据、定义警报以及设计定制视图来监控应用程序和系统日志的高级功能。

从使用 IBM Cloud Kubernetes 服务配置集群的那一刻起，您就希望知道集群内部发生了什么。您需要访问日志来解决问题和预防问题。在任何时候，您都希望能够访问不同类型的日志，如工作日志、pod 日志、应用程序日志或网络日志。此外，您可以在 Kubernetes 集群中监控不同来源的日志数据。因此，管理和访问这些来源的日志记录的能力非常重要。成功管理和监控日志取决于您如何为 Kubernetes 平台配置日志功能。

### 使用 LogDNA 实例预配 IBM 日志分析，并配置 LogDNA 代理

> 在提供实例之前，检查 [**先决条件**](https://cloud.ibm.com/docs/services/Log-Analysis-with-LogDNA/tutorials/kube.html#prereqs)

一旦在 Knative Kubernetes 集群上运行了应用程序，就可以使用 LogDNA 服务提供一个 [IBM 日志分析实例](https://cloud.ibm.com/observe/logging/create)

*   选择一个地区/位置(美国南部)
*   选择一个资源组
*   选择一个合适的计划(本文推荐 **7 天日志搜索**
*   点击**创建**。通过这一步，您可以配置一个集中式日志管理系统，其中的日志数据托管在 IBM Cloud 上。

### 配置您的 Kubernetes 集群，将日志发送到您的 LogDNA 实例

*   一旦在[https://cloud.ibm.com/observe/logging](https://cloud.ibm.com/observe/logging)下提供了服务，点击**编辑日志源**，然后点击 **Kubernetes。**

<figure>[![](img/82d17d75f7dcf4e3a4f1c5783d22b6f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HxMDsLd4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADbOwzmmiZTM6Pwgb2oQ7Cw.png) 

<figcaption>在 IBM 云上的可观测性</figcaption>

</figure>

*   您应该看到在集群的每个节点上自动安装 logdna-agent pod 的两个命令。

```
kubectl create secret generic logdna-agent-key --from-literal=logdna-agent-key= **<INGESTION_KEY>**

kubectl create -f https://repo.logdna.com/ibm/prod/logdna-agent-ds-us-south.yaml 
```

*   这将在集群中的每个 worker(节点)上配置 LogDNA 代理。

<figure>[![](img/19203f44b5fe4cacc75303fa7de3639e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QWC_DIYZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/862/1%2ASQb88xplfaJgL2v9ZpG8_w.png)

<figcaption>K8s 集群上的 LogDNA 代理使用 LogDNA</figcaption>

</figure>

向 IBM 日志分析发送日志

*   要确认在集群中的每个工作节点上成功创建 LogDNA 代理，请运行以下命令

```
kubectl get pods 
```

<figure>[![](img/44bdf94c2c231aa515a081388d0b11a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NfQaadNA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOIgYdoXaqiiMlgSVcWxJiQ.png)

<figcaption>kubectl IBM kui 工具上的命令</figcaption>

</figure>

### 启动 LogDNA 仪表板并查看日志

让我们检查和搜索日志，并通过点击**查看日志 DNA 来创建一些图表。**在一个新的标签**，**下的**一切**，你应该开始实时查看所有日志。

[![](img/69e5f68454acb55fef34441817350b15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aaR12rd5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ag--X5ryHt_jwygHhtHc88A.gif)

为此，我们需要生成一些流量/负载，有许多开源库/工具可用。在这篇文章中，我们将使用[贝吉塔](https://github.com/tsenart/vegeta)，一个 HTTP 负载测试工具和库。也可以用[嘿](https://github.com/rakyll/hey)。

在生成负载之前，让我们快速检查一下在部署应用程序时是否正确设置了 IP_ADDRESS 和 HOST_URL。

```
echo $IP_ADDRESS

echo $HOST_URL 
```

安装[贝吉塔](https://github.com/tsenart/vegeta/releases)后，运行下面的命令

```
echo “GET [http://${IP_ADDRESS](http://%24%7BIP_ADDRESS)}" | vegeta attack -duration=300s -header “Host: ${HOST_URL}” | tee results.bin | vegeta report
 vegeta report -type=json results.bin > metrics.json
 cat results.bin | vegeta plot > plot.html
 cat results.bin | vegeta report -type=”hist[0,100ms,200ms,300ms]” 
```

你可以打开你运行过上面命令的文件夹中的*plot.html*文件来查看贝吉塔图

[![](img/73b39ab40fc0d1ee4b85cb2ed002ce64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ObC_6ihO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhJuS-5X0DGHwTc1mXvVj5Q.png)

在 LogDNA 仪表板上，您可以在搜索框中搜索/过滤各种字段，还可以突出显示您感兴趣的术语。您也可以通过点击未保存的视图将此保存为一个**视图**作为书签。

<figure>[![](img/4c87f96698c0c0083e4252c8cee98702.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DZ0LZp6j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AnPRZ7VXBLnUVdkZWoyB1zw.png) 

<figcaption>LogDNA 仪表盘</figcaption>

</figure>

> **将日志归档到 IBM 云对象存储**，参考[此链接](https://cloud.ibm.com/docs/services/Log-Analysis-with-LogDNA/archive.html#archiving)

你可以过滤标签，来源，应用程序和级别(信息，错误等)的日志。,)

<figure>[![](img/780c702b099a440b4754a2c63ac77ca5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0ExLNRDX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AdB2p5dqcFcC_-Pekh6tdEQ.png) 

<figcaption>过滤日志</figcaption>

</figure>

### 图板和图表

电路板是一个命名的图形集合，类似于[视图](https://docs.logdna.com/docs/views)。所有电路板都位于电路板列表中，并且可能包含任意数量的图形。对纸板所做的更改会自动保存。

要创建新的板，请在左侧窗格中>单击控制面板下方的图标，然后单击+NEW BOARD。让我们把这个叫做*棋盘*。

*   点击+添加图表。
*   选择 *destinationApp* 作为要绘制的字段。
*   选择 knative-node-app-00001 作为可选值，然后单击添加图表。

<figure>[![](img/3d5d11b9f9c19f1c8c9ab738882e4bf5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7_xMXx2_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AsGgdPcleSqBI5FJthwdTKA.png) 

<figcaption>新图形创建</figcaption>

</figure>

将创建一个新图表，其中包含 24 小时内的 destinationApp 图(可以随意更改时间范围)。您也可以通过点击图形下方的**+添加图**来添加新的图。

<figure>[![](img/a8e555a3bfbb7a168a58dc902c8e6bd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PkPheNRl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ABVIPdNzEmoTUaTGaoI6hAQ.png) 

<figcaption>多图图板</figcaption>

</figure>

Knative 以其自动缩放功能而闻名，让我们通过添加如上图所示的新图表来了解自动缩放器如何处理传入的流量/请求。

> knative Serving auto scaler——另一个运行单个 Pod 的 k8s 部署，它监视运行用户代码的 Pod 上的请求负载。它增加和减少运行用户代码的部署规模，以补偿更高或更低的流量负载。

此外，作为对您创建的图形的扩展，您可以看到各种字段的细分(直方图),如节点、目的地名称等。，通过点击图形下方的箭头>+Add。您可以创建多个分解结构。

<figure>[![](img/07a141377d94e0a3314048fa2967afd2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UGVFu0EP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2An9lLYwUHjebr6xfwpSDThA.png) 

<figcaption>职工节点信息</figcaption>

</figure>

我们刚刚触及了冰山一角，还有许多领域/指标需要关注。探索具有不同负载和过滤器的工具。

***疑问或担忧？发布问题或在 Twitter @***[***vidyasargmsc***](https://twitter.com/VidyasagarMSC)

### 进一步阅读

*   使用[警报](https://console.bluemix.net/docs/services/Log-Analysis-with-LogDNA/alerts.html#alerts)
*   [将日志](https://console.bluemix.net/docs/services/Log-Analysis-with-LogDNA/export.html#export)导出到本地文件
*   使用 Kaniko — [在 K8s 集群中构建一个容器映像，并将其推送到 IBM Cloud Container Registry](https://medium.com/@VidyasagarMSC/build-a-container-image-inside-a-k8s-cluster-and-push-it-to-ibm-cloud-container-registry-abac9b1e5246)
*   [IBM、Google 催生无服务器云项目](http://www.eweek.com/development/ibm-google-give-birth-to-knative-serverless-cloud-project)
*   [知识文档](https://github.com/knative/docs)

* * *