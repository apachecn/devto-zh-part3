# 跨多个 Kubernetes 集群运行 Argo 工作流

> 原文：<https://dev.to/admiralty/running-argo-workflows-across-multiple-kubernetes-clusters-4jap>

[最初发表于金钟的博客。](https://admiralty.io/blog/running-argo-workflows-across-multiple-kubernetes-clusters/)

我们最近[开源了多集群调度器](https://github.com/admiraltyio/multicluster-scheduler)，这是一个由 Kubernetes 控制器组成的系统，可以智能地跨集群调度工作负载。在这篇博文中，我们将使用它和 [Argo](https://argoproj.github.io/argo) 一起运行多集群工作流(管道、Dag、ETL ),更好地利用资源和/或组合来自不同区域或云的数据。

**大多数使用 Kubernetes 的企业管理多个集群**。[由于各种原因](https://dev.to/blog/introducing-multicluster-controller/)，每个团队、区域、环境或其组合可能有一个或几个集群。您的集群可能由不同的云提供商托管—在多云基础架构中—和/或在本地—在混合基础架构中。然而，隔离的好处是以[和其他](https://dev.to/blog/introducing-multicluster-controller/)、**降低[装箱](https://en.wikipedia.org/wiki/Bin_packing_problem)效率和数据碎片**为代价的。让我们探讨两种情况。

**场景 A:你需要运行一个大型的并行工作流**，例如机器学习培训管道，这需要比你团队集群中可用的资源更多的资源。您可以横向扩展以提高速度，或者限制并行性以节省资金。同时，其他团队集群中的可用资源处于闲置状态。Multicluster-scheduler 允许您从自己的集群中，通过单个 pod 或 pod 模板注释，选择将 pod 委派给其他有资源可用的集群。

**场景 B:您需要运行一个结合来自多个云或区域的数据的工作流**。要么更有效，要么需要在离数据源更近的地方运行一些步骤。为了优化吞吐量或节省数据出口费用，您可能希望在加载离您更近的结果之前压缩或聚合数据。或者为了遵守隐私法规并最大限度地减少您的攻击面，您可能希望尽可能在上游匿名化数据。您可以部署远程服务或功能，并从您的工作流中调用它们，但这将是复杂的。Multicluster-scheduler 允许您简单地指定一个 pod 应该在哪个集群中运行，同样，使用单个 pod 或 pod 模板注释。

## 一个多集群吊舱的旅程

以下是多集群 pod 之旅的快速总结。

1.  当用`multicluster.admiralty.io/elect=""`注释创建一个 pod 时，多集群调度器代理的[变异 pod 准入 webhook](https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/) 用一个等待被杀死的虚拟 [busybox](https://hub.docker.com/_/busybox) 替换 pod 的容器。原始的规范被保存为另一个注释。
2.  我们将产生的 pod 称为**代理 pod** 。代理然后将代理 pod 的**观察**发送到调度器的集群，该集群可以是同一个集群。代理还监视其他 pod、节点和节点池，并将对它们的观察发送到调度程序的集群，以指导其决策。
3.  调度程序在自己的集群中创建一个**代表 pod 决策**。如果原始 pod 用`multicluster.admiralty.io/clustername=foo`标注，则代表 pod 决策以集群“foo”为目标。否则，基于当前的观察，调度器将目标放在能够容纳我们的 pod 的最多副本的集群上。更高级的日程安排选项正在的[项目中。](https://github.com/admiraltyio/multicluster-scheduler#roadmap)
4.  目标集群中的代理看到决策并创建一个**委托 pod** 。代理 pod 与原始 pod 具有相同的规格。
5.  委派窗格的观察结果被发送回调度程序的集群。当代理 pod 被注释时，相同的注释被反馈给代理 pod(例如，因此 Argo 可以读取步骤输出)，并且当它成功或终止时，信号被发送到代理 pod 的容器，以便它成功或也终止。

更多详情，请查看[自述文件](https://github.com/admiraltyio/multicluster-scheduler)。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[多集群调度器](https://github.com/admiraltyio/multicluster-scheduler)

### Kubernetes 控制器系统，可跨集群智能调度工作负载。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 多集群调度程序

Multicluster-scheduler 是一个 Kubernetes 控制器系统，它可以跨集群智能地调度工作负载。它使用简单，并且易于与其他工具集成。

1.  在要联合的每个集群中安装 multicluster-scheduler。将集群配置为源和/或目标，以构建集中式或分散式拓扑。
2.  用`multicluster.admiralty.io/elect=""`注释任何源集群中的任何 pod 或 pod 模板(例如，部署、作业或 [Argo](https://argoproj.github.io/projects/argo) 工作流等)。
3.  Multicluster-scheduler 将选出的 pod 变异为在代表目标集群的 [virtual-kubelet](https://virtual-kubelet.io/) 节点上调度的*代理 pod*，并在远程集群中创建*代理 pod*(实际运行容器)。
4.  Pod 依赖项(目前仅配置映射和机密)“跟随”代理 pod，即根据需要将它们复制到目标集群。
5.  反馈循环更新代理窗格的状态和注释，以反映代理窗格的状态和注释。
6.  以代理 pod 为目标的服务被重新路由到它们的…

</article>

[View on GitHub](https://github.com/admiraltyio/multicluster-scheduler)

## 演示

让我们看看实际情况。创建两个集群，比如用 [Minikube](https://kubernetes.io/docs/setup/minikube/) 或者你[最喜欢的](https://cloud.google.com/kubernetes-engine/docs/how-to/creating-a-cluster) [云](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough) [提供者](https://docs.aws.amazon.com/eks/latest/userguide/create-cluster.html)。在这篇博文中，我们将假设它们在 kubeconfig 中的关联上下文是“cluster1”和“cluster2”，但是我们将使用变量，因此您可以使用自己的:

```
CLUSTER1=cluster1 # change me
CLUSTER2=cluster2 # change me 
```

现在，按照[安装指南](https://github.com/admiraltyio/multicluster-scheduler#installation)，在集群 1 中安装调度器，在两个集群中安装代理。

我们还需要阿尔戈在任何一个集群。在本指南中，我们将使用 cluster1，但是可以随意更改变量:

```
ARGO_CLUSTER=$CLUSTER1 # change me
NON_ARGO_CLUSTER=$CLUSTER2 # change me 
```

安装 Argo 控制器和 UI([Argo 入门指南](https://argoproj.github.io/docs/argo/demo.html)的步骤 2):

```
kubectl --context $ARGO_CLUSTER create ns argo
kubectl --context $ARGO_CLUSTER apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/v2.2.1/manifests/install.yaml 
```

我们并不热衷于给予 Argo pods 管理员权限，正如《Argo 入门指南》第 3 步所建议的那样，所以我们将使用一个最小服务帐户来代替。因为 pods 将在两个集群中运行，所以我们在两个集群中都需要这个服务帐户:

```
ARGO_POD_RBAC=https://raw.githubusercontent.com/admiraltyio/multicluster-scheduler/master/config/samples/argo-workflows/_service-account.yaml
kubectl --context $ARGO_CLUSTER apply -f $ARGO_POD_RBAC
kubectl --context $NON_ARGO_CLUSTER apply -f $ARGO_POD_RBAC 
```

让我们也在本地安装 Argo CLI 尽管它是可选的——以便很好地提交和跟踪工作流:

```
# On Mac:
brew install argoproj/tap/argo
# On Linux:
curl -sSL -o /usr/local/bin/argo https://github.com/argoproj/argo/releases/download/v2.2.1/argo-linux-amd64
chmod +x /usr/local/bin/argo
# On Windows:
curl -sSL -o argo https://github.com/argoproj/argo/releases/download/v2.2.1/argo-windows-amd64 # and add to your PATH 
```

现在，您可以通过向 pod 模板添加`multicluster.admiralty.io`注释，将任何 Argo 工作流转换为多集群工作流。另外，如果您希望调度程序决定在哪里运行您的 pod，不要忘记指定资源请求。

### 场景 A:优化大型并行工作流

默认的 GKE 集群有三个节点，每个节点有 1 个 vCPU 和 3.75GB 内存，其中 940m vCPU 和 2.58GiB 内存是可分配的。系统单元以及多集群调度器和 Argo 已经请求集群 1 中的 1840m vCPU 和集群 2 中的 1740m vCPU。因此，集群 1 有 980m vCPU 可用，集群 2 有 1080m。我们不需要为这个实验花费额外的钱:我们将通过 10 个并行步骤建模一个“大型并行工作流”,每个步骤需要 200m vCPU(包括 Argo sidecar 的 100m)。

首先，让我们运行下面的单集群工作流(也可以在[多集群调度器示例目录](https://github.com/admiraltyio/multicluster-scheduler/tree/master/config/samples/argo-workflows)中找到):

```
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: singlecluster-parallel-
spec:
  entrypoint: singlecluster-parallel
  templates:
  - name: singlecluster-parallel
    steps:
    - - name: sleep
        template: sleep
        withItems: [0, 1, 2, 3, 4, 5, 6, 7, 9, 10]
  - name: sleep
    container:
      image: busybox
      command: [sleep, 10]
      resources:
        requests:
          cpu: 100m # Note: Argo sidecar adds another 100m 
```

提交:

```
argo --context $ARGO_CLUSTER submit --serviceaccount argo-workflow --watch https://raw.githubusercontent.com/admiraltyio/multicluster-scheduler/master/config/samples/argo-workflows/blog-scenario-a-singlecluster.yaml 
```

这里是最终状态:

```
Duration:            1 minute 16 seconds

STEP                             PODNAME                                  DURATION  MESSAGE
 ✔ singlecluster-parallel-6rtkc
 └-·-✔ sleep(0:0)                singlecluster-parallel-6rtkc-839758060   11s
   ├-✔ sleep(1:1)                singlecluster-parallel-6rtkc-1823198064  12s
   ├-✔ sleep(2:2)                singlecluster-parallel-6rtkc-4064072188  11s
   ├-✔ sleep(3:3)                singlecluster-parallel-6rtkc-2040401880  27s
   ├-✔ sleep(4:4)                singlecluster-parallel-6rtkc-3078784476  27s
   ├-✔ sleep(5:5)                singlecluster-parallel-6rtkc-3529283624  27s
   ├-✔ sleep(6:6)                singlecluster-parallel-6rtkc-3081898924  43s
   ├-✔ sleep(7:7)                singlecluster-parallel-6rtkc-2914639584  43s
   ├-✔ sleep(8:9)                singlecluster-parallel-6rtkc-3024028329  43s
   └-✔ sleep(9:10)               singlecluster-parallel-6rtkc-3224503614  1m 
```

仅在集群 1 上运行工作流就花费了 1 分 16 秒。我们可以看到，cluster1 只能在四个波中同时运行三个步骤，这不太理想，但也在意料之中，因为可用的 940m vCPU 位于三个“箱”中。

让我们用`multicluster.admiralty.io/elect=""`注释工作流的 pod 模板，使它在两个集群上运行:

```
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: multicluster-parallel-
spec:
  entrypoint: multicluster-parallel
  templates:
  - name: multicluster-parallel
    steps:
    - - name: sleep
        template: sleep
        withItems: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
  - name: sleep
    container:
      image: busybox
      command: [sleep, 10]
      resources:
        requests:
          cpu: 100m # Note: Argo sidecar adds another 100m
    metadata:
      annotations:
        multicluster.admiralty.io/elect: "" 
```

提交:

```
argo --context $ARGO_CLUSTER submit --serviceaccount argo-workflow --watch https://raw.githubusercontent.com/admiraltyio/multicluster-scheduler/master/config/samples/argo-workflows/blog-scenario-a-multicluster.yaml 
```

这里是最终状态:

```
Duration:            31 seconds

STEP                            PODNAME                                 DURATION  MESSAGE
 ✔ multicluster-parallel-lmw2d
 └-·-✔ sleep(0:0)               multicluster-parallel-lmw2d-1353848687  12s
   ├-✔ sleep(1:1)               multicluster-parallel-lmw2d-714502387   14s
   ├-✔ sleep(2:2)               multicluster-parallel-lmw2d-894725111   14s
   ├-✔ sleep(3:3)               multicluster-parallel-lmw2d-711387939   13s
   ├-✔ sleep(4:4)               multicluster-parallel-lmw2d-479610983   14s
   ├-✔ sleep(5:5)               multicluster-parallel-lmw2d-1696675651  13s
   ├-✔ sleep(6:6)               multicluster-parallel-lmw2d-1336174783  15s
   ├-✔ sleep(7:7)               multicluster-parallel-lmw2d-2767328819  29s
   ├-✔ sleep(8:9)               multicluster-parallel-lmw2d-3117624962  29s
   └-✔ sleep(9:10)              multicluster-parallel-lmw2d-2469206667  29s 
```

跨集群 1 和集群 2 运行该工作流仅用了 31 秒。最初，七个步骤能够并发运行，随后是剩余的三个步骤。请注意，一些步骤在集群 1 中运行，其他步骤在集群 2 中运行:

```
kubectl --context $ARGO_CLUSTER get pods 
```

输出:

```
NAME                                                      READY     STATUS      RESTARTS   AGE
cluster1-default-multicluster-parallel-lmw2d-1336174783   0/2       Completed   0          4m
cluster1-default-multicluster-parallel-lmw2d-1696675651   0/2       Completed   0          4m
cluster1-default-multicluster-parallel-lmw2d-2767328819   0/2       Completed   0          4m
cluster1-default-multicluster-parallel-lmw2d-3117624962   0/2       Completed   0          4m
cluster1-default-multicluster-parallel-lmw2d-479610983    0/2       Completed   0          4m
multicluster-parallel-lmw2d-1336174783                    0/2       Completed   0          4m
multicluster-parallel-lmw2d-1353848687                    0/2       Completed   0          4m
multicluster-parallel-lmw2d-1696675651                    0/2       Completed   0          4m
multicluster-parallel-lmw2d-2469206667                    0/2       Completed   0          4m
multicluster-parallel-lmw2d-2767328819                    0/2       Completed   0          4m
multicluster-parallel-lmw2d-3117624962                    0/2       Completed   0          4m
multicluster-parallel-lmw2d-479610983                     0/2       Completed   0          4m
multicluster-parallel-lmw2d-711387939                     0/2       Completed   0          4m
multicluster-parallel-lmw2d-714502387                     0/2       Completed   0          4m
multicluster-parallel-lmw2d-894725111                     0/2       Completed   0          4m
... (and all the pods from the single-cluster workflow) 
```

名称以“cluster1-default-”为前缀的五个 pod 是代表 pod。前缀表示它们的来源。其他单元是代理单元。

在集群 2 中，只有代表窗格:

```
kubectl --context $NON_ARGO_CLUSTER get pods 
```

输出:

```
NAME                                                      READY     STATUS      RESTARTS   AGE
cluster1-default-multicluster-parallel-lmw2d-1353848687   0/2       Completed   0          4m
cluster1-default-multicluster-parallel-lmw2d-2469206667   0/2       Completed   0          4m
cluster1-default-multicluster-parallel-lmw2d-711387939    0/2       Completed   0          4m
cluster1-default-multicluster-parallel-lmw2d-714502387    0/2       Completed   0          4m
cluster1-default-multicluster-parallel-lmw2d-894725111    0/2       Completed   0          4m 
```

### 场景 B:多集群 ETL

我们将用一个简单的 DAG 工作流来模拟这个场景，其中步骤 A 和 C 可以在集群 1 中运行，但是步骤 B 必须在集群 2 中运行；步骤 C 依赖于步骤 A 和 b。注意使用`multicluster.admiralty.io/clustername` pod 模板注释来强制放置:

```
#  A   B*
#   \ /
#    C
#
# * B must run in cluster2
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: multicluster-dag-
spec:
  entrypoint: multicluster-dag
  templates:
  - name: multicluster-dag
    dag:
      tasks:
      - name: A
        template: sleep
      - name: B
        template: sleep-remote
        arguments:
          parameters:
          - name: clustername
            value: cluster2 # change me
      - name: C
        dependencies: [A, B]
        template: sleep
  - name: sleep
    container:
      image: busybox
      command: [sleep, 10]
  - name: sleep-remote
    inputs:
      parameters:
      - name: clustername
    container:
      image: busybox
      command: [sleep, 10]
    metadata:
      annotations:
        multicluster.admiralty.io/elect: ""
        multicluster.admiralty.io/clustername: "{{inputs.parameters.clustername}}" 
```

如果`NON_ARGO_CLUSTER`不等于“cluster2 ”,请在提交之前修改工作流。

```
argo --context $ARGO_CLUSTER submit --serviceaccount argo-workflow --watch https://raw.githubusercontent.com/admiraltyio/multicluster-scheduler/master/config/samples/argo-workflows/blog-scenario-b.yaml 
```

这里是最终状态:

```
Duration:            26 seconds

STEP                       PODNAME                           DURATION  MESSAGE
 ✔ multicluster-dag-ftrwh
 ├-✔ A                     multicluster-dag-ftrwh-745251266  11s
 ├-✔ B                     multicluster-dag-ftrwh-728473647  12s
 └-✔ C                     multicluster-dag-ftrwh-711696028  12s 
```

注意，步骤 B 被委托给集群 2:

```
kubectl --context $NON_ARGO_CLUSTER get pods 
```

输出:

```
NAME                                                READY     STATUS      RESTARTS   AGE
cluster1-default-multicluster-dag-ftrwh-728473647   0/2       Completed   0          2m 
```

在真实的场景中，您将使用工件存储库和/或步骤输入/输出在步骤之间传输数据。

## 讨论:Pod 级联邦

正如我们所展示的，多集群调度器可以很好地与 Argo 集成。我们不必修改 Argo 源代码，对工作流清单的简单注释就足以让它们跨集群运行。对于像 [Federation v2](https://github.com/kubernetes-sigs/federation-v2) 这样的项目来说，这是不可能的，它要求客户使用新的联合 API，例如联合部署模板、放置和覆盖。multicluster-scheduler 的主要优点是它在 pod 级别联合集群，[“Kubernetes 对象模型中最小和最简单的单元”](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/)整个 Kubernetes 生态系统都是围绕豆荚展开的。通过选择 pods 作为多集群调度器的单元，我们实现了一系列“免费的”、松散耦合的集成。具有自定义和外部指标的多集群[水平 Pod 自动缩放器](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)*应该能够开箱即用(我们将很快证明这一点)，而与 [Istio](https://istio.io/) 和 [Knative](https://cloud.google.com/knative/) 的集成正在进行中。*

## 结论

Multicluster-scheduler 可以跨 Kubernetes 集群运行 Argo 工作流，将 pods 委托给资源可用的地方，或者按照用户的指定。它可以在不扩展集群的情况下加快并行工作流的运行速度，并简化多区域和多云 ETL 流程。这种集成是由以 pods 为中心的多集群调度器架构实现的，pods 是 Kubernetes 对象模型中最小和最简单的单元。前进的道路令人振奋，其中包括与云原生生态系统的更多集成，以及高级调度。我们很想听到社区的想法和反馈，我们欢迎贡献！

## 鸣谢

非常感谢 [Argo 的作者](https://github.com/argoproj/argo/graphs/contributors)设计了一个伟大的云原生工作流引擎，以及[控制器运行时](https://github.com/kubernetes-sigs/controller-runtime/graphs/contributors)的作者，它为多集群调度器的许多组件提供了动力。