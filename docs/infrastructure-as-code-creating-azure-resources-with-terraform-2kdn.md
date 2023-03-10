# 基础设施即代码——用 Terraform 创建 Azure 资源

> 原文：<https://dev.to/funkysi1701/infrastructure-as-code-creating-azure-resources-with-terraform-2kdn>

Terraform 是一种编写 Azure 资源创建脚本的方式。通过编写脚本，创建您的资源，您可以重复运行它们，并确保一切配置与脚本指定的完全相同。不要忘记勾选复选框，如果它在您的脚本中，那么它将使用完全相同的配置进行部署。

执行 terraform 脚本的过程遵循以下基本流程。

**terraform init**–初始化 terraform 目录

**terraform 计划**–生成并显示将要执行的内容

**terra form apply**–将 terra form 脚本应用到您的 Azure 订阅

**terra form destroy**–删除您在上一步中创建的 Azure 资源

让我们看看如何从构建或发布管道中执行 terraform 脚本。

我正在从我的发布管道中执行我的 terraform 脚本，由于我不想将二进制文件签入我的源代码，我将添加一些管道来将东西放在正确的位置。terraform 二进制可以从[https://www.terraform.io/downloads.html](https://www.terraform.io/downloads.html)获得。

首先，我执行一个 Powershell 脚本来下载 terraform 二进制文件，这是一个 zip 文件，所以我需要下载并解压缩它。

```
$url  =  "https://releases.hashicorp.com/terraform/0.11.13/terraform_0.11.13_windows_amd64.zip"  $output  =  "terraform.zip"  (New-Object  System.Net.WebClient).DownloadFile($url,  $output)  Add-Type  -AssemblyName  System.IO.Compression.FileSystem  [System.IO.Compression.ZipFile]::ExtractToDirectory($output,  Get-Location) 
```

Enter fullscreen mode Exit fullscreen mode

现在我有了 terraform 二进制文件，我可以运行 Azure Cli 并执行上面的 terraform 命令。

现在是时候看看地形脚本了。这个特定的脚本创建了一个资源组、一个应用服务计划和一个 webapp。

```
# Configure the Microsoft Azure Provider
provider "azurerm" {
  subscription_id = ""
  client_id       = ""
  client_secret   = ""
  tenant_id       = ""
}

resource "azurerm_resource_group" "rg" {
        name = "resource-group"
        location = "northeurope"
}

resource "azurerm_app_service_plan" "plan" {
  name                = "planname"
  location            = "${azurerm_resource_group.rg.location}"
  resource_group_name = "${azurerm_resource_group.rg.name}"

  sku {
    tier = "Standard"
    size = "S1"
  }
}

resource "azurerm_app_service" "webapp" {
  name                = "example-app-service"
  location            = "${azurerm_resource_group.rg.location}"
  resource_group_name = "${azurerm_resource_group.rg.name}"
  app_service_plan_id = "${azurerm_app_service_plan.plan.id}"
} 
```

Enter fullscreen mode Exit fullscreen mode

您需要从 azure 订阅中填写四个 guidss，要找到这些 guid，您需要从 Azure Cli 运行几个命令。查看

[https://docs . Microsoft . com/en-us/azure/virtual-machines/Linux/terraform-install-configure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/terraform-install-configure)了解关于这些命令的更多详细信息。

安装 Azure Cli 后，从 Azure Cloud Shell 或 Powershell 命令提示符运行这些程序。

az 登录–要登录 azure，将会打开一个浏览器窗口。

az 帐户显示–查询" {subscriptionId:Id，tenantId:tenantId}"

这将显示您的订阅 id。

az account set–SUBSCRIPTION = " ${SUBSCRIPTION_ID} "

其中$ { SUBSCRIPTION _ ID }是您在上一步中找到的订阅 ID。

az ad sp create-for-RBAC–role = " Contributor "–scopes = "/subscriptions/＄{ SUBSCRIPTION _ ID } "

其中＄{ SUBSCRIPTION _ ID }是您在上一步中找到的订阅 ID，并假定您拥有订阅权限。

这最后一个命令是关于一系列 Guids 的，您需要在您已经拥有的 terraform 文件 appId = client_id，password = client_secret，tenant = tenant_id 和 subscription_id 的顶部填写这些 Guids。

这只是让您体验一下使用 terraform 可以做些什么。查看 Azure Terraform 文档，你可以创建许多不同的资源，甚至还有一个包含一些样本的 T2 github repo。