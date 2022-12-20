# PowerShell 代码片段:搜索站点地图

> 原文：<https://dev.to/swimburger/powershell-snippet-crawling-a-sitemap-351c>

这里有一个 PowerShell 函数，可以用来验证站点地图中的所有页面是否返回 HTTP 状态代码 200。

你也可以用它来预热你的网站，或者确保你的网站缓存在冷启动后是热的。

```
Function  CrawlSitemap  {  Param(  [parameter(Mandatory=$true)]  [string]  $SiteMapUrl  );  $SiteMapXml  =  Invoke-WebRequest  -Uri  $SiteMapUrl  -UseBasicParsing  -TimeoutSec  180;  $Urls  =  ([xml]$SiteMapXml).urlset.ChildNodes  ForEach  ($Url  in  $Urls){  $Loc  =  $Url.loc;  try{  $result  =  Invoke-WebRequest  -Uri  $Loc  -UseBasicParsing  -TimeoutSec  180;  Write-Host  $result.StatusCode  -  $Loc;  }catch  [System.Net.WebException]  {  Write-Warning  (([int]$_.Exception.Response.StatusCode).ToString()  +  " - "  +  $Loc);  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用如下脚本:

```
CrawlSitemap  -SiteMapUrl  'https://www.swimburger.net/sitemap.xml'; 
```

Enter fullscreen mode Exit fullscreen mode

我个人将它作为我的持续交付渠道的一部分，来预热我的网站和 Cloudflare 的缓存。

希望有用！