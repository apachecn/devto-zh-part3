# 在 Terraform 中构建 Azure 容器注册中心

> 原文：<https://dev.to/documentednerd/building-out-azure-container-registry-in-terraform-1k3a>

所以我之前在 TerraForm 模板上发布了一些帖子，我构建这个模板是为了支持创建一个 kubernetes 集群。这背后的意图是为 Azure Government 中的 kubernetes 集群提供一个解决方案。为了了解更多关于这个集群的信息，我在这里发了一篇博文。

现在，我遇到的一个问题是“我们如何将它与 Azure Container Registry 集成？”对于那些不熟悉的人来说，Azure Container Registry 是 Azure 提供的 PaaS 产品，允许您将容器映像推送到 docker registry，而不必管理底层 VM、补丁、更新和其他维护。这允许您只为存储容器图像的空间付费，容器图像确实非常小。

实现这个逻辑的第一部分是使用下面的代码在 TerraForm 中创建容器注册中心。

需要注意的一点是，使用“count”变量是为了确保不会创建这个注册表，除非您创建一个“lkma ”,它是作为主虚拟机运行的虚拟机。

```
resource "azurerm\_container\_registry" "container-registry" { count = "${lookup(var.instance\_counts, "lkma", 0) == 0 ? 0 : 1}" name = "containerRegistry1" resource\_group\_name = "${azurerm\_resource\_group.management.name}" location = "${var.azure\_location}" admin\_enabled = true sku = "Standard" depends\_on = ["azurerm\_role\_assignment.kub-ad-sp-ra-kv1"] } 
```

所以老实说，我对工作没什么要求。对于下一部分，实际上只是添加几行代码来实现注册中心和 kubernetes 集群之间的连接。这些行如下:

```
echo 'Configure ACR registry for Kubernetes cluster' kubectl create secret docker-registry \<SECRET\_NAME\> --docker-server $5 --docker-email $6 --docker-username=$7 --docker-password $8 echo 'Script Completed' 
```

所以真的就这样了。我已经对 GitHub 模板做了这些修改，所以请检查一下。上面几行代码允许我传递给脚本的用户主体信息用于将 azure 容器注册表连接到我的集群。真的就这些了。