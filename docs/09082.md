# 如何使用 Azure Active Directory 访问 Azure Linux 虚拟机

> 原文：<https://dev.to/azure/how-to-access-azure-linux-virtual-machines-with-azure-active-directory-dee>

由 [@nepeters](https://twitter.com/nepeters) 撰写

使用 Azure AD 凭据访问 Azure Linux 虚拟机通过以下方式提高了安全性:

*   集中控制和实施 Azure AD 凭据的访问策略
*   减少对本地访问帐户的依赖
*   与多因素身份认证集成

在这篇博文中，我将快速浏览使用 Azure AD 凭证访问 Azure Linux 虚拟机的基本配置步骤。有关详细步骤和文档，请参见[使用 Azure Active Directory 身份验证登录 Azure 中的 Linux 虚拟机](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/login-using-aad?WT.mc_id=ITOpsTalk-blog-nepeters)。

## 创建虚拟机

首先，你需要一个 Azure Linux 虚拟机。这篇博客使用 Azure CLI 创建虚拟机，但是任何部署虚拟机的方法都可以。如果你已经有一个 Azure Linux 虚拟机，这一节可以跳过。

使用 [az group create](https://docs.microsoft.com/en-us/cli/azure/group?WT.mc_id=ITOpsTalk-blog-nepeters&view=azure-cli-latest#az-group-create) 命令创建一个资源组。

```
az group create --name myResourceGroup --location eastus 
```

使用 [az vm create](https://docs.microsoft.com/cli/azure/vm?WT.mc_id=ITOpsTalk-blog-nepeters&view=azure-cli-latest#az-vm-create) 命令创建一个虚拟机。请注意，我既没有使用`--admin-username`参数来创建本地用户帐户，也没有使用任何参数来创建或提供 SSH 密钥。

```
az vm create --resource-group myResourceGroup --name linuxVM --image UbuntuLTS 
```

这就是奇迹发生的地方。使用 [az vm extension set](https://docs.microsoft.com/cli/azure/vm/extension?WT.mc_id=ITOpsTalk-blog-nepeters&view=azure-cli-latest#az-vm-extension-set) 命令安装 Active Directory Linux SSH 扩展。这个扩展负责 Azure AD 集成的配置。

```
az vm extension set --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH --name AADLoginForLinux --resource-group myResourceGroup --vm-name linuxVM 
```

## 配置基于角色的访问

在使用 Azure AD 帐户登录虚拟机之前，必须配置 Azure AD 访问权限。为此，我们将在 Azure AD 帐户、“虚拟机管理员登录”AD 角色和虚拟机之间创建一个角色绑定。

首先，使用 [az vm show](https://docs.microsoft.com/cli/azure/vm?WT.mc_id=ITOpsTalk-blog-nepeters&view=azure-cli-latest#az-vm-show) 命令获取虚拟机的 ID。在这个例子中，ID 存储在一个名为 **VMID** 的变量中。

```
VMID=$(az vm show --resource-group myResourceGroup --name linuxVM --query id -o tsv) 
```

使用 [az 角色分配创建](https://docs.microsoft.com/en-us/cli/azure/role/assignment?WT.mc_id=ITOpsTalk-blog-nepeters&view=azure-cli-latest#az-role-assignment-create)命令创建角色绑定。请注意，这里的 **-受让人**将是 Azure AD 帐户或组，将为其建立访问权限。

```
az role assignment create --role "Virtual Machine Administrator Login" --assignee user@contoso.com --scope $VMID 
```

## 访问虚拟机

随着虚拟机的创建和访问的建立，您现在可以使用 SSH 访问虚拟机了。首先，获取虚拟机的公共 IP 地址。这可以通过 [az](https://docs.microsoft.com/cli/azure/vm?WT.mc_id=ITOpsTalk-blog-nepeters&view=azure-cli-latest#az-vm-show) vm [show](https://docs.microsoft.com/cli/azure/vm?WT.mc_id=ITOpsTalk-blog-nepeters&view=azure-cli-latest#az-vm-show) 命令来完成。

```
az vm show -d --resource-group myResourceGroup --name linuxVM --query publicIps -o tsv 
```

现在创建 SSH 连接。在这个例子中，我从终端使用 SSH。请注意，Azure AD 用户帐户是在命令中指定的。

```
ssh user@contoso.com @137.117.88.113 
```

完成后，系统会提示您打开浏览器并完成身份验证。按照说明操作，完成后按回车键。

```
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AJ9GDRXBQ to authenticate. Press ENTER when ready. 
```

此时，应该已经成功创建了 SSH 连接。请随时在评论或 Twitter 上联系我们，电话: [@nepeters](https://twitter.com/nepeters) 。