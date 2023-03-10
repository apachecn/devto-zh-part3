# 分步指南:使用 Azure Arm 模板的 Azure 命令行界面(CLI)简介

> 原文：<https://dev.to/azure/step-by-step-introduction-to-azure-command-line-interface-cli-with-azure-arm-templates-36ph>

作者: [@jaydestro](https://dev.to/jaydestro)

使用 Azure 资源管理器模板为您提供了一种使用 JSON 来编写基础设施的方法！在本教程中，我们将向您展示如何开始使用不同的参数以及您的模板。

[来自 Azure 资源管理器概述:](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview/?WT.mc_id=armapache-youtube-jagord)

> Azure 资源管理器是 Azure 的部署和管理服务。它提供了一致的管理层，使您能够创建、更新和删除 Azure 订阅中的资源。部署后，您可以使用其访问控制、审计和标记功能来保护和组织您的资源。
> 
> 当您通过门户、PowerShell、Azure CLI、REST APIs 或客户端 SDK 采取行动时，Azure 资源管理器 API 会处理您的请求。因为所有请求都是通过相同的 API 处理的，所以您可以在所有不同的工具中看到一致的结果和功能。门户中可用的所有功能也可以通过 PowerShell、Azure CLI、REST APIs 和客户端 SDK 获得。最初通过 API 发布的功能将在最初发布后的 180 天内出现在门户中。
> 
> 下图显示了所有工具如何与 Azure 资源管理器 API 交互。API 将请求传递给资源管理器服务，资源管理器服务对请求进行身份验证和授权。然后，资源管理器将请求路由到适当的服务。

有了资源管理器，你可以创建一个模板(JSON 格式)来定义你的 Azure 解决方案的基础设施和配置。通过使用模板，您可以在解决方案的整个生命周期中重复部署您的解决方案，并确信您的资源以一致的状态部署。

要学习本教程，建议您:

*   Azure 帐户
*   一些 Bash shell 体验
*   对脚本/编程的一般理解

您需要具备的重要链接:

[Azure 资源管理器模板的参数部分](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-templates-parameters?WT.mc_id=armapache-youtube-jagord)

[使用资源管理器模板和 Azure CLI 部署资源](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy-cli/?WT.mc_id=armapache-youtube-jagord)

[Ubuntu-Apache-test-page GitHub Repo](https://github.com/Azure/azure-quickstart-templates/tree/master/ubuntu-apache-test-page)

要创建资源组，请执行以下操作:

```
az group create -l centralus -n \<insert your preferred name here\> 
```

ARM 模板的基本部署:

```
az group deployment create --name \<your-deploy-name\> --resource-group \<your resource group name\> --template-file azuredeploy.json 
```

使用指定的参数部署:

```
az group deployment create --name \<your deployment group\> --resource-group=\<your resource group name\> --template-file azuredeploy.json --parameter location=eastus testPageTitle="demo jay" testPageBody="\<p\>This is a really cool test page.\</p\>" adminUsername=\<your preferred username\> adminPassword=\<your preferred password\> dnsNameForPublicIP=\<your preferred hostname\> 
```

对本教程有其他问题吗？欢迎在下方留言。