# 使用 Az PowerShell 向 Azure 应用服务访问限制批量添加应用洞察可用性测试 IP

> 原文：<https://dev.to/swimburger/bulk-add-application-insights-availability-test-ips-to-azure-app-service-access-restrictions-using-az-powershell-3if4>

Azure App Service 中有一个名为“访问限制”的功能，它允许你限制谁可以与你的 web 应用程序通信。您可以将服务锁定到 IPv4 范围列表、IPv6 范围或虚拟网络。你可以按照微软的文档使用 web 界面设置这些限制。

如果你正在使用 Azure Application Insights **针对你的应用服务内部 DNS 和启用的访问限制进行可用性测试**，那些可用性测试将停止工作。当你使用访问限制时，它也会限制对所有 Azure 服务的访问，包括可用性测试。

你可以从 docs.microsoft.com 找到所有的 [IP 范围，并手动将所有 300+ IP 范围添加到访问限制中。或者，您可以节省几个小时的时间，使用 PowerShell 来为您完成这项工作。](https://docs.microsoft.com/en-us/azure/azure-monitor/app/ip-addresses#availability-tests)

## 批量插入可用性测试 IP 范围脚本

在[之前的帖子](https://dev.to/blog/azure/bulk-add-ip-access-restrictions-to-azure-app-service-using-az-powershell)中，我分享了一个脚本，你可以使用 PowerShell 将 IP 访问限制批量插入到你的 Azure 应用服务中。在本文中，我将基于该脚本，添加插入所有可用性测试 IP 范围所需的功能。以下脚本将:

1.  读取“AvailabilityTestIps.txt”文件，并将其分成几行
2.  确定该行是否为标题。
    *   如果是 header，将其存储为当前组并跳到下一行。
    *   如果为空，下一行将是标题，因此$ IsHeader 被设置为$True 并跳到下一行。
    *   否则继续 3
3.  确定该行是否包含“/”
    *   如果包含"/"，则使用该行作为 IP 范围
    *   否则，添加“/32”将其转换为有效的 IP 范围
4.  使用当前组和 IP 范围按照访问限制格式创建新的哈希表
5.  将哈希表添加到访问限制列表中
6.  将所有访问限制传递给“addrestrictedipazureappservice . PS1”脚本。

```
Param(  [Parameter(Mandatory  =  $true)]  [string]  $ResourceGroupName,  [Parameter(Mandatory  =  $true)]  [string]  $AppServiceName,  [Parameter(Mandatory  =  $true)]  [string]  $SubscriptionId,  [Parameter(Mandatory  =  $true)]  [string]  $RulePriority  )  $ErrorActionPreference  =  "Stop"  $AvailabilityTestIpsFile  =  Get-Content  "$PSScriptRoot/AvailabilityTestIps.txt"  $AvailabilityTestIpsLines  =  $AvailabilityTestIpsFile.Split([Environment]::NewLine)  $IsHeader  =  $True  $CurrentGroup  =  $Null;  $NewIpRestrictions  =  @();  ForEach($Line  in  $AvailabilityTestIpsLines){  if($IsHeader){  $CurrentGroup  =  $Line;  $IsHeader  =  $False  continue  }  if([System.String]::IsNullOrEmpty($Line)){  $IsHeader  =  $True  #next line will be header  continue  }  $Ip  =  $Null  if($Line.Contains("/")){  $Ip  =  $Line;  }else{  $Ip  =  "$Line/32";  }  $NewIpRestrictions  +=  @{  ipAddress  =  $Ip;  action  =  "Allow";  priority  =  $RulePriority;  name  =  "Av IP $CurrentGroup";  description  =  "Availability Test IP $CurrentGroup";  tag  =  "Default";  }  }  &  "$PSScriptRoot\AddRestrictedIPAzureAppService.ps1"  `  -ResourceGroupName  $ResourceGroupName  `  -AppServiceName  $AppServiceName  `  -SubscriptionId  $SubscriptionId  `  -NewIpRules  $NewIpRestrictions 
```

Enter fullscreen mode Exit fullscreen mode

用法:

```
.\AddAvailabilityRestrictedIPApp.ps1  `
  -ResourceGroupName  "YourResourceGroup"  `
  -AppServiceName  "YourAppServiceName"  `
  -SubscriptionId  "YourSubscriptionGuid"  `
  -RulePriority  "100" 
```

Enter fullscreen mode Exit fullscreen mode

*   您需要将两个脚本都保存到磁盘上，并确保它们在同一个文件夹中。从 GitHub gist 下载脚本。
*   您需要将[“availabilitytestips . txt”文件](https://gist.githubusercontent.com/Swimburger/b2d58bff38156b73a5417b7f818fc5be/raw/f7725989ea89bd8ad3e12316854ed2ccc6619225/AvailabilityTestIps.txt)保存到同一个文件夹，并用来自 docs.microsoft.com 的 [IP 进行更新。](https://docs.microsoft.com/en-us/azure/azure-monitor/app/ip-addresses#availability-tests)
*   您需要安装 [Az PowerShell 模块](https://github.com/Azure/azure-powershell)。
*   如果您已经有访问限制，则规则优先级很重要，它将决定针对您的服务提出请求时的评估顺序。

我希望这个脚本为您节省了一些时间！