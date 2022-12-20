# kubernetes 集群中的多租户

> 原文：<https://dev.to/ankitbansal/multi-tenancy-in-kubernetes-cluster-44me>

集装箱化和 kubernetes 的采用正在迅速增加。在 kubernetes 集群中运行您的应用程序的一些主要好处是快速供应、轻量级、自动伸缩、无服务器架构。然而，它们也带来了新的问题需要解决。其中一个问题是，由于资源是共享的，如何确保资源的公平利用，并避免一个受损害的租户影响其他租户。另一个更重要的问题是安全性，以及如何在租户之间实现安全的资源隔离。

**什么是多租户**

多租户是一种架构模式，其中多个客户通过单个应用程序实例获得支持。每个客户被称为应用程序的租户，实际上可以是个人或组织。当您的系统支持大量客户时，多租户是一个非常有吸引力的价值主张，因为它避免了单独维护系统。为了避免一个租户影响其他租户，为每个租户提供了定义明确的资源隔离。在多租户集群中，从不同的租户部署多个应用程序。提供商有责任确保租户相互隔离。

我们在将平台迁移到 kubernetes 时遇到了这个问题。Kubernetes 集群由各种资源层组成，例如节点、名称空间、pod 和容器，因此可以在多个级别上实现隔离。为 kubernetes 建议的默认隔离是将每个租户分隔在不同的名称空间中。然而，它确实带来了各种重要的安全考虑。作为一个多租户平台，提供安全性和隔离对我们的业务至关重要。经过深思熟虑，我们决定采用节点级隔离。在本文中，我将讨论名称空间级隔离和节点级隔离及其优缺点。

**基于名称空间的隔离**

大多数 kubernetes 资源都在一个名称空间中。如果不为资源指定名称空间，它将进入默认名称空间。名称空间更像是一个表示和管理集群资源的逻辑实体。您可以将其视为集群本身中的一个虚拟集群。基于名称空间的隔离是一种可用于实现多租户的方法。想法是让每个租户在不同的名称空间中运行。

您可以在集群中创建任意数量的名称空间。命名空间可以这样创建:

```
{
  "apiVersion": "v1",
  "kind": "Namespace",
  "metadata": {
    "name": "tenant1",
    "labels": {
      "name": "tenant1"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> ku bectl create-f namespace . YAML

```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: tenant1-role
  namespace: tenant1
rules:
  - apiGroups: [""]
    resources: ["pods", "secrets"]
    verbs: ["get", "list", "watch"] 
```

Enter fullscreen mode Exit fullscreen mode

> kubectl create -f role.yaml

```
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: tenant1-role-binding
  namespace: tenant1
subjects:
  - kind: ServiceAccount
    name: tenant1-account
    namespace: tenat1
roleRef:
  kind: Role
  name: tenant1-role
  apiGroup: rbac.authorization.k8s.io 
```

Enter fullscreen mode Exit fullscreen mode

> 多维数据集创建-f rolebinding.yaml

接下来，您需要创建网络策略来确保跨名称空间的通信被阻止。默认情况下，集群中的 pod 之间允许通信，但是，您可以定义策略来阻止所有通信，然后启用显式通信。注意对这些策略的支持取决于群集提供程序使用的网络插件。

此策略将阻止所有进入 pod 的流量。

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
spec:
  podSelector: {}
  policyTypes:
  - Ingress 
```

Enter fullscreen mode Exit fullscreen mode

> ku bectl create-f default policy . YAML

然后，您可以显式指定另一个网络策略，以允许命名空间内的流量。注意网络策略本质上是附加的。

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: within-namespace
  namespace: tenant1
spec:
  podSelector:
  ingress:
    - from:
      - NamespaceSelector:
          matchLabels:
            name: tenant1 
```

Enter fullscreen mode Exit fullscreen mode

> kubectl create -f 命名空间策略. yaml

您可以用同样的方式指定出口策略。

通过这样做，我们确保了我们的资源被分离出来供租户使用。然而，仍然存在一些问题。如果一个租户试图创建太多资源，会导致其他租户速度变慢或没有足够的计算可用。Kubernetes 支持指定可应用于每个容器或跨名称空间的限制范围和资源配额。例如，此资源配额将确保在命名空间内，总使用量不会超过指定的限制。

```
apiVersion: v1
kind: ResourceQuota
metadata:
  name: mem-cpu-demo
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi 
```

Enter fullscreen mode Exit fullscreen mode

> 忽必烈 apply-f resource quota . YAML–namespace = tenant 1

这够了吗？

问题是，做所有这些是否足够，是否可以依赖。事实上，内核仍然是共享的，可能会有一些漏洞，导致用户访问节点或节点中的容器。你可以在 docker 里看看最近的一些[漏洞](https://www.cvedetails.com/vulnerability-list/vendor_id-13534/product_id-28125/Docker-Docker.html)。这可能会对业务造成重大影响，对于多租户平台，这是绝对应该避免的。有[吊舱安全政策](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)可以实现一些隔离和安全，但他们似乎不足以对抗这一点。如果您计划在集群中运行自己的代码而不是定制代码，或者如果您计划支持内部系统或客户组织中的多个团队，您可以选择这种策略。对我们来说不是这样。因此，我们决定采用节点级隔离。

**基于节点的隔离**

并非 kubernetes 中的所有资源都绑定在一个名称空间中。节点和永久卷等低级资源在命名空间之间共享。这个想法是为了确保一个租户的 pods 被安排在不同的节点上。这确保内核由相同租户和卷装载的容器共享，且主机内核不再有风险。在这种情况下，我们使用下面的命令用租户信息标记节点。

> kubectl 标签节点 worker1 tenant=tenant1

现在，您可以指定只将 pod 调度到具有该标签的节点。

```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: some-container
    image: customImage
    imagePullPolicy: Always
  nodeSelector:
    tenant: tenant1 
```

Enter fullscreen mode Exit fullscreen mode

这样，您可以确保您的应用程序在所需的节点上得到调度。此外，您将需要创建额外的基础设施来监视 kubernetes 中关联的节点，并自动识别何时需要汇集新节点。这种解决方案确实实现了多租户，但是会导致资源利用率降低。鉴于目前在 kubernetes 中缺乏适当的解决方案，我们发现这种方法符合我们的目的。尽管如此，仍然有一个问题，即主节点是共享的。在我们的案例中，我们没有太多的选择，因为我们使用的是托管集群。然而，我认为 kubernetes 应该在这方面提供更多的隔离。然而，这很好地满足了我们的目的。

**为什么不按租户划分集群**

每个租户一个集群实际上无法与这两个选项相比。然而，在某些情况下，当您只有几个租户或几个团队需要管理时，您会发现需要为每个租户或团队创建单独的集群。如果这个策略对你有用，那就去做吧。这将避免你在前面描述的场景中遇到的许多头痛。

**前方的路**

可以看出，这两个问题各有利弊。尽管名称空间隔离提供了更好的资源利用，但它确实会导致潜在的安全问题。另一方面，节点隔离确实会导致资源利用效率低下。我们目前拥有的多租户需求和解决方案之间似乎存在差距，各种其他解决方案正在加速填补这一差距。看起来有希望的解决方案有:

gVisor : [gVisor](https://gvisor.dev/) 是一个由 google 构建的用户空间内核，在许多 google 项目中使用。它实现了 linux 系统接口的重要部分，包括文件系统、管道、信号等。它还利用 runsc 实用程序来确保应用程序和主机内核之间的隔离边界。因此，gVisor 提供了一种轻量级的虚拟机替代方案，同时具有清晰的资源分离。kubernetes 正在努力实现这一目标。

另一个正在开发的有趣概念是[卡式容器](https://katacontainers.io/)。Kata 容器是轻量级的虚拟机，它运行在一个专用的内核上，提供网络、内存、I/O 等的隔离。它们建立在开放容器倡议(OCI)的标准之上，因此可以直接得到 kubernetes 的支持。然而，这个项目仍处于早期开发阶段。