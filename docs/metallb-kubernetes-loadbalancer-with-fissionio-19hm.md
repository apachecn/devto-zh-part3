# MetalLB Kubernetes 带裂变的负载平衡器. io

> 原文：<https://dev.to/jmarhee/metallb-kubernetes-loadbalancer-with-fissionio-19hm>

我最近开始使用 [MetalLB](https://metallb.universe.tf/) 来管理我的集群数据中心的第 2 层和第 3 层网络，以便在裸机集群中提供负载平衡器类型的 Kubernetes 服务资源:

[jmarhee/packet-multi arch-k8s-terra form](http://github.com/jmarhee/packet-multiarch-k8s-terraform)

因此，对于 MetalLB 的这种配置:

```
apiVersion: v1
kind: Namespace
metadata:
  name: metallb-system
---
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: provider-network
      protocol: layer2
      addresses:
      - **192.168.1.200-192.168.1.250** 
```

您的地址池 192.168.1.200-192.168.1.250 将在您的服务负载平衡器的生命周期内提供该地址池中的一个地址。这非常有用，特别是对于像[裂变. io](http://fission.io) 路由器这样的服务，它可以根据需要从上游第 2 层或第 3 层网络服务获取 IP。

安装 MetalLB 时:

```
kubectl create -f metallb-config.yaml ;\
kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.7.3/manifests/metallb.yaml 
```

连接到该提供商网络的任何服务(意味着任何类型:负载平衡器)都将从 MetalLB 获得一个地址。所以，当你安装裂变:

【Kubernetes 的无服务器功能

您将看到一个类似路由器的服务，它将充当您的任务的入口点，该任务配置了 URI 路径和外部 IP:

```
jmarhee@controller:~$ kubectl get service router -n fission
NAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGE
router LoadBalancer 10.109.224.184 **192.168.1.200** 80:31180/TCP 9m40s 
```

然后所有任务都可以在访问，在这种情况下:

```
http://192.168.1.200/<task route> 
```

使用- url 参数创建任务将使任务 HTTP 触发器可访问:

```
**jmarhee@controller:~$** fission function create --name hello --env nodejs --code hello.js **--url /hello**
Package 'hello-js-9tb7' created
function 'hello' created
route created: GET /hello -> hello

**jmarhee@controller:~$** curl [**http://192.168.1.200/hello**](http://192.168.1.200/hello)
hello, world! 
```

对于 MetalLB 外部 ip 自动化和默认情况下使用裂变的入口设置而言，这是一个相当直接的使用案例，但是，这为裸机 Kubernetes(或那些不在 AWS 或 GCP 等托管云服务上的应用程序，或具有可用云控制器来调配和提取资源的 Kubernetes SaaS)创造了一些巨大的自动化机会，使其成为许多网络设置的重要实用工具，您可能会在集群的上游使用这些设置来提供服务:

[MetalLB，用于 Kubernetes 的裸机负载平衡器](https://metallb.universe.tf/concepts/)

结合 Kubernetes 自带的其他特性，您可以更严格地控制负载平衡服务(如裂变)的访问和安全选项(即 TLS，将此服务作为后端):

[使用 Kubernetes TLS Ingress 和 Platform9 裂变 FaaS 端点](https://medium.com/@jmarhee/add-tls-to-your-fission-io-endpoint-with-kubernetes-ingresses-913a78403edf)