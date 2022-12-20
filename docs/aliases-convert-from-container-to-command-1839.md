# 别名:从容器转换为命令

> 原文：<https://dev.to/kinzal/aliases-convert-from-container-to-command-1839>

我们开发了一个名为“别名”的工具，将`docker run`定义为 YAML，并作为命令执行。

[GitHub - k-kinzal/aliases:解析容器对所有命令的依赖](https://github.com/k-kinzal/aliases)

由于 aliases 使用容器，安装唯一的 docker 允许您以相同的方式执行命令，而不依赖于环境。

## 开始使用

```
$ curl -sSL 'https://github.com/k-kinzal/aliases/releases/download/v0.2.1/darwin-amd64.tar.gz' | tar xvz
$ cp aliases /usr/local/bin/aliases 
```

**$HOME/。别名/aliases.yaml**

```
/usr/local/bin/kubectl:
  image: chatwork/kubectl
    tag: 1.11.2
  env:
    KUBECONFIG: $KUBECONFIG
    volume:
  - $HOME/.kube:/root/.kube
  - $PWD:/work
  workdir: /work 
```

通过定义命令名、图像、标签和`docker run`选项，别名可用作命令。

```
$ eval "$(aliases gen --export)" 
```

通过执行`aliases gen`，命令将被设置为`PATH`并可用。

```
$ kubectl get all 
```

您只需这样做就可以执行该命令。
别名中定义的命令在您的环境、您同事的环境或 CI 环境中的工作方式相同。

您可以解决该命令在安装方法方面遇到的问题，因为它不能与所需版本一起工作。

## 切换命令执行的版本

kubernetes 集群和`kubectl`命令必须是相同的版本。
所以，如果你有多个 kubernetesk 集群，你会为如何使 kubernetes 集群和`kubectl`版本相同而烦恼。

爱丽丝可以做到。

```
$ kubectl get all                       # It works with the version defined by aliases.yaml
$ KUBECTL_VERSION=1.8.4 kubectl get all # It works with the version by environment variable 
```

用环境变量`[COMMAND NAME] _VERSION`覆盖`aliases.yaml`的`tag`。
只需这一个就能轻松切换版本。你不必再考虑使用像`rbenv`或`nvm`这样的版本切换机制。

## 组合多个容器并解决对命令的依赖

执行容器的一个问题是你不能执行一个没有安装在容器镜像中的命令。

例如，当使用`helmfile`时，你需要将`helm`安装在同一个容器中。
然而，当你想使用`kubectl`命令和其他来自`helmfile`的命令时，你必须多次重新创建容器映像。

在别名中，您可以通过定义命令依赖性来解决对其他命令的依赖性，而无需重新创建容器映像。

```
/usr/local/bin/kubectl:
  image: chatwork/kubectl
    tag: 1.11.2
  env:
    KUBECONFIG: $KUBECONFIG
    volume:
  - $HOME/.kube:/root/.kube
  - $PWD:/work
  workdir: /work

/usr/local/bin/helm:
  image: chatwork/helm
  tag: 2.11.0
  volumes:
  - /tmp:/tmp
  - $HOME/.helm:/root/.helm
  - $PWD:/work
  workdir: /work

/usr/local/bin/helmfile:
  image: chatwork/helmfile
  tag: 0.40.1-2.11.0-1.11.2
  volumes:
  - /tmp:/tmp
  - $HOME/.kube:/root/.kube
  - $HOME/.helm:/root/.helm
  - $PWD:/work
  workdir: /work
  dependencies:
  - /usr/local/bin/kubectl
  - /usr/local/bin/helm 
```

通过这样定义`dependencies`，你可以从`helmfile`开始执行`kubectl`或`helm`。
您不再需要创建自己的容器映像。
如果有正式发布的容器图像，您可以使用它。如果需要依赖新命令，可以将它们组合起来。

但是，该功能使用 docker `privileged`。
与不可信的图像一起使用不安全。

## 在 CircleCI 上执行

独立于环境的别名使得在 CI(如 CircleCI)中执行变得容易。
要配合 CircleCI 使用，请使用别名提供的 [CircleCI Orb](https://circleci.com/orbs/registry/orb/k-kinzal/aliases) 。

```
version: "2.1"

orbs:
  aliases: k-kinzal/aliases@0.2.1

jobs:
  aliases:
    machine: true
    steps:
    - checkout
    - aliases/install
    - aliases/gen
    - run: make build

workflows:
  version: 2

  aliases:
    jobs:
    - aliases 
```

请在项目根目录中准备`aliases.yaml`。
然后你可以像本地一样执行相同的命令，而不用编写命令安装。
您不再需要使用`circleci`命令进行多次调试。。
你只需要定义在本地工作的命令。

## 我们需要反馈

化名是新生婴儿。为了更好地成长，请给我们反馈。