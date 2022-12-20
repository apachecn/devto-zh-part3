# AppBinding 的案例

> 原文：<https://dev.to/tamal/the-case-for-appbinding-4ck4>

> 这篇文章最初发表在[这里](https://blog.byte.builders/post/the-case-for-appbinding/)。

Kubernetes 已经成为云本地世界事实上的指挥者。Kubernetes 坚持这样的理念，即内核应该很小，并允许开发人员编写自己的扩展。引入新资源类型的一种方式是使用`CustomResourceDefintions (CRD)`(最初称为`ThirdPartyResources`)。使用 CRDs，任何人都可以定义一个新的资源类型，其行为类似于标准的 Kubernetes 资源。这使得任何人都可以为定制资源编写控制器，并以软件形式获取操作知识。CoreOS 推广了术语“[操作符](https://coreos.com/blog/introducing-operators.html)”作为这种模式的名称。

在 [AppsCode](https://twitter.com/AppsCodeHQ) 时，我们已经用这个模型构建了各种 Kubernetes 原生应用。例如，我们一直在从事一个名为 [KubeDB](https://twitter.com/KubeDB) 的项目，该项目自动化了 Kubernetes 上的数据库管理。这有点像 AWS RDS，但是使用在 Kubernetes 上运行的容器。例如，要部署 PostgreSQL 数据库，您可以使用如下所示的 yaml 定义:

```
apiVersion: kubedb.com/v1alpha1
kind: Postgres
metadata:
  name: quick-postgres
  namespace: demo
spec:
  version: "10.2-v1"
  storageType: Durable
  storage:
    storageClassName: "standard"
    accessModes:
    - ReadWriteOnce
    resources:
      requests:
        storage: 1Gi
  terminationPolicy: DoNotTerminate 
```

## 连接运算符

随着时间的推移，越来越多的在 Kubernetes 上运行日常操作的功能通过 CRD 及其相关的操作符获得。例如，我们的用户已经[要求](https://github.com/kubedb/project/issues/141) us 支持使用 [HashiCorp Vault](https://www.vaultproject.io/) 对 KubeDB 管理的数据库进行用户管理。Vault 是一个机密管理服务器，用于管理各种类型的机密，包括数据库机密。我们一直致力于一个名为 [KubeVault](https://twitter.com/KubeVault) 的项目，通过 Vault 为秘密管理带来 Kubernetes 本地体验。现在，我们面临的挑战之一是如何将 KubeDB 项目的 CRDs 与 KubeVault 项目的 CRDs 连接起来。以下是要求:

*   不应该强迫用户同时使用这两个项目。他们可以选择将其中一个项目用于他们的预期目的。但是他们应该能够利用它们来获得额外的功能。
*   用户应该能够引入在 Kubernetes 集群之外管理的数据库，并使用 KubeVault 操作符为这些数据库发布秘密。例如，用户可能正在使用 AWS RDS 管理的数据库。他们想用 KubeVault 项目来管理这类数据库的秘密。或者，用户可能希望使用 Helm charts 提供数据库，但使用 KubeVault 进行用户管理。

这些需求意味着我们不能紧密耦合来自不同项目的 CRD。相反，我们需要一个中间资源来连接由不同工具管理的服务。

## 连接配置

连接不同服务所需的关键信息通常是应用程序的 URL、一些秘密凭证和/或一些配置参数。因此，我们考虑使用一个标准的 Kubernetes `Service`对象。但是这将意味着我们必须使用注释来为这样的服务指向额外的秘密和配置映射。我们希望避免使用注释作为规范。此外，如果这样的服务托管在 Kubernetes 集群之外，那么 Kubernetes `Service`对象似乎不适合存储连接信息。

## 服务目录

我们环顾四周，看看社区中的其他人是否已经解决了这个问题。我们找到的关闭项目是[服务目录](https://github.com/kubernetes-incubator/service-catalog)。服务目录允许任何人直接从本地 Kubernetes 工具中舒适地配置云服务。该项目通过[开放服务代理 API](https://github.com/openservicebrokerapi/servicebroker) 将服务代理集成到 Kubernetes 生态系统中。服务目录项目有一个名为 [`ServiceBinding`](https://github.com/kubernetes-incubator/service-catalog/blob/master/docs/v1/api.md#servicebinding-resource) 的资源类型，可用于表示通过 service broker 管理的资源的连接信息。但是它有两个主要问题:

*   服务目录期望资源由服务代理管理。但是在我们的例子中，资源可能已经存在，并且没有被任何代理管理。回到前面的例子，用户可能已经有了一个 AWS RDS 管理的 PostgreSQL 数据库。他们只是想用 KubeVault 来管理它的秘密。
*   服务目录项目是作为 Kubernetes 扩展 apiserver 实现的。这意味着服务目录的用户必须部署和管理额外的 etcd 集群。这给用户带来了极大的操作复杂性。对于像 KubeVault 这样的运营商来说，依赖扩展的 apiserver 是不可接受的。

## AppBinding

因此，我们提出了`AppBinding`的概念。AppBinding 使用应用程序的 URL(通常用于非 Kubernetes 常驻服务实例)或 Kubernetes 服务对象(如果托管在 Kubernetes 集群中)、一些可选参数和一个凭证密码指向应用程序。AppBinding 与服务目录中的 ServiceBinding 概念非常相似，主要区别如下:

*   AppBinding 是一个 CRD。因此，任何想要依赖 AppBinding 概念的项目都可以注册 CRD 类型并开始使用它。使用 AppBindign 资源的操作过载为零。
*   AppBinding 可以通过其 URL 或服务引用来引用应用程序(如果托管在 Kubernetes 集群中)。不需要提出服务类别、服务计划等。就像服务目录的情况一样。

下面是一个指向 PostgreSQL 实例的 AppBinding 示例。

```
apiVersion: appcatalog.appscode.com/v1alpha1
kind: AppBinding
metadata:
  name: postgres-app
  namespace: demo
spec:
  secret:
    name: postgres-user-cred # secret
  clientConfig:
    service:
      name: postgres
      scheme: postgresql
      port: 5432
      path: "postgres"
      query: "sslmode=disable"
    insecureSkipTLSVerify: true
  parameters:
    # names of the allowed roles to use this connection config in Vault
    allowedRoles: "*" 
```

现在，KubeVault 操作者可以从它自己的 CRD 中引用这个数据库对象，并相应地发布秘密。

你可以在 [GitHub](https://github.com/kmodules/custom-resources/blob/master/apis/appcatalog/v1alpha1/appbinding_types.go) 上找到 AppBinding 的类型定义和自动生成的 Go 客户端。

如果你一直读到最后，我要感谢你。如果你有任何问题，想知道更多，你可以通过 [Twitter](https://twitter.com/tsaha) 或 [Email](mailto:tamal@appscode.com) 联系我。