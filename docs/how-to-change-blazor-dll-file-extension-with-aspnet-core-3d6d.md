# 如何用 ASPNET 核心更改 Blazor dll 文件扩展名

> 原文：<https://dev.to/rembou1/how-to-change-blazor-dll-file-extension-with-aspnet-core-3d6d>

Blazor 是一个执行库(dll)定位。net 标准，在浏览器中。当应用程序加载时，它通过 xhr 获取你的应用程序二进制文件。问题是，在一些内网或者对于一些 AV 提供商来说，下载。dll 文件是禁止的。我们可以在这期 github 中看到，这是一些人的问题:[https://github.com/aspnet/Blazor/issues/172](https://github.com/aspnet/Blazor/issues/172)。虽然我认为 ASPNET 团队会在第一次发布之前解决这个问题(如果有的话)，但在这篇博客文章中，我将向您展示如何使用 ASPNET Core 中已经存在的现有工具来解决这个问题。

## 会发生什么

您的 blazor 索引页面如下所示

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    TOSS

    <base href="/" />
</head>
<body>
    <div class="container">
        <app>
            <div class="jumbotron p-2 m-2">
                <p class="lead">Loading all the things ....</p>
            </div>
        </app>
    </div>
    <script type="blazor-boot">
    </script>

    <script src="_framework/blazor.webassembly.js"></script>

</body>
</html> 
```

*   标签中的一切都是为了闪屏
*   加载 blazor.webassembly.js 时
    *   它下载了一个名为“blazor.boot.json”的文件，其中包含了你的应用程序需要的所有文件
    *   它下载 mono wasms 运行时
    *   它从以前的 json 下载您的项目 dll 和依赖项 dll
    *   并把所有的东西连接在一起(到目前为止，这对我来说仍然很神奇，但我可能会在未来的博客文章中挖掘它)

下面是我的项目 Toss 的 blazor.boot.json:

```
{  "main":"Toss.Client.dll",  "entryPoint":"Toss.Client.Program::Main",  "assemblyReferences":[  "Markdig.dll",  "MediatR.dll",  "MediatR.Extensions.Microsoft.DependencyInjection.dll",  "Microsoft.AspNetCore.Blazor.Browser.dll",  "Microsoft.AspNetCore.Blazor.dll",  "Microsoft.AspNetCore.Blazor.TagHelperWorkaround.dll",  "Microsoft.Extensions.DependencyInjection.Abstractions.dll",  "Microsoft.Extensions.DependencyInjection.dll",  "Microsoft.JSInterop.dll",  "Mono.WebAssembly.Interop.dll",  "mscorlib.dll",  "System.ComponentModel.Annotations.dll",  "System.Core.dll",  "System.dll",  "System.Net.Http.dll",  "Toss.Shared.dll",  "Markdig.pdb",  "Toss.Client.pdb",  "Toss.Shared.pdb"  ],  "cssReferences":[  ],  "jsReferences":[  ],  "linkerEnabled":true  } 
```

这是我的应用程序启动时网络标签的截图

[![Toss startup network tab](img/bc430969fd400e97e589b54a4c20dae8.png "Toss startup network tab")](/assets/img/Capture.PNG)

我们需要在步骤 2 和 3 中完成:

*   在客户端，当我们请求一个。dll“将扩展名更改为”。布拉佐尔”
*   在服务器端，当。blazor“文件被请求将其更改为”。因此它与物理文件名匹配

## 更改呼入请求

下面是更改传入请求的代码

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
  var options = new RewriteOptions()
      .AddRewrite("^_framework/_bin/(.*)\\.blazor", "_framework/_bin/$1.dll",
  skipRemainingRules: true);

  app.UseRewriter(options);
} 
```

*   我使用现有的内置于 ASPNET 核心的重写引擎，但是你也可以使用来自你的 web 服务器的引擎:nginx，apache，IIS...
*   正则表达式非常简单，你可以在这里测试你的[http://regexstorm.net/tester](http://regexstorm.net/tester)
*   您的重写器声明必须是 Configure 中的第一个声明
*   为了测试，我只是提取了 powershell 以从 chrome dev tools 获取第一个请求，并更改了扩展:

```
 Invoke-WebRequest -Uri "http://localhost:52386/_framework/_bin/Toss.Client.blazor" -Headers @{"Pragma"="no-cache"; "DNT"="1"; "Accept-Encoding"="gzip, deflate, br"; "Ac
cept-Language"="fr-FR,fr;q=0.9,en;q=0.8,en-US;q=0.7"; "User-Agent"="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.77 Safari/537.36"; "Accept
"="*/*"; "Referer"="http://localhost:52386/"; "Cookie"=".AspNetCore.Antiforgery.MCOkDYqzrsU=CfDJ8JBC_YjSTnhOnptilfRxtJO-G3t3ZxYDD7hdgqQ7f50T9bQrKR_6T-0OZo46WxGYxiznVoHxYXGL-sQWWtJ4hMy5tL1-nbji
iUH29wZPn5x80cwesCTDGMCRIDmn5f97H8eBN2u_lEXcesNX-ZIMyEI; CSRF-TOKEN=CfDJ8JBC_YjSTnhOnptilfRxtJNwmzsFKwbNtGqSAajSqxrwyM6vFT15lgY7EJ6KiQjjTs850EXHXiF-LUHdnFqiK6SBgHV2yQuFC05r2RMGlPcUjjH9x3xAJEkx
2QEp0Bn9eS9t9SxFTW5JCRI2GCv1u_Y"; "Cache-Control"="no-cache"} 
```

我用好的 HTTP 请求头得到了 200 分:)

## 在客户端改变

现在我需要在客户端更改文件名，这样 XHR 的“myassembly.dll”就变成了“myasembly.blazor”。我需要这样做，所以它是更离散的可能，因为”。dll”必须通过大多数 mono wasm 和 blazor 代码库进行硬编码。我试图通过中间件更改 blazor.boot.json 中的扩展，但我的应用程序无法启动。所以我选择在 XHR 级别这样做，在 blazor.webassembly.js ( [源代码](https://github.com/aspnet/Blazor/blob/master/src/Microsoft.AspNetCore.Blazor.Browser.JS/src/Platform/Mono/MonoPlatform.ts))中重写 Blazor 使用的方法。

```
 XMLHttpRequest.prototype.open_before = XMLHttpRequest.prototype.open;

XMLHttpRequest.prototype.open = function (method, url, async) {
    if (url.endsWith(".dll")) {
        url = url.replace("dll", "blazor");
    }
    return this.open_before(method, url, async);
}; 
```

*   这是非常基本的 js 代码，由于它的灵活性，我们几乎可以覆盖一切。

这是我的网络标签

[![Toss startup network tab](img/dd93dae52c95d4e2f1be8ef97a54902a.png "Toss startup network tab")](/assets/img/Capture2.PNG)

## 结论

这种变通办法不是一个长期的解决方案，因为我使用非文档化的东西，反病毒或防火墙可能会因为下载文件的内容而阻止我。但无论如何，它有助于理解组装加载在 Blazor 中是如何工作的，尽管缺乏扩展点，我们仍然可以找到解决方案。

## 引用

*   [https://docs . Microsoft . com/en-us/aspnet/core/fundamentals/URL-rewriting？view = aspnetcore-2.1 #使用时 url 重写中间件](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/url-rewriting?view=aspnetcore-2.1#when-to-use-url-rewriting-middleware)
*   [https://developer . Mozilla . org/en-US/docs/Web/API/XMLHttpRequest/open](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/open)