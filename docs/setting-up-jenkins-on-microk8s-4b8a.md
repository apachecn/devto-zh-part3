# 在 MicroK8s 上设置 Jenkins

> 原文：<https://dev.to/psfeng/setting-up-jenkins-on-microk8s-4b8a>

我最近买了一台家用迷你电脑(MSI Cubi 3 Silent ),我将它用作各种用途的服务器，我想要的是设置 Jenkins 来自动构建和部署我的辅助项目到我的本地 MicroK8s 集群(Ubuntu Server 18.04)。

原来你可以在 kubernetes 上安装 Jenkins，而且多亏了 Kubernetes 插件，Jenkins 代理将运行在专为构建你的任务而创建的 ephimeral pods 中。

如果你知道你必须做什么，它实际上是很容易配置的，但事后看来，这是非常常见的情况，不是吗？我战斗了一整天，这是我的发现，可能需要 30 分钟来设置。

## 先决条件

*   [MicroK8s](https://microk8s.io/) 集群(应该也可以与其他 Kubernetes 集群一起工作)，启用**存储**。
*   主机上运行的 Docker 服务(Kubernetes 之外)
*   在 Kubernetes 安装和部署的[头盔](https://helm.sh)

## 在 Kubernetes 上安装詹金斯

```
# Download Jenkins chart values for customization. I'm linking the version I used, but the newest should work.
$ wget -O jenkins-chart.yml https://github.com/helm/charts/blob/b22774e2f2019c0e5d8182aab6111a84e95fa8ca/stable/jenkins/values.yaml

# Edit chart options to use NodePort instead of LoadBalancer, as MicroK8s doesn't
# ship with any: https://github.com/ubuntu/microk8s/issues/200#issuecomment-441180273
# 
# ServiceType: NodePort
# NodePort: 32000 (optional, if you want to have a fixed one for exposing it externally)
#
# Alternatively, if you have Ingress enabled on MicroK8s you could 
# use ServiceType: ClusterIP and add a rule in your Ingress configuration to
# direct traffic to Jenkins.
$ vim jenkins-chart.yml

$ helm install --name jenkins -f jenkins-chart.yaml stable/jenkins

# Follow the instructions to retrieve the generated password.
# If you need to uninstall Jenkins, then
$ helm del --purge jenkins 
```

此时，应该可以通过 [http://your-server:32000](http://your-server:32000) 访问 Jenkins。然后我安装了蓝海插件，因为我觉得它更美观，而且它是为[詹金斯管道](https://jenkins.io/doc/book/pipeline/)设计的。安装它就像去**管理插件**和勾选**蓝海**框一样简单。您不需要检查其他的，它们会被自动添加为依赖项。

现在我们准备创建我们的第一个管道！

## 创建 Github 管道

假设我们想为 Github 上的私有存储库创建一个管道。

1.  遵循此处的说明[,但跳过最终的管道创建向导，因为我们将使用带有](https://jenkins.io/doc/book/blueocean/creating-pipelines/)[声明管道](https://jenkins.io/doc/book/pipeline/syntax/#declarative-pipeline)语法的 [Jenkinsfile](https://jenkins.io/doc/book/pipeline/jenkinsfile/) 来代替。
2.  在你的 Github repo 中创建一个指向[http://your-server:32000/Github-web hook/](http://your-server:32000/github-webhook/)的 **webhook** (用你的域/端口/https 自定义，并保留尾部斜杠)并验证它正在工作(秘密无关紧要)。理论上这应该由蓝海在第一步中自动完成，但它对我不起作用。请注意，如果您使用的是防火墙或在专用网络中，您必须打开端口和/或将其转发到您的服务器。
3.  在存储库的根目录下创建一个名为 **Jenkinsfile** 的文件。我将假设您已经有了一个 dockerized 应用程序，并且已经很好地配置了 Kubernetes 部署文件。

```
// Jenkinsfile

pipeline {
  agent {
    kubernetes {
      // this label will be the prefix of the generated pod's name
      label 'jenkins-agent-my-app'
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    component: ci
spec:
  containers:
    - name: docker
      image: docker
      command:
        - cat
      tty: true
      volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
    - name: kubectl
      image: lachlanevenson/k8s-kubectl:v1.14.0 # use a version that matches your K8s version
      command:
        - cat
      tty: true
  volumes:
    - name: docker-sock
      hostPath:
        path: /var/run/docker.sock
"""
    }
  }

  stages {
    stage('Build image') {
      steps {
        container('docker') {
          sh "docker build -t your-registry/my-app:latest ."
          sh "docker push your-registry/my-app:latest"
        }
      }
    }

    stage('Deploy') {
      steps {
        container('kubectl') {
          sh "kubectl delete -f ./kubernetes/deployment.yaml"
          sh "kubectl apply -f ./kubernetes/deployment.yaml"
          sh "kubectl apply -f ./kubernetes/service.yaml"
        }
      }
    }
  }
} 
```

提交并将其推送到您的存储库。应该通知 Jenkins，并且应该开始构建！

### Jenkins file 的一些注意事项

*   声明的`docker`和`kubectl`容器使得我们可以在管道中运行这些命令，而无需将它们安装在主机中，这通常是 Kubernetes 之外的 Jenkins 所做的。
*   我们使用主机的 Docker 守护进程，这就是为什么它是先决条件，也是我们用`path: /var/run/docker.sock`定义`docker-sock`的原因。如果你不想让你的主机中有 Docker，你可以试试 Docker 中的 [Docker。](https://medium.com/hootsuite-engineering/building-docker-images-inside-kubernetes-42c6af855f25)
*   我们使用的是[lachlanevenson/k8s-ku bectl](https://github.com/lachie83/k8s-kubectl/blob/v1.14.0/Dockerfile)，这是一个非常简单的图像，只有`kubectl`。您可以使用任何其他带有`kubectl`的图像，但是要确保该图像不会使用其他用户 id 运行，否则您将会遇到 Jenkins 的权限问题。我是从尝试使用 [bitnami/kubectl](https://github.com/bitnami/bitnami-docker-kubectl/blob/ee7364cd186dfcf0bbb18dd0ad6b3e16ddadf564/1.14/debian-9/Dockerfile#L16) 的经历中知道的。
*   你可能想知道为什么我们有`command: cat`和`tty: true`。他们[避免集装箱提前离开](https://github.com/jenkinsci/kubernetes-plugin#constraints)。说实话，我也不知道具体是怎么操作的。
*   `git`没有具体步骤。如果使用 webhooks，当接收到 push 事件时，git 步骤会自动添加。

### 其他感兴趣的事情

*   如果您有子模块，请确保在您的 Jenkins repo 构建配置中，在**高级子模块行为**下检查**递归更新子模块**。
*   当构建被执行时，你可能会注意到“僵尸”进程(见`top`命令)。我不知道为什么会发生这种情况，但它可能是詹金斯或 Docker 中的一个错误。不过，这种情况并不总是发生。

## 最后的话

配置 Jenkins 可能看起来是一项非常艰巨的任务，当然也不是显而易见的。有了 Kubernetes 和 Helm，我相信这要简单得多，但仍然很难找到容易复制的指令。

如果您完全从零开始设置一个没有现有 Kubernetes 集群的 Jenkins 环境，您可能想看看 [Jenkins X](https://jenkins-x.io/) 。否则，我希望这个指南对你有用。

下次见！