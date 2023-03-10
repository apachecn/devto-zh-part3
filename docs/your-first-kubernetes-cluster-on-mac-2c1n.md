# 您在 Mac 上的第一个 Kubernetes 集群

> 原文：<https://dev.to/greenido/your-first-kubernetes-cluster-on-mac-2c1n>

[![](img/b8cfe0cc0fe2d591ee001add37c6bab3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iw00rzJa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://greenido.files.wordpress.com/2019/04/screen-shot-2019-04-23-at-9.43.46-am.png%3Fw%3D696)

如果你想了解 Kubernetes，第一件事就是亲自尝试。既然很多开发者都在用 Mac，那就来看看怎么运行吧。这篇文章是我第一次“玩”K8b 时写的“清单”……如果你想知道更多细节，请查看官方网站。

首先，在 Mac 上，我们将安装一些工具来帮助我们运行它。在第 0 步，你应该安装 [brew](https://brew.sh/) (或者他们称之为家酿)。您可以在终端中使用一行代码来完成:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将安装 kubectl，它将帮助我们从终端控制 K8b:

```
$ brew install kubectl 
```

Enter fullscreen mode Exit fullscreen mode

kubectl 是用于访问任何 Kubernetes 集群的二进制文件。之后，我们需要一个虚拟机(它将托管 k8b ),在这种情况下，我们将使用虚拟机箱:

```
$ brew cask install virtualbox 
```

Enter fullscreen mode Exit fullscreen mode

Minikube 运行在 Linux 和 Mac 上的虚拟机中。

让我们安装 minikube:

```
$ brew cask install minikube 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以开始玩我们的第一个 k8s 集群了。检查以下几条命令，了解如何启动 *minikube* 检查其状态并停止。

```
# Let's start the $ minikube start --vm-driver=virtualbox --logtostderr #Give minikube a bit of time... the first run might take a few minutes # check what is going on $ minikube status host: Running kubelet: Running apiserver: Running kubectl: Correctly Configured: pointing to minikube-vm at 192.168.99.100 # Let see the UI from our browser $ minikube dashboard http://127.0.0.1:50723/api/v1/namespaces/kube-system/services/http:kubernetes-dashboard:/proxy/#!/overview?namespace=default # Now you might want to stop it $ minikube stop 
```

Enter fullscreen mode Exit fullscreen mode

您可能需要从客户端环境控制多个集群。例如，在 minikube 上运行一个本地 k8s 集群，在 GKE 上运行一个。要查看、列出和切换当前上下文，您可以运行下面的命令

```
$ kubectl config current-context $ kubectl config get-contexts $ kubectl config use-context minikube 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过以下方式与 k8s 集群进行交互:

*   ku bectl–这是一个命令行界面(CLI)。
*   图形用户界面(GUI)，云提供商仪表板。注意 minikube 也有内置的 UI 界面。
*   您可以通过`$ minikube dashboard`进入

默认情况下， *kubectl* 从内部的配置文件中获取主节点端点和凭证。kube 目录。它通常位于用户主目录下。我们也可以通过
查看设置

```
$ kubectl config view 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以使用 kubectl 来获得当前集群的详细信息:

```
$ kubectl cluster-info 
```

Enter fullscreen mode Exit fullscreen mode

*Kubernetes master 运行于*[https://192 . 168 . 64 . 2:8443](https://192.168.64.2:8443)

*根据您的设置，可能会有所不同。

如果我们有很多想要组织成团队/项目的用户，我们可以使用[名称空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)将 Kubernetes 集群划分成子集群。在一个名称空间中创建的资源/对象的名称是唯一的，但在不同的名称空间中却不是。

要列出所有的名称空间，我们可以运行下面的命令:

```
$ kubectl get namespaces 
```

Enter fullscreen mode Exit fullscreen mode

通常，Kubernetes 创建两个默认名称空间: *kube-system* 和 *default* 。kube-system 名称空间包含由 Kubernetes 系统创建的对象。默认命名空间包含属于任何其他命名空间的对象。默认情况下，我们连接到默认的名称空间。 *kube-public* 是一个特殊的名称空间，所有用户都可以读取，用于特殊目的，比如引导集群。这个名称空间现在存在于用 *kubeadm* 创建的集群中。它包含一个 ConfigMap 对象 cluster-info，用于帮助发现和安全引导(基本上，包含集群的 CA 等)。它不需要身份验证就可以读取，而且由于 k8s 集群的实现方式不同，默认情况下，并非所有集群都有这个名称空间。

[![🛑](img/20a6c7a43f42f4a0704b872d69c37c66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DWL6qEfz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f6d1.png) 在默认命名空间下部署应用程序是一种不好的做法。

现在，如果您希望将一个容器(带有一个应用程序)部署到您的集群。检查我们的集群中有哪个节点:

```
$ kubectl get nodes 
```

Enter fullscreen mode Exit fullscreen mode

在您获得我们拥有的一个节点后，您可以尝试:

```
$ kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080 
```

Enter fullscreen mode Exit fullscreen mode

并验证:

```
$ kubectl get deployments NAME READY UP-TO-DATE AVAILABLE AGE kubernetes-bootcamp 1/1 1 1 2m22s 
```

Enter fullscreen mode Exit fullscreen mode

应用程序正在运行，但如果我们希望看到它，我们需要“打开”网络。在 Kubernetes 内部运行的 pod 是在一个私有的、隔离的网络上运行的。默认情况下，它们在同一个 kubernetes 集群中的其他 pods 和服务中是可见的，但在该网络之外是不可见的。

当我们使用`kubectl`时，我们通过 API 端点与我们的应用程序通信。

`kubectl`命令可以创建一个代理，将通信转发到集群范围的专用网络中。代理可以通过按下 control-C 来终止，并且在运行时不会显示任何输出。你想在一个新的终端窗口上运行这个命令，并让它在后台运行:

```
$ kubectl proxy Starting to serve on 127.0.0.1:8001 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们获取 pod 名称并存储它以备将来使用:

```
$ export POD\_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}') echo Name of the Pod: $POD\_NAME 
```

Enter fullscreen mode Exit fullscreen mode

请看我们的应用程序(在本例中，只是一个返回字符串):

```
$ curl http://localhost:8001/api/v1/namespaces/default/pods/$POD\_NAME/proxy/ 
```

Enter fullscreen mode Exit fullscreen mode

我们应该得到:

```
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-6bf84cb898-mwb42 | v=1 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看日志:

```
$ kubectl logs $POD\_NAME Kubernetes Bootcamp App Started At: 2019-04-23T17:09:00.785Z | Running On: kubernetes-bootcamp-6bf84cb898-mwb42 Running On: kubernetes-bootcamp-6bf84cb898-mwb42 | Total Requests: 1 | App Uptime: 48.01 seconds | Log Time: 2019-04-23T17:09:48.795Z Running On: kubernetes-bootcamp-6bf84cb898-mwb42 | Total Requests: 2 | App Uptime: 86.209 seconds | Log Time: 2019-04-23T17:10:26.994Z Running On: kubernetes-bootcamp-6bf84cb898-mwb42 | Total Requests: 3 | App Uptime: 214.666 seconds | Log Time: 2019-04-23T17:12:35.451Z 
```

Enter fullscreen mode Exit fullscreen mode

最常见的操作可以用下面的 kubectl 命令来完成:

**ku bectl get**–列出资源

**ku bectl describe**–显示资源的详细信息

**ku bectl exec**–在 pod 中的容器上执行命令。例如，要查看所有变量:

```
$ kubectl exec $POD\_NAME env PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin HOSTNAME=kubernetes-bootcamp-6bf84cb898-mwb42 KUBERNETES\_PORT=tcp://10.96.0.1:443 KUBERNETES\_PORT\_443\_TCP=tcp://10.96.0.1:443 KUBERNETES\_PORT\_443\_TCP\_PROTO=tcp KUBERNETES\_PORT\_443\_TCP\_PORT=443 KUBERNETES\_PORT\_443\_TCP\_ADDR=10.96.0.1 KUBERNETES\_SERVICE\_HOST=10.96.0.1 KUBERNETES\_SERVICE\_PORT=443 KUBERNETES\_SERVICE\_PORT\_HTTPS=443 NPM\_CONFIG\_LOGLEVEL=info NODE\_VERSION=6.3.1 HOME=/root 
```

Enter fullscreen mode Exit fullscreen mode

或者在 Pod 的容器中打开 bash 会话:

```
$ kubectl exec -ti $POD\_NAME bash 
```

Enter fullscreen mode Exit fullscreen mode

下一步是使用服务来公开您的应用程序。

你可以在[这里](https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/)阅读并尝试一下。

玩得开心！

[![](img/638b4c5e6e7fd9d1bb7e08efede57162.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pc0wB1Fp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://greenido.files.wordpress.com/2019/04/screen-shot-2019-04-23-at-10.18.29-am.png%3Fw%3D696)