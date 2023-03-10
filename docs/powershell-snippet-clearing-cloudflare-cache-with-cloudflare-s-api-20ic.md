# PowerShell 片段:使用 Cloudflare 的 API 清除 Cloudflare 缓存

> 原文：<https://dev.to/swimburger/powershell-snippet-clearing-cloudflare-cache-with-cloudflare-s-api-20ic>

Cloudflare 有许多服务可以帮助加快您的 web 属性、进行全球扩展、保护您的站点等等。Cloudflare 提高网站速度的一种方法是将网站缓存在离访问者更近的地方。默认情况下，HTML 不会被缓存，但是使用 [Cloudflare PageRules](https://support.cloudflare.com/hc/en-us/articles/218411427-Understanding-and-Configuring-Cloudflare-Page-Rules-Page-Rules-Tutorial-) 也可以缓存 HTML。每当您缓存某个东西时，总会有需要刷新、清除或清理该缓存的时候。Cloudflare 提供了一个 GUI 来完成这项工作，但是您可以使用 GUI 执行的每个操作，也可以使用 Cloudflare 的 API 来完成。您可以使用 API 在您选择的 CMS 中更新内容时自动清除缓存，或者在您的连续交付管道中清除缓存。使用 PowerShell，我们将与 Cloudflare 的 API 进行交互，并清除它们的缓存。

## Cloudflare API 键

在能够与 Cloudflare API 交互之前，您需要获取必要的密钥。对于脚本，您需要获取区域 ID 和全局 API 密钥。

在 Cloudflare 中导航到您站点的概览页面，向下滚动以在右下角找到区域 ID。

[![Cloudflare Zone ID Setting](img/c058be83c04a6c951e96d40d2eb30e2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--joeIEbX2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.swimburger.net/media/1113/cloudflare-zone-id.png)

复制您的区域 ID 后，单击“获取您的 API 密钥”,这将带您进入您的个人资料。在您个人资料的底部，您会看到以下部分:

[![Cloudflare API keys section](img/8ed56a93324766a79e28113bf0324e01.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QKVobQlV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.swimburger.net/media/1114/cloudflare-api-keys-section.png)

单击“查看”查看全局 API 密钥，并按照说明获取密钥。记住这个 API 键，您可以对所有 Cloudflare 站点执行所有 Cloudflare 操作。 [Cloudflare 正在将 API 密钥的范围扩大到特定的权限和站点](https://community.cloudflare.com/t/restricted-api-keys/13647/42)，但现在请记住这种**风险**！

有了 API 密匙和区域 ID，你就可以继续与 API 交互了。

## 使用 API 清除 Cloudflare 缓存

**使用下面的脚本，您可以清除您网站的所有 Cloudflare 缓存:**

```
Param(  [parameter(Mandatory=$true)]  [string]  $AdminEmail,  [parameter(Mandatory=$true)]  [string]  $ApiKey,  [parameter(Mandatory=$true)]  [string]  $ZoneId  );  $PurgeCacheUri  =  "https://api.cloudflare.com/client/v4/zones/$ZoneId/purge_cache";  $RequestHeader  =  @{  'X-Auth-Email'  =  $AdminEmail  'X-Auth-Key'  =  $ApiKey  };  $RequestBody  =  '{"purge_everything":true}';  Invoke-RestMethod  `
  -Uri  $PurgeCacheUri  `
  -Method  Delete  `
  -ContentType  "application/json"  `
  -Headers  $requestHeader  `
  -Body  $RequestBody 
```

Enter fullscreen mode Exit fullscreen mode

上面的 PowerShell 脚本执行以下操作:

1.  构造 cache_purge 操作的 API URL，将您的区域 ID 嵌入其中
2.  设置所需的认证头，指定您的管理员电子邮件和 API 密钥
3.  将 HTTP 主体设置为 JSON 字符串，指定清除所有内容
4.  将所有这些放在一起，然后发送一个 HTTP 删除请求

有关更多详细信息，请阅读 [Cloudflare API 文档，了解缓存\清除](https://api.cloudflare.com/#zone-purge-all-files)。

**使用以下脚本清除特定 URL 的缓存:**

```
Param(  [parameter(Mandatory=$true)]  [string[]]  $UrlsToPurge,  [parameter(Mandatory=$true)]  [string]  $AdminEmail,  [parameter(Mandatory=$true)]  [string]  $ApiKey,  [parameter(Mandatory=$true)]  [string]  $ZoneId  );  $PurgeCacheUri  =  "https://api.cloudflare.com/client/v4/zones/$ZoneId/purge_cache";  $RequestHeader  =  @{  'X-Auth-Email'  =  $AdminEmail  'X-Auth-Key'  =  $ApiKey  };  $RequestBody  =  @{  files  =  $UrlsToPurge  }  |  ConvertTo-Json  -Compress;  Invoke-RestMethod  `
  -Uri  $PurgeCacheUri  `
  -Method  Delete  `
  -ContentType  "application/json"  `
  -Headers  $requestHeader  `
  -Body  $RequestBody 
```

Enter fullscreen mode Exit fullscreen mode

这个函数与前面的代码片段做的几乎一样，只是它将构造一个不同的 JSON 有效负载，其中包含传递给它的所有 URL。

有关更多详细信息，请阅读针对通过 URL 进行 cache_purge 的 [Cloudflare API 文档](https://api.cloudflare.com/#zone-purge-files-by-url)。