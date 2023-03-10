# Kubernetes 中使用 TCP 代理的 kubectl 端口转发外部服务

> 原文：<https://dev.to/bzon/kubectl-port-forward-external-services-using-tcp-proxy-in-kubernetes-4li2>

## 剧情简介

一些工作负载连接到外部服务是很常见的，比如运行在 Kubernetes 集群之外的数据库。这些外部服务通常是安全的，并部署在私有子网中。

我的团队目前使用谷歌 Kubernetes 引擎，我们使用 GCP Memorystore (Redis 作为一种服务)进行缓存。为了安全地访问 Memorystore，我们必须提供一个充当堡垒主机的虚拟机，但是我们发现了一个更好的方法来处理这个问题。

### 使用 TCP 代理的端口转发

我们所有的服务都在 Kubernetes 上运行，在处理我们的服务时，我们希望尽可能地坚持使用`kubectl everything`工作流。多亏了 [tecnativa/tcp-proxy](https://github.com/Tecnativa/docker-tcp-proxy) ，它使得 tcp 代理与 Docker 真的很容易。

所以基本上，我们的 Kubernetes 集群可以访问的任何外部服务也可以通过部署这个吸盘在本地访问。

> 警告:这可能是你的安全漏洞，但不是我们的。

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    app: redis-proxy
  name: redis-proxy
  namespace: default
spec:
  selector:
    matchLabels:
      app: redis-proxy
  template:
    metadata:
      labels:
        app: redis-proxy
    spec:
      containers:
      - image: tecnativa/tcp-proxy:latest
        imagePullPolicy: Always
        env:
          - name: LISTEN
            value: ":6379" # The listen address that it will be exposed to.
          - name: TALK
            value: "10.1.1.5:6379" # Private address of Memorystore.
        name: redis-proxy
        resources:
          requests:
            cpu: 10m
            memory: 10m
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      securityContext: {} 
```

部署了 tcp-proxy 之后，我们现在就可以对 tcp-proxy Pod 进行端口转发了。

```
kubectl port-forward redis-proxy 6379:6379 
```

并使用`redis-cli`在本地访问 Memorystore。

```
redis-cli -p 6379 
```