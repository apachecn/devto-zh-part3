# Kubernetes 入门(在家)—第 1 部分

> 原文：<https://dev.to/just_insane/getting-started-with-kubernetes-at-home-part-1-1lal>

当你想到 Kubernetes 时，你可能会想到 AWS 或 GCP，这是一个很好的托管服务，你可以轻松地旋转资源并在其上构建应用程序。这太棒了，老实说，这是体验 Kubernetes 的最佳方式。然而，如果你需要的只是一个实验室，可以在里面乱搞和实验，或者学习新的东西，这可能是非常不划算的。这就是为什么我们打算自己建立 Kubernetes。

在本文中，我们将了解 Kubernetes 的初始部署，从创建节点(在本例中为 CentOS 7 虚拟机)到启动并运行集群。

## 第 1 部分:安装我们的虚拟机

第一步是创建一些虚拟机。我在实验室中使用自定义 vCenter 模板，但如果您没有，可以按照这些简单的步骤操作。

您将需要在至少一台机器上完成这些步骤，但是越多越好，以获得 Kubernetes 的全部好处。

您的机器/虚拟机应该至少有 1 个内核和 3Gb 内存。

1.  从 USB ISO 镜像安装 CentOS 7，基本安装就可以了
2.  创建一个可访问的用户。这个用户应该是 sudo 用户组的一部分，并且最好拥有无密码的 SSH 验证
3.  为您的主机分配静态 IP 地址。可以选择设置主机名。
4.  我讨厌这样说，但官方文件说要禁用节点之间的防火墙，我找不到关于需要哪些端口的文档。
5.  或者，将您的主机添加到 DNS。

## 第 2 部分:永久安装

我们将为我们的集群使用 [Kubespray](https://github.com/kubernetes-sigs/kubespray) ，因为它使得创建和更新 Kubernetes 集群变得非常简单和直接。

你可以在这里找到官方文件。

快速指南如下:

1.  安装依赖项

    `sudo pip install -r requirements.txt`

2.  复制示例库存

    `cp -rfp inventory/sample inventory/mycluster`

3.  构建清单，你可以使用内置的构建器，或者看看[这里的](https://gitlab.com/just.insane/kubernetes/blob/master/src/installation/hosts.ini)为例。有一个主设备是好的，但是`kube-master`和`etcd`部分应该是相同的。

4.  部署集群

    `ansible-playbook -i inventory/mycluster/hosts.yml --become --become-user=root cluster.yml`

然后，您所要做的就是等待 Kubespray 自动部署您的集群。在我的 6 节点集群上，集群完成设置并运行通常需要大约 10–15 分钟。

请注意，在 Kubespray 清单中，有几个选项非常有用。首先，在`addons.yaml`文件中，启用 Helm 和 Kubernetes 仪表板自动部署是一个好主意。如果您对默认的基于令牌的身份验证有问题，那么启用`k8s-cluster.yaml`文件中的`kube_basic_auth`也是有益的。如果您决定以后这样做，您可以简单地进行更改，然后使用上面步骤 4 中的命令重新运行部署。

## 第三部分:测试集群

您可以使用以下命令测试您的集群是否启动并运行:

1.  应该会返回类似这样的内容:

    `Kubernetes master is running at https://10.0.40.245:6444`

2.  `kubectl get nodes`显示所有节点的状态。

你可以在我的 [Gitlab repo](https://gitlab.com/just.insane/kubernetes) 中找到更多关于我如何设置 Kubernetes 的信息，其中有有用的代码片段、完整的配置文件以及扩展的文档。