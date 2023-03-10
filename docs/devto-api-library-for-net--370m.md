# 开发到的 API 库。网🚀

> 原文：<https://dev.to/turnerj/devto-api-library-for-net--370m>

最近有几篇文章([Vue Twitter Bot](https://dev.to/danielelkington/a-bot-that-tweets-new-dev-articles-about-vue-4p5a)&[React Twitter Bot](https://dev.to/frontendwizard/inspired-by-thevuedev-i-created-thereactdev-55g2))谈到创建自动 Twitter Bot 来发布特定主题的文章，我想我应该加入进来，为任何主题编写一个基本的库。网络客户端。这是一个完全异步的库，使用。净标准。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [特纳杰](https://github.com/Turnerj) / [ forem-dotnet](https://github.com/Turnerj/forem-dotnet)

### 。Forem 应用程序的. NET API 接口。Forem 是支持 DEV 和其他在线社区的平台。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 的 Forem API。网

用于 [Forem](https://www.forem.com/) 应用程序的 API 接口。Forem 是支持[开发](https://dev.to)和其他在线社区的平台。

[![AppVeyor](img/0b2b0992f1ca99b78db4417ca9f1d1ed.png)](https://ci.appveyor.com/project/Turnerj/forem-dotnet)[![Codecov](img/4e72fda00f78d54715daf9b5265710c3.png)](https://codecov.io/gh/turnerj/forem-dotnet)[![NuGet](img/8f51c2934c8cd6f4c358cc142c4677ef.png)](https://www.nuget.org/packages/Forem.Api)

## API 支持

我们尝试按照 [Forem/DEV API](https://docs.dev.to/api/) 支持所有 API 端点，包括:

*   文章
*   用户
*   播客
*   录像

如果可用，库支持分页和页面大小控制。如果有当前不支持的 API 端点，请随意打开问题或创建 PR。

## 入门指南

### 装置

要安装库，请在 Nuget 管理器控制台上运行以下命令:

`Install-Package Forem.Api -Version 0.4.0`

## 使用

*   对于某些方法，需要传递开发 API 密钥`apiKey`来验证您的用户身份。要获取一个，请查看验证部分[获取 API 密钥](https://docs.dev.to/api/#section/Authentication)

*   使用 DI，只需执行以下操作就可以将服务添加到容器中:

    ```
    services.AddForemApi(new Uri("https://dev.to/"))

    ```

    Enter fullscreen mode Exit fullscreen mode
*   通过添加…来配置您的`HttpClient` DI

</article>

[View on GitHub](https://github.com/Turnerj/forem-dotnet)

现在还为时过早，因为 API 可能会有一些变化，可能在某个时候会需要一个 API 密匙，但可能会帮助一些人更容易上手。

你可以在 NuGet [这里](https://www.nuget.org/packages/DevTo.Api/)抢到。

很高兴与开发团队合作，以确保该库支持该网站的当前和未来的 API。🙂