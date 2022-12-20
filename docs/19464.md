# 快速知识:Kubernetes 港口转运

> 原文：<https://dev.to/mfahlandt/fast-knowledge-kubernetes-port-forwarding--33d0>

当我最终在 Kubernetes 中发现将 pod 的一个端口转发到本地主机的选项时，它改变了我开始使用我的集群的方式。

我经常将我的 MongoDB、MYSQL 或 Elasticsearch 放在 Kubernetes 集群中，在 pods 上运行。自然，它没有公共接入点或端口转发到群集外部。但是，有时您希望使用客户端来访问数据库，或者希望在本地使用 cli 来查找错误。

解决方法是

```
kubectl port-forward localport:podport 
```

Enter fullscreen mode Exit fullscreen mode

这允许您轻松地将 mongoDB 端口转发到本地主机，例如

```
kubectl port-forward pods/mongo-0 27017:27017

or

kubectl port-forward deployment/mongo-0 27017:27017 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以使用 MongoDB 客户端(例如 Robo 3T)连接到 localhost:27017，这样您就连接到了远程 NOSQL 数据库。

欲了解更多信息，请点击此处