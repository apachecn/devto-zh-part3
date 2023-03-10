# 为 Terraform 开发配置环境

> 原文：<https://dev.to/documentednerd/configuring-your-environment-for-terraform-development-51dl>

所以我最近用一套开源工具做了很多工作，特别是 [TerraForm](https://www.terraform.io/) 和 [Packer](https://www.packer.io/) 。TerraForm 的核心是一种将真正的基础设施作为代码来实现的方法，它通过提供一种简单的函数风格语言来实现，在这种语言中，您可以为云创建基本的实现，然后利用资源提供者进行部署。这些资源提供商允许你部署到各种各样的云平台(完整列表可以在[这里](https://www.terraform.io/docs/providers/)找到)。它还为调试、定位提供了强大的支持，并支持所需的状态配置方法，这使得在云中维护您的环境变得更加容易。

尽管如此，像大多数开源工具一样，它可能需要对您的本地开发环境进行一些配置，我想把这篇文章放在一起描述它。以下是配置您的环境的步骤。

### 第一步:在你的 Windows 10 机器上安装 Windows 子系统

首先，您需要能够利用 bash 作为 Linux 子系统的一部分。您可以按照本指南中概述的步骤在 Windows 10 计算机上启用此功能:

[https://docs.microsoft.com/en-us/windows/wsl/install-win10](https://docs.microsoft.com/en-us/windows/wsl/install-win10)

一旦您完成了这一步，您将能够继续使用 VS 代码和其他所需的组件。

### 步骤 2:安装 VS 代码和 Terraform 插件

对于本指南，我们推荐使用 VS code 作为您的编辑器，VS Code 可以在各种操作系统上工作，并且是一个非常轻量级的代码编辑器。

您可以从以下链接下载 VS 代码:

[https://code.visualstudio.com/download](https://code.visualstudio.com/download)

一旦你下载并安装了 VS 代码，我们需要为 Terraform 安装 [VS 代码扩展。](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureterraform)

完成后点击“安装”和“重新加载”。这将允许你有智能和支持不同的地形文件类型。

### 第三步:打开终端

然后，您可以从 VS 代码应用程序中执行剩余的步骤。进入“查看”菜单，选择“集成终端”。您会看到终端出现在底部:

默认情况下，终端设置为“powershell”，键入“Bash”切换到 Bash 脚本。您可以按照以下指导来默认您的 shell-[https://code . visual studio . com/docs/editor/integrated-terminal # _ configuration](https://code.visualstudio.com/docs/editor/integrated-terminal#_configuration)

### 步骤 4:在子系统上安装 Unzip

运行以下命令在您的 linux 子系统上安装“unzip ”,这将需要解压缩 terraform 和 packer。

*sudo apt-get 安装解压*

### 步骤 5:安装 TerraForm

您将需要执行以下命令来下载和安装 Terraform，我们需要从获取最新版本的 terraform 开始。

转到此链接:

[https://www.terraform.io/downloads.html](https://www.terraform.io/downloads.html)

并为 TerraForm 复制二进制文件的适当版本的链接。

回到 VS 代码，输入以下命令:`wget {url for terraform}`然后依次运行以下命令:`unzip {terraform.zip file name}` `sudo mv terraform /usr/local/bin/terraform` `rm {terraform.zip file name}`通过键入以下命令确认安装:`terraform --version`

### 步骤 6:安装封隔器

首先，我们需要获得最新版本的 packer。转到以下 url，并复制相应版本的 Url。

[https://www.packer.io/downloads.html](https://www.packer.io/downloads.html)

返回 VS 代码，执行以下命令:

`wget {packer url}`

`unzip {packer.zip file name}`

`sudo mv packer /usr/local/bin/packer`

`rm {packer.zip file name}`

### 步骤 7:安装 Azure CLI 2.0

再次回到 VS 代码，下载/安装 azure CLI。为此，请执行以下步骤和命令:

[https://docs . Microsoft . com/en-us/CLI/azure/install-azure-CLI-apt？view=azure-cli-latest](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

### 步骤 8:针对 Azure 进行身份验证

完成后，你就可以运行 terraform 项目了，但在此之前，你需要对 Azure 进行认证。这可以通过在 bash 终端中运行以下命令来实现(参见下面的链接):

[https://docs . Microsoft . com/en-us/azure/azure-government/documentation-government-get-started-connect-with-CLI](https://docs.microsoft.com/en-us/azure/azure-government/documentation-government-get-started-connect-with-cli)

一旦完成，您将通过 Azure 的认证，并能够更新各种环境的文档。

**注意:您的身份验证令牌将过期，如果您收到有关令牌过期的消息，请输入命令进行刷新:**

`az account get-access-token`

令牌生命周期可以在这里描述-[https://docs . Microsoft . com/en-us/azure/active-directory/develop/active-directory-token-and-claims # access-tokens](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims#access-tokens)

之后，您就可以在本地机器上使用 Terraform 了。