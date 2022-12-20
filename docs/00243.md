# 使用 Az PowerShell 向 Azure 应用服务批量添加 IP 访问限制

> 原文：<https://dev.to/swimburger/bulk-add-ip-access-restrictions-to-azure-app-service-using-az-powershell-1fj6>

默认情况下，Azure 应用服务可以通过 Azure 的公共 DNS 以[App service _ NAME]. Azure websites . net 的格式公开访问，但有许多原因不希望您的应用通过 Azure 的默认 DNS 访问，例如:

*   为了防止搜索引擎索引和关联您的网站内容与错误的域名
*   强制所有流量通过网络应用防火墙(WAF)进行路由
*   您的应用程序服务只需要能够被其他云资源访问，公开访问会带来安全风险

Azure App Service 中有一个名为“访问限制”的功能，它允许你限制谁可以与你的 web 应用程序通信。您可以将服务锁定到 IPv4 范围列表、IPv6 范围或虚拟网络。你可以按照微软的文档使用网络界面来设置这些限制。

不幸的是，无法使用 GUI 批量插入多个 IP 范围，但可以使用 PowerShell 和 Az PowerShell 模块。

## 使用 Az PowerShell 脚本批量插入 IP 访问限制

目前，Az PowerShell 模块中没有与访问限制直接交互的内置函数[，但是通过一点 PowerShell 脚本，您可以构建该功能。以下脚本将:](https://docs.microsoft.com/en-us/azure/app-service/app-service-ip-restrictions#programmatic-manipulation-of-access-restriction-rules)

1.  让您登录 Azure
2.  获取您的应用服务
3.  将新的 IP 限制添加到现有限制中
4.  将更新的应用服务属性发送回 Azure 资源管理器(ARM 将为您更新资源)

```
Param(  [Parameter(Mandatory  =  $true)]  [string]  $ResourceGroupName,  [Parameter(Mandatory  =  $true)]  [string]  $AppServiceName,  [Parameter(Mandatory  =  $true)]  [string]  $SubscriptionId,  [Parameter(Mandatory  =  $true)]  [Hashtable[]]  $NewIpRules  #PSObject has parsing bug in az-module, so we ahve to use hashtable instead  )  $ErrorActionPreference  =  "Stop"  Import-Module  Az  #If logged in, there's an azcontext, so we skip login  if($Null  -eq  (Get-AzContext)){  Login-AzAccount  }  Select-AzSubscription  -SubscriptionId  $SubscriptionId  #grab the latest available api version  $APIVersion  =  ((Get-AzResourceProvider  -ProviderNamespace  Microsoft.Web).ResourceTypes  |  Where-Object  ResourceTypeName  -eq  sites).ApiVersions[0]  $WebAppConfig  =  Get-AzResource  -ResourceName  $AppServiceName  -ResourceType  Microsoft.Web/sites/config  -ResourceGroupName  $ResourceGroupName  -ApiVersion  $APIVersion  foreach  ($NewIpRule  in  $NewIpRules)  {  $WebAppConfig.Properties.ipSecurityRestrictions  +=  $NewIpRule  }  Set-AzResource  -ResourceId  $WebAppConfig.ResourceId  -Properties  $WebAppConfig.Properties  -ApiVersion  $APIVersion 
```

Enter fullscreen mode Exit fullscreen mode

用法:

```
$restrictions  =  @(  @{  ipAddress  =  "173.245.48.0/20";  action  =  "Allow";  priority  =  "100";  name  =  "some ip";  description  =  "some ip";  tag  =  "Default";  },  @{  ipAddress  =  "2400:cb00::/32";  action  =  "Allow";  priority  =  "100";  name  =  "some ip";  description  =  "some ip";  tag  =  "Default";  }  )  .\AddRestrictedIPAzureAppService.ps1  `
  -ResourceGroupName  "YourResourceGroup"  `
  -AppServiceName  "YourAppServiceName"  `
  -SubscriptionId  "YourSubscriptionGuid"  `
  -NewIpRules  $restrictions 
```

Enter fullscreen mode Exit fullscreen mode

*   您需要安装 [Az PowerShell 模块](https://github.com/Azure/azure-powershell)。

运行此脚本后，您传入的 IP 范围将被添加到您的 Azure 应用服务的访问限制中。

如果您在 Azure Application Insights 中使用 Cloudflare 或可用性测试，您将需要包括它们的所有 IP，以便这些服务能够运行。

下面两个帖子包含了脚本抓取和生成 IP 的限制:

*   [使用 Az PowerShell 将 Cloudflare 的 IP 地址批量添加到 Azure 应用服务访问限制中](https://dev.to/blog/azure/bulk-add-cloudflares-ips-to-azure-app-service-access-restrictions-using-az-powershell)
*   [使用 Az PowerShell 向 Azure 应用服务访问限制批量添加应用洞察可用性测试 IP 地址](https://dev.to/blog/azure/bulk-add-application-insights-availability-test-ips-to-azure-app-service-access-restrictions-using-az-powershell)

*附注:脚本使用了哈希表，因为 Az 模块没有正确解析 PSCustomObject。更多信息见[本期 GitHub](https://github.com/Azure/azure-powershell/issues/5099)。*