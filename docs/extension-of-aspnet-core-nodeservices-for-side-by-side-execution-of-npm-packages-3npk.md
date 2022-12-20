# 用于并行执行 npm 包的 ASP.NET 核心节点服务的扩展

> 原文：<https://dev.to/akashkava/extension-of-aspnet-core-nodeservices-for-side-by-side-execution-of-npm-packages-3npk>

# ASP.NET 核心的节点服务器

NodeServices 的扩展，用于并行执行节点包的版本。

## 安装

`Install-Package NeuroSpeech.NodeServer`

从[https://inedo.com/proget](https://inedo.com/proget)安装并设置 ProGet，并将你的私有包发布到 ProGet 中的 npm 注册表上。将来会支持经过验证的 npm 注册表。

## 特性

1.  同一包的不同版本的并行执行
2.  从私有的 npm 库(如 ProGet)下载并解压缩 NPM 包和依赖项
3.  TempFolder 可以在`D:`驱动器上配置，建议 Azure 虚拟机使用。

## 安全

1.  节点服务器不执行`npm install`或`npm start`脚本。
2.  TempFolder 驱动器需要写权限
3.  只有在`PrivatePackages`中列出的包才会被下载和提取，但是依赖关系并不局限于包白名单。您的开发人员必须小心不要将它们包含在包依赖项中。

## 设置

```
services.AddSingleton<NodeServer.NodeServer>(
    sp => new NodeServer.NodeServer(sp,
        new NodeServer.NodeServerOptions
        {
            // This must be unique to avoid multiple process accessing same
            // folder conflicts
            TempFolder = "D:\\temp\\" + Guid.NewGuid(),
            // ProGet private registry url
            NPMRegistry = "https://private-proget.company.com/npm/PRIVATE",
            PrivatePackages = new string[] {
                "@company/package@1.0.1",
                "@company/core@1.0.1"
            }
        })); 
```

## 执行

```
 [HttpGet("template/{version}/{name}")]
    public async Task<string> ProcessTemplate(
        [FromRoute] string name,
        [FromRoute] string version,
        [FromBody] EmailModel model,
        [FromService] NodeServer.NodeServer server
    ) {

        // get node services from the installed package version
        // if version does not exist, it will download package
        // along with all its dependencies
        var package = await 
            server.GetInstalledPackageASync($"@company/template@{version}");

        var nodeServices = package.NodeServices;

        return await nodeServices.InvokeExportAsync(
            "index.js",
            "default",
            new {
                fromName: "Sender",
                fromAddress: "senderEmail",
                body: model
            });

    } 
```

`GetInstalledPackageAsync`方法将创建一个文件夹`{TempFolder}\npm\{package}\v{version}`，它将从给定的 npm 注册表中提取包。

> 它不会安装 npm 包，因为 IIS 网站可能没有足够的权限来执行`npm`命令。所以为了让事情更简单，NodeServer 检查 package.json 文件，并下载`node_modules`文件夹中的所有依赖项。它不执行任何脚本。

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png)[neurospeech](https://github.com/neurospeech)/[ASP-net-core-node-server](https://github.com/neurospeech/asp-net-core-node-server)

### 用于并行执行 npm 包的 ASP.NET 核心节点服务的扩展

<article class="markdown-body entry-content" itemprop="text">

[![Build status](img/8610a3b0abd90f17247a9c36631807d1.png) ](https://ci.appveyor.com/project/neurospeech/asp-net-core-node-server) [ ![NuGet](img/7a43309b436ab972fd548f9df1fd75a4.png)](https://www.nuget.org/packages/NeuroSpeech.NodeServer)

# ASP.NET 核心的节点服务器

NodeServices 的扩展，用于并行执行节点包的版本。

## 装置

`Install-Package NeuroSpeech.NodeServer`

从[https://inedo.com/proget](https://inedo.com/proget)安装并设置 ProGet，并将你的私有包发布到 ProGet 中的 npm 注册表上。将来会支持经过验证的 npm 注册表。

## 特征

1.  同一包的不同版本的并行执行
2.  从私有的 npm 库(如 ProGet)下载并解压缩 NPM 包和依赖项
3.  TempFolder 可以在`D:`驱动器上配置，建议 Azure 虚拟机使用。

## 安全性

1.  节点服务器不执行`npm install`或`npm start`脚本。
2.  TempFolder 驱动器需要写权限
3.  只有在`PrivatePackages`中列出的包才会被下载和提取，但是依赖关系并不局限于包白名单。您的开发人员必须小心不要将它们包含在包依赖项中。

## 设置

```
services.AddSingleton<NodeServer.NodeServer>(
    sp => new NodeServer.NodeServer(sp
        new NodeServer.
```

…</article>

[View on GitHub](https://github.com/neurospeech/asp-net-core-node-server)