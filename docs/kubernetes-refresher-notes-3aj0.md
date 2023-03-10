# kubrines-refresh notes-重新整理备注

> 原文：<https://dev.to/nirmal_kumar/kubernetes-refresher-notes-3aj0>

这篇文章是给任何人快速更新 Kubernetes 中的短语的。如果你开始探索 Kubernetes，那么这可以作为你应该知道的领域的清单。我还包含了一些使用 kubernetes 的基本命令。

## 概念:

*   忽必烈忽必烈忽必烈忽必烈忽必烈忽必烈忽必烈忽必烈忽必烈忽必烈忽必烈忽必烈忽必烈忽必烈忽必烈忽必烈
*   集群-主节点和工作节点
*   结节
*   节点端口
*   ClusterIP
*   有状态与无状态
*   部署
*   服务
*   豆荚
*   命名空间
*   语境
*   持久卷
*   PersistentVolumeClaim
*   主机路径(本地)
*   进入
*   LoadBalancer(负载平衡器)
*   配置图
*   秘密
*   域名系统(..svc.cluster.local
*   立方体管理员
*   RBAC
*   服务帐户
*   首次展示
*   复制比例

## 脚本

*   注释、标签和选择器
*   Kustomize (-k vs -f)
*   应用/创建/修补

## 局部库设置:

*   Mac 桌面坞站
*   迷你库贝

## 工具

*   [立方仪表板](https://github.com/kubernetes/dashboard)用于立方簇的 web UI
*   Kubectl 和代理
*   [Kubernetic 桌面](https://kubernetic.com/) -查看集群资源，类似于 Docker 的 Kitematic。
*   SEK ret-Kubernetes Secrets YAML 的加密工具
*   [Kamus](https://github.com/Soluto/kamus)-Kubernetes 应用的加密和解密解决方案
*   Kubectx——在 kubectl 中更快地在集群和名称空间之间切换
*   kube-ps1 - Kubernetes 为 bash 和 zsh 提示信息，以查看带有名称空间的当前集群上下文
*   Kubefwd -为当地发展提供大宗港口转运 Kubernetes 服务。

基本命令

### 语境

```
 # Get all contexts
kubectl config get-contexts 

# Get Current Context
kubectl config current-context

# Set the default context
kubectl config use-context nk-dev-cluster  

kubectl config set-context nk-dev --namespace=rafa-dev --cluster=docker-for-desktop-cluster --user=docker-for-desktop 
```

### 形容 K8 的资源

```
# kubectl describe <resource> <resourceName>
kubectl describe pods pod1

# Get the resource definition in yaml
kubectl get pod my-pod -o yaml 
```

### 公开一个服务

```
#expose a app named web-app as a service 

kubectl expose deployment web-app --name=web-app --type=NodePort --target-port=8080

# For Testing Locally
kubectl port-forward servicename 8080:8080 
```

### 连接到正在运行的 Pod 容器

```
 kubectl exec web-app-fcc6b5ddf-k2txq -it -- /bin/sh 
```

### 故障排除

```
 #Simple Container to quickly run some curl commands to the pods
kubectl run curl-test --image=radial/busyboxplus:curl -i --tty --rm 
```

感谢您阅读本复习资料。