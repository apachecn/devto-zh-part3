# 面向系统管理员的 Azure 云外壳技巧(bash)

> 原文：<https://dev.to/azure/azure-cloud-shell-tips-for-sysadmins-bash-4h2e>

作为 Azure 的支持者，我花时间做的事情之一就是学习初学者使用微软 Azure 的最简单的方法。今天我想和你分享一些利用 Azure 内置工具的方法。

Azure 云外壳是我使用 Azure 最喜欢的事情之一。我可能不得不从本地计算机运行的许多管理任务现在可以从浏览器中完成，我可以在任何地方验证我的 Azure 帐户。我过去总是担心需要一台有 shell 或终端程序的计算机，并且上面一直有我的 ssh 密钥。现在，我在我的 Azure Cloud Shell 上保留了大部分内容，这样我几乎可以在任何地方工作。

Azure Cloud Shell 可以运行 [bash](https://docs.microsoft.com/azure/cloud-shell/quickstart?WT.mc_id=devto-blog-jagord) 或者 [Powershell](https://docs.microsoft.com/en-us/azure/cloud-shell/quickstart-powershell?WT.mc_id=devto-blog-jagord) 。

1.  从 Azure 门户的顶部导航启动云外壳。

2.  选择订阅以创建存储帐户和 Microsoft Azure 文件共享。

3.  选择“创建存储”

4.  选择 Bash 环境。

太好了。现在，让我们从云外壳打开一个新标签，并获得一个全尺寸的外壳，或者您可以在浏览器中导航到[https://shell.aure.com](https://shell.azure.com)以访问完整视图:

[![](img/56fbc8b250d43dcf43c86ea94ddc4a79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TsND1c37--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v556tb9l4swzfi6043v6.png)

作为一名正在向云迁移的系统管理员，有许多工具会对你有所帮助。

## 超酷的工具！

让我们看看一些可用的 CLI 工具，当您开始使用 Azure 时，它们会让您的生活变得更加轻松。

### 代码

Azure 中有一个简单版本的 VS 代码，允许你在 shell 中创建或编辑文件。运行演示时需要便笺簿吗？这是另一个非常酷的功能。要访问它，只需在 shell 中键入`code`:

[![](img/8632fc6f3acdae33dd9541c329527ff4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9HnHrSAx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c3iuocr1h066gedg6s8h.gif)

### Ansible

Ansible 是一个通过利用基于 YAML 的行动手册来自动化应用和基础设施的工具，这些行动手册已经成为许多系统管理员日常任务的一部分。

Ansible 可以在 Azure Cloud Shell 中使用，允许您立即开始使用您可能在其他地方编写的现有剧本。不需要代理，只需准备好您的 ssh 密钥并开始工作。

你可以在这里观看我关于使用 Ansible 和 Azure 的完整视频:

[https://www.youtube.com/embed/dBvLrjrB82Y](https://www.youtube.com/embed/dBvLrjrB82Y)

### kubectl

如果你打算使用 [Azure Kubernetes 服务](https://azure.microsoft.com/en-us/services/kubernetes-service/?WT.mc_id=devto-blog-jagord)，你将需要熟悉 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) ，它就在你的云 Shell 中，不需要额外安装。`Kubectl`是您将用来操作 AKS 集群的命令行界面。

下面是一个使用`kubectl`获取我构建的 AKS 集群的凭证以及集群节点状态的示例:

[![](img/0310995c8cf78bff5078b6607d666e70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mmOAMxLd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s6z03seb2tjia7k28bki.png)

### az

当然，Azure 将为你提供 CLI 工具来管理你的资源！从同一个基于浏览器的 shell 中，`az`工具是您所需要的一切！[Azure 命令行界面(CLI)](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest?WT.mc_id=devto-blog-jagord) 是微软用于管理 Azure 资源的跨平台命令行体验。Azure CLI 易于学习，是构建使用 Azure 资源的自定义自动化的完美工具。

我强烈推荐查看[快速入门:使用 Azure CLI 部署 Azure Kubernetes 服务(AKS)集群](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough?toc=%2fcli%2fazure%2ftoc.json/?WT.mc_id=devto-blog-jagordd)教程，以获得使用 AKS 和 Azure CLI 的完整演示。

### ssh

在您信任的生产环境中有一个可以随时使用 ssh 的地方是非常重要的。Cloud Shell 允许您提供 ssh 密钥，就像在几乎任何其他“Linux”环境中一样，以便在需要时登录系统。

## 还有其他提示吗？

你还想让我给你提供什么建议吗？我总是希望听听不同经验层次的人想了解什么。请在下方留言评论！

谢谢，并享受使用 Azure！