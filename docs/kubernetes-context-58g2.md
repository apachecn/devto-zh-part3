# Kubernetes context

> 原文：<https://dev.to/dannypsnl/kubernetes-context-58g2>

## 在你开始之前

你应该已经安装了`kubectl`这个命令行工具。知道什么是库伯内特。了解为什么我们需要为不同的成员分离环境。

## 演示配置

首先，我们需要准备一个可以帮助我们学习上下文操作的配置。现在，创建一个名为`config_demo`的文件。

```
$ touch config_demo 
```

接下来，将这些内容复制到`config_demo`中。

```
apiVersion: v1
kind: Config

contexts:
- context:
    cluster: ""
    user: ""
  name: backend
- context:
    cluster: ""
    user: ""
  name: frontend 
```

在我们的假设中，我们有两种开发人员，前端和后端。

现在，输入`kubectl --kubeconfig config_demo config get-contexts`输出:

```
$ kubectl --kubeconfig config_demo config get-contexts
CURRENT NAME CLUSTER AUTHINFO NAMESPACE
          backend
          frontend 
```

看，我们现在有两个上下文。让我们进入下一步。

```
$ kubectl --kubeconfig config_demo config use-context backend
Switched to context "backend". 
```

显然，这个命令将我们的上下文改为现在的`backend`。但是我们如何在其他时候检查这个事实呢？开始了。

```
$ kubectl --kubeconfig config_demo config current-context
backend 
```

好了，现在我们已经知道了`kubectl config`这个命令的一些基本操作，让我们来看看你的配置文件的当前内容:

```
apiVersion: v1
clusters: []
contexts:
- context:
    cluster: ""
    user: ""
  name: backend
- context:
    cluster: ""
    user: ""
  name: frontend
current-context: backend
kind: Config
preferences: {}
users: [] 
```

我们加入了一些新的东西，但基本上，最重要的部分是`current-context`，所以现在你知道`kubectl`如何存储这些信息，然后你知道如何通过直接修改配置来创建它。

现在，让我们向这个配置添加一些用户。

```
apiVersion: v1
# ignore
users:
- name: frontend-developer
- name: backend-developer 
```

然后为他们添加设置凭证

```
$ kubectl --kubeconfig config_demo config set-credentials backend-developer --username=danny --password=danny
User "backend-developer" set.
$ kubectl --kubeconfig config_demo config set-credentials frontend-developer --username=notme --password=notme
User "frontend-developer" set. 
```

现在让我们看看配置
中有什么不同

```
apiVersion: v1
# ignore
users:
- name: backend-developer
  user:
    password: danny
    username: danny
- name: frontend-developer
  user:
    password: notme
    username: notme 
```

在这里，我们使用基本认证，我不建议在现实世界中使用这种认证，记住不要使用相同的用户名和密码！我曾经听到老师在创建一个例子时使用 1234 作为密码，而学生甚至在工作中也使用 1234，因为他们习惯于这样做，所以一定要让它发生在你身上！

让我们转到用法部分，我们将设置上下文的用户

```
$ kubectl --kubeconfig config_demo config set-context frontend --namespace frontend --user frontend-developer
Context "frontend" modified.
$ kubectl --kubeconfig config_demo config set-context backend --namespace backend --user backend-developer
Context "backend" modified. 
```

然后查看配置内部的变化

```
apiVersion: v1
# ignore
contexts:
- context:
    cluster: ""
    namespace: backend
    user: backend-developer
  name: backend
- context:
    cluster: ""
    namespace: frontend
    user: frontend-developer
  name: frontend 
```

如您所见，我们没有集群，因此无法执行某些操作，所以现在我们将使用 kubernetes for docker 环境默认集群来执行以下任务。为此，我们必须将集群添加到配置

```
apiVersion: v1
# ignore
- context:
    cluster: docker-for-desktop-cluster
    namespace: backend
    user: backend-developer
  name: backend 
```

对`frontend`也要这样做，记住我们没有创建集群，所以你已经在你的计算机上包含了`~/.kube/config`来得到`docker-for-desktop-cluster`

一旦这样做了，集群上的任何操作都需要身份验证信息，例如:

```
$ KUBECONFIG=~/.kube/config:config_demo kubectl get pod
Error from server (Forbidden): pods is forbidden: User "system:anonymous" cannot list pods in the namespace "backend" 
```

附言:只是一个小提示:在命令行中`po` as `pod` as `pods`。

如何登录用户是另一部分，所以我不会花时间在这上面，但我可以提供一些参考资料，以便您可以从这些文档中学习。[https://kubernetes . io/docs/reference/access-authn-authz/authentic ation/](https://kubernetes.io/docs/reference/access-authn-authz/authentication/)

## 实用

但是我所说的不会真正用在你的工作中，除非你是 kube-master 或者技术领导者或者其他什么人，你必须操作 kubernetes 中的这些基本部分。所以在这里我会告诉你如何正确使用上下文，这是设置`KUBECONFIG`这个环境变量！

在你的`.bashrc`、`.zshrc`或任何地方，插入这个:

```
export KUBECONFIG=$KUBECONFIG:config1:config2:config3 
```

配置来自哪里？通常，都是从你的云平台复制过来的，比如: **Azure** ， **GCP** ， **AWS** 。

不管怎样，这个配置已经包含了上下文信息，所以在你设置好你的`KUBECONFIG`之后，你可以通过`kubectl config get-contexts`看到很多上下文，然后你需要的就是使用它们！

感谢阅读，下次见。