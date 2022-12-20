# 在 Azure Kubernetes 上部署微服务(让我们加密)

> 原文：<https://dev.to/ianknighton/deploying-a-microservice-on-azure-kubernetes-with-lets-encrypt-4eon>

(我也在我的[博客](https://ianknighton.com/)上写了这个。如果你去看看会很酷。尽管现在那里什么都没有。)

我最近不得不在工作中努力克服这一点，我想分享我的文档，以防它可以在未来帮助别人。这是一个非常非常枯燥的帖子，但它应该涵盖了大多数基础知识。

# 问题

我们的一名开发人员刚刚在`golang`创建了一个新的微服务，我们需要将其部署到 Kubernetes 集群中，以便利用扩展能力。该服务需要可以通过网络访问，并受到 SSL 证书的保护。此时，应用程序能够使用`docker-compose`运行，并完全准备好进行部署。

# 解

**任何你看到`<something>`的地方都是一个变量将被使用的指示。你需要在整个过程中跟踪这些，因为很多都是重复使用的。**

### 依赖关系

需要在您的机器上安装以下工具并使其正常工作。

*   [azure-cli](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
*   [库贝克特尔](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
*   [kompose](https://github.com/kubernetes/kompose)
*   [舵](https://helm.sh/docs/using_helm/)

## 流程:

### 创建资源组

创建资源组:

```
az group create --name <resourceGroup>  --location <location> 
```

Enter fullscreen mode Exit fullscreen mode

### 创建集群

```
az aks create \
 --resource-group <resourceGroup>  \
 --name <clusterName>  \
 --node-count 1 \
    --enable-addons monitoring \
    --generate-ssh-keys 
```

Enter fullscreen mode Exit fullscreen mode

这个命令需要几分钟(有时大约 10 分钟)来运行，最后将返回一个 JSON 格式的输出，其中包含集群的设置。**复制并保存该信息**。

### 连接到集群

```
az aks get-credentials --resource-group <resourceGroup>  --name <clusterName> 
```

Enter fullscreen mode Exit fullscreen mode

完成后，验证您的连接。

```
kubectl get nodes 
```

Enter fullscreen mode Exit fullscreen mode

这应该会返回一个输出，列出状态为*就绪*
的节点

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-8675309-0    Ready     agent     2d        v1.9.11 
```

Enter fullscreen mode Exit fullscreen mode

### 初始化舵/舵杆

#### 创建服务账户

用下面的 YAML 在您的工作目录中创建一个名为`helm-rbac.yaml`的文件。

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system 
```

Enter fullscreen mode Exit fullscreen mode

用`kubectl`创建账户。

```
kubectl apply -f helm-rbac.yaml 
```

Enter fullscreen mode Exit fullscreen mode

#### 配置舵

为舵连接初始化船上的舵杆。

```
helm init --service-account tiller 
```

Enter fullscreen mode Exit fullscreen mode

### 创建入口控制器

```
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2 
```

Enter fullscreen mode Exit fullscreen mode

此过程将为群集创建一个 IP 地址，我们将需要它。

查找公共 IP 地址:

```
kubectl get service -l app=nginx-ingress --namespace kube-system 
```

Enter fullscreen mode Exit fullscreen mode

从该命令的输出中，您将需要`LoadBalancer`服务的`EXTENRAL-IP`。

```
NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)                      AGE
invincible-toucan-nginx-ingress-controller        LoadBalancer   10.0.186.72   192.167.15.243   80:30947/TCP,443:32654/TCP   2d invincible-toucan-nginx-ingress-default-backend   ClusterIP      10.0.173.78   <none>  80/TCP 
```

Enter fullscreen mode Exit fullscreen mode

### 安装证书管理器

*(快速编辑:Cert-Manager 版本 0.6 似乎存在问题。这个文档是针对版本`0.5.2`编写的，所以我更新了这个命令来指定版本。)*

```
helm install stable/cert-manager \
    --version 0.5.2 \
    --namespace kube-system \
    --set ingressShim.defaultIssuerName=letsencrypt-prod \
    --set ingressShim.defaultIssuerKind=ClusterIssuer 
```

Enter fullscreen mode Exit fullscreen mode

### 创建集群发布者

在您的工作目录中创建一个名为`cluster-issuer.yaml`的文件。

```
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: <emailAddress>
    privateKeySecretRef:
      name: letsencrypt-prod
    http01: {} 
```

Enter fullscreen mode Exit fullscreen mode

使用`kubectl`创建集群发布者。

```
kubectl apply -f cluster-issuer.yaml 
```

Enter fullscreen mode Exit fullscreen mode

### 创建证书对象

在您的工作目录中创建一个名为`certificate.yaml`的文件，并添加以下信息。

```
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - <url>
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - <url>
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer 
```

Enter fullscreen mode Exit fullscreen mode

使用`kubectl`应用该证书。

```
kubectl apply -f certificates.yaml 
```

Enter fullscreen mode Exit fullscreen mode

### 创建容器注册表

```
az acr create --resource-group <resourceGroup>  --name <registryName> --sku Basic 
```

Enter fullscreen mode Exit fullscreen mode

验证您能够登录到注册表。这些凭证可以在 Azure 门户的“访问密钥”刀片上找到。

```
az acr login --name <acrName> 
```

Enter fullscreen mode Exit fullscreen mode

### 授予从集群到注册表的访问权限

在您的工作目录中创建一个名为`grant-access.sh`的文件，包含以下信息:

```
#!/bin/bash

AKS_RESOURCE_GROUP=<resourceGroup>
AKS_CLUSTER_NAME=<clusterName>
ACR_RESOURCE_GROUP=<resourceGroup>
ACR_NAME=<registryName>

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID 
```

Enter fullscreen mode Exit fullscreen mode

保存并运行脚本，两者之间应该允许连接。

### 构建 Docker 映像并将其推送到注册表

将目录切换到 repo 并构建容器。

```
docker build -t <imageName>:<imageTag> . 
```

Enter fullscreen mode Exit fullscreen mode

一旦构建被验证，标记并将其推送到存储库。

```
docker tag <imageName>  <acrName>/<imageName>:<imageTag>
docker push <acrName>/<imageName>:<imageTag> 
```

Enter fullscreen mode Exit fullscreen mode

### 将映像部署到 Kubernetes 集群

`docker-compose.yml`文件已经存在于注册表中，所以只需要验证它。

```
docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

假设一切正常，使用`kompose`转换到 Kubernetes 部署/服务。

```
kompose -f docker-compose.yml up 
```

Enter fullscreen mode Exit fullscreen mode

验证部署和单元已创建并正在运行。

```
kubectl get deployments

NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE <service>  1         1         1            1           4h 
```

Enter fullscreen mode Exit fullscreen mode

```
kubectl get pods

NAME                         READY     STATUS    RESTARTS   AGE <service>-85b87ddc6-bfm7j    1/1       Running   0          4h 
```

Enter fullscreen mode Exit fullscreen mode

### 创建入口路由

创建一个名为`ingress-route.yaml`的文件，并添加以下内容:

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - <url>
    secretName: tls-secret
  rules:
  - host: <url>
    http:
      paths:
      - path: /
        backend:
          serviceName: <service-name>
          servicePort: 3000 
```

Enter fullscreen mode Exit fullscreen mode

用`kubectl`应用更改。

```
kubectl apply -f ingress-route.yaml 
```

Enter fullscreen mode Exit fullscreen mode

### 测试

通过在浏览器中导航到您的 URL 来验证一切正常。以我目前的经验来看，这些变化可能需要 20 分钟左右才能通过互联网传播出去。这可能会导致一些怪异的行为。

一个指标是，如果您看到一个正常情况下不会显示的 pod 为`cm-acme-http-solver`运行(`kubectl get pods`)。这意味着它仍在从 LetsEncrypt 收集证书。

## 资源:

*   [快速启动:使用 Azure CLI 部署 Azure Kubernetes 服务(AKS)集群](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough)
*   [在 Azure Kubernetes 服务(AKS)中使用 Helm 安装应用程序](https://docs.microsoft.com/en-us/azure/aks/kubernetes-helm)
*   [在 Azure Kubernetes 服务(AKS)上创建 HTTPS 入口控制器](https://docs.microsoft.com/en-us/azure/aks/ingress-tls)
*   [翻译一个坞站复合文件到库资源](https://kubernetes.io/docs/tasks/configure-pod-container/translate-compose-kubernetes/)
*   [带入口控制器 AK 的自定义域和 Azure Kubernetes】](https://stackoverflow.com/questions/51848544/custom-domain-and-azure-kubernetes-with-ingress-controller-aks)
*   [使用 Docker CLI 将您的第一个映像推送到私有 Docker 容器注册表中](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-docker-cli)