# 将 PowerShell 与 Azure Cloud Shell 结合使用

> 原文：<https://dev.to/azure/using-powershell-with-azure-cloud-shell-4iio>

*我们已经选出了我们最喜欢的由[迈克尔·克伦普](https://twitter.com/mbcrump)创造的技巧和窍门，以及计划整个四月在 Azure 上发布的新技术内容！错过一天(或更多)？[赶上系列](https://dev.to/t/azureapril/latest)。*

**没有天蓝色？** [抢免费订阅](https://azure.microsoft.com/en-us/free?&WT.mc_id=azureapril_devto-blog-cxa)。

Azure 云壳包括两种命令行语言供你使用:PowerShell 和 Bash。传统上，由于 Azure CLI 中丰富的工具集，Bash 一直是 Cloud Shell 中的首选方法。随着 Azure PowerShell 工具的更新，这两个选项之间的功能对等，所以让我们看看 Azure Cloud Shell 中的 PowerShell。

### 在 Azure 云壳中访问 PowerShell

要在 Azure Cloud Shell 中访问 PowerShell，您有几个选择。

1.  你可以在 shell 中输入`pwsh`,这样你就可以通过 PowerShell 的认证进入一个 Cloud Shell 实例，而不用离开当前的 Cloud Shell 会话。
    [![Switching from Bash to PowerShell in Cloud Shell](img/d97cb3a60a629a6424cd409b4d5d3b3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EwN9qeJD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wzvhjas5qdj7q705w0ws.png)

2.  另一个选项是通过从下拉菜单
    [![Choosing PowerShell from Dropdown](img/cb2fa91f7a0cbf3388fbf3ecf0dd847c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9MSqmiR9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j3te50b9hnz9ngewrf4u.png) 中选择 **PowerShell** 来重新启动云壳窗口

并选择“确认”重新启动。这将使用 PowerShell 将您的会话重新连接到当前容器。
[![Restart Session with PowerShell](img/3fa21bcc1c862ca93d8bbdc7a244cdb9.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--eeVKHoaO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e8fdkkoal2pyf2lzqpka.png)

一旦通过身份验证，你就可以使用 PowerShell 探索 Azure 中可用的资源。

要验证您正在运行的 PowerShell 版本，请键入`$PSVersionTable`。

```
PS  Azure:\>  $PSVersionTable  Name  Value  ----  -----  PSVersion  6.2.0  PSEdition  Core  GitCommitId  6.2.0  OS  Linux  4.15.0-1041-azure  #45-Ubuntu SMP Fri Mar 15 14:41:00 UTC 2019  Platform  Unix  PSCompatibleVersions  {1.0,  2.0,  3.0,  4.0…}  PSRemotingProtocolVersion  2.3  SerializationVersion  1.1.0.1  WSManStackVersion  3.0 
```

请注意，云 Shell 实例的操作系统是 Linux。这是因为 cloud shell 在 Linux 容器内部使用 PowerShell 核心。正如您在所有已安装的模块中看到的那样，您可以在您的机器上使用许多与 PowerShell Core 相同的工具。

```
 PS  Azure:\>  Get-Module  ModuleType  Version  Name  ExportedCommands----------  -------  ----  ----------------Script  1.5.1  Az.Accounts  {Add-AzEnvironment,  Clear-AzContext,  Clear-AzDefault,  Connect-AzAccount…}Script  1.8.0  Az.Compute  {Add-AzContainerServiceAgentPoolProfile,  Add-AzImageDataDisk,  Add-AzVhd,  Add-AzVMAdditi…  Script  1.7.0  Az.Network  {Add-AzApplicationGatewayAuthenticationCertificate,  Add-AzApplicationGatewayBackendAddr…  Script  1.3.1  Az.Resources  {Add-AzADGroupMember,  Export-AzResourceGroup,  Get-AzADAppCredential,  Get-AzADApplicatio…  Script  1.2.0  Az.Storage  {Add-AzRmStorageContainerLegalHold,  Add-AzStorageAccountManagementPolicyAction,  Add-AzS…  Script  0.0.0.9  AzureAD.Standard.Preview  {Add-AzureADAdministrativeUnitMember,  Add-AzureADApplicationOwner,  Add-AzureADApplicati…  Script  0.9.3  AzurePSDrive  Manifest  6.1.0.0  Microsoft.PowerShell.Management  {Add-Content,  Clear-Content,  Clear-Item,  Clear-ItemProperty…}  Manifest  6.1.0.0  Microsoft.PowerShell.Utility  {Add-Member,  Add-Type,  Clear-Variable,  Compare-Object…}  Script  0.9.3  PSCloudShellUtility  {Disable-AzVMPSRemoting,  Dismount-CloudDrive,  Enable-AzVMPSRemoting,  Enter-AzVM…}  Script  2.0.0  PSReadLine  {Get-PSReadLineKeyHandler,  Get-PSReadLineOption,  Remove-PSReadLineKeyHandler,  Set-PSRea…  Binary  0.8.1  SHiPS 
```

此外，你有像 Azure PowerShell 模块这样的内置工具来管理 Azure 中的资源。

要查看所有 Azure PowerShell 命令，请键入`Get-Command -Module AZ* | more`

```
PS  Azure:\>  Get-Command  -Module  AZ*  |  more  CommandType  Name  Version  Source  -----------  ----  -------  ------  Alias  Add-AdlAnalyticsDataSource  1.0.0  Az.DataLakeAnalytics  Alias  Add-AdlAnalyticsFirewallRule  1.0.0  Az.DataLakeAnalytics  Alias  Add-AdlStoreFirewallRule  1.2.0  Az.DataLakeStore 
```

这样做的好处是，你不用担心更新这些工具；都为你做好了。

### [T1】探索 Azure Drive](#exploring-azure-drive)

当你登录云壳时，你将可以访问 Azure Drive。Azure Drive 为您提供了对所有租户资源的类似文件系统的访问。它通过基于 SHiPS(PowerShell 中的简单层次结构)构建 PowerShell 驱动器来实现这一点。所以你可以做一个目录列表，看看你所有的订阅如下:

```
Azure:/  PS  Azure:\>  dir  Directory:  Azure:  Mode  SubscriptionName  SubscriptionId  TenantId  State  ----  ----------------  --------------  --------  -----  +  ca-mibender-demo-test  xxxx-xxxx-xxxx-xxxx-xxxx  xxxx-xxxx-xxxx-xxxx-xxxx  Enabled 
```

然后使用 cd
深入目录查看部署在 Azure 中的资源，就像虚拟机一样

```
 Azure:/  PS  Azure:\>  cd  ./ca-mibender-demo-test/VirtualMachines/  Azure:/ca-mibender-demo-test/VirtualMachines  PS  Azure:\>  dir  Directory:  Azure:/ca-mibender-demo-test/VirtualMachinesName  ResourceGroupName  Location  VmSize  OsType  NIC  ProvisioningState  PowerState  ----  -----------------  --------  ------  ------  ---  -----------------  ----------  myVM  CLOUDSHELL-DEMO-MICHAEL  westeurope  Standard_A1  Linux  myVM-nic  Succeeded  running  vm-linux-01  AZURE-CLOUDSHELL-DEMO  westus2  Standard_DS1_v2  Linux  vm-linux-01336  Succeeded  deallocated  vm-linux-02  AZURE-CLOUDSHELL-DEMO  westus2  Standard_DS1_v2  Linux  vm-linux-02181  Succeeded  running  vm-win-01  AZURE-CLOUDSHELL-DEMO  westus2  Standard_DS1_v2  Windows  vm-win-01543  Succeeded  running  vm-win-02  AZURE-CLOUDSHELL-DEMO  westus2  Standard_DS1_v2  Windows  vm-win-02953  Succeeded  running 
```

现在，如果您想查看一个虚拟机的属性，比如说 vm-linux-01，您可以对那个
使用`Get-AzVm`命令

```
PS  Azure:\>  get-AzVm  -Name  vm-linux-01  ResourceGroupName  Name  Location  VmSize  OsType  NIC  ProvisioningState  Zone  -----------------  ----  --------  ------  ------  ---  -----------------  ----  AZURE-CLOUDSHELL-DEMO  vm-linux-01  westus2  Standard_DS1_v2  Linux  vm-linux-01336  Succeeded 
```

### 访问 Azure 云驱动

云壳的另一个特点是 CloudDrive。CloudDrive 是一个 5GB 的 Azure 文件共享，为您的云 Shell 实例提供[持久存储。这允许您存储脚本、安装工具和其他文件管理任务，并且在每次访问 Cloud Shell 时都可以使用所有这些。](https://docs.microsoft.com/en-us/azure/cloud-shell/persisting-shell-storage?WT.mc_id=azureapril_devto-blog-cxa)

要访问您的云驱动器，请键入`cd $Home`以访问云驱动器所在的主驱动器。然后键入`cd ./CloudDrive`进入您的持久 CloudDrive 存储。

```
PS  Azure:\>  cd  $home  PS  /home/michael>  dir  Directory:  /home/michael  Mode  LastWriteTime  Length  Name  ----  -------------  ------  ----  d----l  4/25/19  1:24  PM  clouddrive  PS  /home/michael>  cd  ./clouddrive/  PS  /home/michael/clouddrive>  dir  Directory:  /home/michael/clouddrive  Mode  LastWriteTime  Length  Name  ----  -------------  ------  ----  d-----  4/15/19  2:13  PM  DemoDirectory  d-----  4/11/19  9:50  PM  scripts  PS  /home/michael/clouddrive> 
```

如果您仍然不喜欢使用 PowerShell，您仍然可以在 PowerShell 控制台中运行 Bash 和 Azure CLI 命令。两者都支持！

```
 PS  Azure:\>  az  group  create  --name  myResourceGroup  --location  eastus  {  "id":  "/subscriptions/XXXX-XXXX-XXXX-XXXX/resourceGroups/myResourceGroup",  "location":  "eastus",  "managedBy":  null,  "name":  "myResourceGroup",  "properties":  {  "provisioningState":  "Succeeded"  },  "tags":  null,  "type":  null  } 
```

这将让你开始在 Azure Cloud Shell 中使用 PowerShell！

想要 Azure 云壳中更多的 PowerShell？查看我们的[快速入门和教程](https://docs.microsoft.com/en-us/azure/cloud-shell/quickstart-powershell?WT.mc_id=azureapril_devto-blog-cxa)！

* * *

我们将在 4 月份每天发布文章，所以请继续关注或提前查看更多提示和技巧[现在](http://azuredev.tips)。