# 使用 Go 和 Kubernetes 进行数据库迁移

> 原文：<https://dev.to/plutov/database-migrations-with-go-and-kubernetes-266a>

具有数据库层的应用程序通常需要将数据库迁移作为其部署过程的一部分。通常，运行迁移是部署应用程序的第一步。

我尝试了不同的工具来运行迁移，我使用了 [goose](https://github.com/pressly/goose) 一段时间，但后来切换到 [migrate](https://github.com/golang-migrate/migrate) ，它支持许多驱动程序和源代码(例如，您可以将您的迁移保存在 S3 或谷歌存储中)，它也有很好的 CLI 和 Docker 支持。

此外，用 semver 封装应用程序迁移和标记 Docker 映像感觉非常自然，因此我们可以在部署管道中运行这个容器。

如果您正在使用 Kubernetes，在启动应用程序之前有多种方法可以运行迁移:

*   从 CD 系统手动运行 container(在这种情况下，您需要设置数据库访问)。
*   使用 Kubernetes 作业并在部署期间触发它。
*   使用`initContainer`并在主容器启动前运行迁移。

我更喜欢最后一个选项，因为应用程序将总是最新的数据库快照，而且如果迁移失败，它也会失败。此外，Kubernetes 将帮助我们缩短这一过程的时间。

步骤:

1.  准备好您的迁移，并将它们存储在一个可用的`migrate`源中(我将展示一个 Google Storage 的例子)
2.  将`initContainers`部分添加到 k8s 部署中。

```
initContainers:
  - name: migrations
    image: migrate/migrate:latest
    command: ['/migrate']
    args: ['-source', 'gcs://bucket/migrations', '-database', 'mongodb://mongo-0.mongo.default.svc.cluster.local:27017/db', 'up'] 
```

现在 Kubernetes 将在启动主 pod 之前运行这个容器。您将能够看到迁移失败原因的日志。