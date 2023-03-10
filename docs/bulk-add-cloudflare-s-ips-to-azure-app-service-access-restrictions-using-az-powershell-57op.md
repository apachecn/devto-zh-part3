# 使用 Az PowerShell 将 Cloudflare 的 IPs 批量添加到 Azure 应用服务访问限制中

> 原文：<https://dev.to/swimburger/bulk-add-cloudflare-s-ips-to-azure-app-service-access-restrictions-using-az-powershell-57op>

在您的网站上启用 Cloudflare 时，Cloudflare 充当缓存代理和 web 应用防火墙(WAF)。默认情况下，Azure 的应用服务使用以下格式公开发布[App service _ NAME]. Azure websites . net。如果您通过该域名公开发布您的应用服务，则可以通过 Azure 的默认 DNS 攻击您的服务来轻松绕过 Cloudflare 的安全性。

您可以通过使用 Azure App Service 的访问限制功能来阻止对您的服务的访问，该功能允许您将其锁定到 IPv4 范围、IPv6 范围或特定虚拟网络的列表。这将防止攻击者直接访问您的服务，但也会阻止 Cloudflare。要解决这个问题，您需要添加 Cloudflare 的 IP 范围。

Cloudflare 托管 2 个包含所有 IP 范围的文本文件: [IPv4 的](https://www.cloudflare.com/ips-v4) & [IPv6 的](https://www.cloudflare.com/ips-v6)。这个列表相当长，如果你想使用 Azure 的 GUI 手动插入这些 IP 范围，将需要几分钟时间。手动添加单个 IP 范围时，除了来自该 IP 范围的流量，所有流量都会被立即阻止。因此，手动插入范围的时间越长，Cloudflare 阻止的合法流量就越多。出于这个原因和一般的懒惰，你应该使用一个脚本来做这件事。

## 批量插入 Cloudflare 的 IP 范围脚本

在[之前的帖子](https://dev.to/blog/azure/bulk-add-ip-access-restrictions-to-azure-app-service-using-az-powershell)中，我分享了一个脚本，你可以使用 PowerShell 将 IP 访问限制批量插入到你的 Azure 应用服务中。在本文中，我将基于该脚本，添加插入所有 cloud flare IP 范围所需的功能。以下脚本将:

1.  获取 Cloudflare 的 IP 范围文本文件
2.  将文本文件解析成有效的 IP 访问限制哈希表
3.  将 IP 范围哈希表传递给批量插入脚本

```
Param(  [Parameter(Mandatory  =  $true)]  [string]  $ResourceGroupName,  [Parameter(Mandatory  =  $true)]  [string]  $AppServiceName,  [Parameter(Mandatory  =  $true)]  [string]  $SubscriptionId,  [Parameter(Mandatory  =  $true)]  [string]  $RulePriority  )  $ErrorActionPreference  =  "Stop"  $IPv4s  =  (Invoke-WebRequest  -Uri  "https://www.cloudflare.com/ips-v4").Content.TrimEnd([Environment]::NewLine).Split([Environment]::NewLine);  $IPv6s  =  (Invoke-WebRequest  -Uri  "https://www.cloudflare.com/ips-v6").Content.TrimEnd([Environment]::NewLine).Split([Environment]::NewLine);  $NewIpRestrictions  =  @();  foreach($IPv4  in  $IPv4s){  $NewIpRestrictions  +=  @{  ipAddress  =  $IPv4;  action  =  "Allow";  priority  =  $RulePriority;  name  =  "Cloudflare IPv4";  description  =  "Cloudflare IPv4";  tag  =  "Default";  }  }  foreach($IPv6  in  $IPv6s){  $NewIpRestrictions  +=  @{  ipAddress  =  $IPv6;  action  =  "Allow";  priority  =  $RulePriority;  name  =  "Cloudflare IPv6";  description  =  "Cloudflare IPv6";  tag  =  "Default";  }  }  &  "$PSScriptRoot\AddRestrictedIPAzureAppService.ps1"  -ResourceGroupName  $ResourceGroupName  -AppServiceName  $AppServiceName  -SubscriptionId  $SubscriptionId  -NewIpRules  $NewIpRestrictions 
```

Enter fullscreen mode Exit fullscreen mode

用法:

```
.\AddCloudflareRestrictedIPApp.ps1 `
    -ResourceGroupName "YourResourceGroup" `
    -AppServiceName "YourAppServiceName" `
    -SubscriptionId "YourSubscriptionGuid" `
    -RulePriority "100" 
```

Enter fullscreen mode Exit fullscreen mode

*   您需要将两个脚本都保存到磁盘上，并确保它们在同一个文件夹中。从 GitHub gist 下载脚本。
*   您需要安装 [Az PowerShell 模块](https://github.com/Azure/azure-powershell)。
*   如果您已经有访问限制，则规则优先级很重要，它将决定针对您的服务提出请求时的评估顺序。

我希望这个脚本为您节省了一些时间！