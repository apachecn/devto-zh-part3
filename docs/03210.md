# 地形-无舵舵部署

> 原文：<https://dev.to/ksivamuthu/terraform-tillerless-helm-deployment-5bhc>

Kubernetes 的 helm 包管理器帮助您在 Kubernetes 集群上安装和管理应用程序。更多信息，参见[舵文档](https://github.com/helm/helm)

Helm 有两个主要组件- Helm 客户端和 Tiller 服务器。

如在文档中:

Helm 客户端是面向最终用户的命令行客户端。客户端负责以下域:

*   本地图表开发
*   管理存储库
*   与 Tiller 服务器交互
*   发送要安装的图表
*   询问关于版本的信息
*   请求升级或卸载现有版本

Tiller 服务器是一个集群内服务器，它与 Helm 客户机交互，并与 Kubernetes API 服务器接口。服务器负责以下工作:

*   监听来自 Helm 客户端的传入请求
*   将图表和配置结合起来构建一个版本
*   将图表安装到 Kubernetes 中，然后跟踪后续版本
*   通过与 Kubernetes 交互来升级和卸载图表

简而言之，客户端负责管理图表，服务器负责管理发布。

当您在集群上运行 tiller 服务器时，tiller 服务器将获得其全部 Kubernetes 管理员权限。Tiller 服务器成为攻击媒介。

当您在本地机器上运行 tiller 服务器时，用户不会继承集群上的 tiller 服务器权限。相反，tiller 继承了最终用户的 Kubernetes 权限。这是在没有 tiller 服务器的远程 kubernetes 集群上安装 helm chart 的推荐方法。

## 就地安装舵柄&舵

*   使用 macOS 上的 homebrew 或 windows 上的 chocolatey 在本地安装 helm。

```
 //macOS
  $ brew install kubernetes-helm

  //Windows
  $ choco install kubernetes-helm

  // Linux/Unix script
  $ curl -LO https://git.io/get_helm.sh
  $ chmod 700 get_helm.sh
  $ ./get_helm.sh 
```

Enter fullscreen mode Exit fullscreen mode

*   用下面的命令创建一个名为 tiller 的名称空间。

```
kubectl create namespace tiller 
```

Enter fullscreen mode Exit fullscreen mode

*   将 TILLER_NAMESPACE 环境变量设置为 TILLER。

```
export TILLER_NAMESPACE=tiller 
```

Enter fullscreen mode Exit fullscreen mode

*   在本地启动 tiller 服务器

```
tiller -listen=localhost:44134 -storage=secret 
```

Enter fullscreen mode Exit fullscreen mode

*   使用- client-only 标志初始化 Helm 客户端，以确保 tiller 没有安装在 kubernetes 集群中

```
export HELM_HOST=:44134
helm init --client-only 
```

Enter fullscreen mode Exit fullscreen mode

*   验证 helm 是否与 tiller 服务器正常通信。

```
helm repo update 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可以在 helm 客户端窗口中运行任何 helm 命令(如 helm install chart_name)来安装、修改、删除或查询集群中的 helm 图表。

## 舵柄插件

Rimas Mocevicius 为此开发了一个头盔插件。详见[的博文](https://rimusz.net/tillerless-helm)。

```
$ helm plugin install https://github.com/rimusz/helm-tiller 
```

Enter fullscreen mode Exit fullscreen mode

它有`start`、`start-ci`、&、`stop`等命令

`start`命令启动 tiller 服务器。`start-ci`命令用于启动 tiller 服务器和 ci 环境。`stop`命令停止蒂勒服务器。

在 github 文档中可以看到这个舵舵杆插件的更多命令

## 地形脚本做无舵舵部署

下面的设置是使用运行在本地主机上的 tiller 服务器和`terraform-helm-provider`

helm-tiller 插件`start-ci`命令用于在本地主机上启动 tiller，通过 local-exec provisioner 资源调用。

Helm provider 使用 tiller 服务器主机配置为 localhost:44134，并将`install_tiller`设置为 false 以不在 kubernetes 集群中安装 tiller 服务器。

运行 terraform 脚本会旋转 tiller 服务器，并在 helm provider 中配置的 kubernetes 集群中安装 helm chart。

```
resource "null_resource" "tiller" {
  provisioner "local-exec" {
    command = "export KUBECONFIG=${var.kubeconfig_path} && helm tiller start-ci &"
  }
}

provider "helm" {
  host            = "127.0.0.1:44134"
  install_tiller  = false

  kubernetes {
    host        = "${var.cluster_endpoint}"
    config_path = "${var.kubeconfig_path}"
  }
}

resource "helm_release" "redis" {
    name      = "redis"
    chart     = "stable/redis"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总结起来

舵 v3 应该是无舵的。你可以在这里阅读 Helm v3 设计方案[。helm-tiller 插件和 terraform 脚本使用 helm-tiller 命令在本地主机上启动 tiller 服务器，有助于在 Helm v2 版本上安全地进行部署。](https://github.com/helm/community/blob/master/helm-v3/000-helm-v3.md)

快乐编码，希望这篇文章有助于设置无舵舵舵部署的地形脚本。