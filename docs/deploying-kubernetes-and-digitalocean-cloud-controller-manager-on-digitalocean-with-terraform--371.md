# 使用 Terraform 在 DigitalOcean 上部署 Kubernetes(和 DigitalOcean 云控制器管理器)

> 原文：<https://dev.to/jmarhee/deploying-kubernetes-and-digitalocean-cloud-controller-manager-on-digitalocean-with-terraform--371>

我最近整合了一个 Terraform 存储库来管理数字海洋上的 Kubernetes 集群部署:

[https://bitbucket.org/jmarhee/cousteau/src/master/](https://bitbucket.org/jmarhee/cousteau/src/master/)

该项目的目标是部署一个集群，以及数字海洋云控制器的工作负载(允许 Kubernetes 提供负载平衡器和块存储等功能，作为 Kubernetes 资源，如服务`LoadBalancer`类型入口和使用`do-storage-class`的`Volumes`)以及用于扩展节点池和在数字海洋空间对象存储中存储[地形状态](https://www.terraform.io/docs/state/)的设施。

一旦您克隆了上面的存储库，在一个名为`terraform.tfvars`的文件中，您可以定义不同的变量，但是使用`terraform.tfvars.sample`文件作为模板，您可以提供两个非可选的变量:

```
digitalocean_token = "digitalocean_rw_api_key"

ssh_key_fingerprints = ["key:fingerprint1","key:fingerprint2"] 
```

Enter fullscreen mode Exit fullscreen mode

我建议查看`vars.tf`以获得可用变量的完整列表，然而，其中一个新的是`secrets_encrypt`(默认为`no`)，当设置为`yes`时，将为您的集群配置[静态秘密加密](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/)。

设置好所有选项后，规划 Terraform 脚本前的剩余步骤是使用 Makefile:
初始化 terraform 提供程序

```
make init-with-storage 
```

Enter fullscreen mode Exit fullscreen mode

这将提示您输入您的[数字海洋空间凭证](https://cloud.digitalocean.com/account/api/tokens)，然后您可以格式化、验证和计划您的部署:

```
make validate-apply 
```

Enter fullscreen mode Exit fullscreen mode

在此之前。

要扩展池，您可以将`terraform.tfvars`中的`count`修改为所需的值，并计划和应用另一次运行。更新连接键将只需要您刷新(或检索，此结果存储在 Terraform 状态中)来自 Terraform 的连接令牌，该令牌存储在控制器上的`random_string.kube_init_token_a`和`_b`中(连接以生成用于`kubeadm`的有效令牌，然后提供给`kubeadm token create [token]`以保持其处于 TF 状态，而不是 kubeadm 的本地令牌，并且必须手动将其提供给新节点)，然后缓冲节点池。

部署后，您可以使用部署的集群，开箱即用，通过附带的[数字海洋云控制器管理器](https://github.com/digitalocean/digitalocean-cloud-controller-manager)提供数字海洋负载平衡器资源和[卷](https://www.digitalocean.com/docs/kubernetes/how-to/add-persistent-storage/)，例如:

```
--------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.4
        ports:
        - containerPort: 80
--------
kind: Service
apiVersion: v1
metadata:
  name: nginx
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: LoadBalancer 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果您是`doctl`的用户，您可以看到在成功完成对上述清单的请求后，`doctl compute load-balancer list`的输出将显示一个新的负载平衡器对象。