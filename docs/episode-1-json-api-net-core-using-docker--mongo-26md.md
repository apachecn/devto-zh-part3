# 第 1 集——使用 ASP.NET 核心、Docker 和 MongoDB 的 JSON API

> 原文：<https://dev.to/garfbradaz/episode-1-json-api-net-core-using-docker--mongo-26md>

## 灵感

有一个关于 [dev.to](https://dev.o) 的系列，我跟随 [Nathan Bland](https://dev.to/nathanabland) 录制了一系列关于创建 JSON API 的视频，使用 *Node.js* 、 *docker* 和 *Express* 。

我喜欢围绕这一点的想法，所以我将做同样的事情，但以博客的形式，但用 ASP.NET 的核心 web API T5 代替 T1 和 T3。我还想使用 MongoDB 作为后端。

## 设置场景

API 本身将面向连锁书店，这些书店希望通过公共 RESTful 服务公开他们的商店和图书，供其他公司使用。这可用于库存查找，并查看哪些商店有库存可供出售。

下面是一些例子来展示所需 API 的形式:

> 作为一家*书店，我可以将*我们的*书店*添加到数据库中，这样我们就可以被访问了
> 
> 作为一家书店，我可以将我们在 T2 的库存添加到我们的数据库中，这样我们就可以公开我们的库存
> 
> 作为一家*书店，我们可以更新*图书的库存水平，以获得准确的库存水平
> 
> 作为一名 *API 消费者，我可以查找 a _stores 地址* *，这样*我们就知道在哪里可以买到*的书*
> 
> 作为一个 *API 消费者 _ 我可以查找 a _book* *所以*我们可以得到一个销售 a _book 的*商店*的*列表*
> 
> 由于*book store IT Security _ 我们可以向 API_For_API 消费者添加 _ API 密钥*,以便在查询 API 时使用

## 期待什么

我的计划是以类似内森的方式每周发布博客文章:

*   设置您的。NET 核心项目结构。
*   使用 docker-compose 设置 docker 环境。
*   使用 Mongo 对 API JSON 后端建模。
*   使用 Swagger 对 API 建模。
*   使用 Xunit 进行单元测试。
*   与 Postman 的集成测试。
*   骨架书店 API(路线等)。
*   骨架库存 API(路线等)。
*   保护您的 API。
*   数据验证。
*   大型数据查询的分页。
*   过滤和搜索。
*   为构建和发布管道连接到 Azure Dev Ops。
*   Github 上的 Publisher Swagger 文档。

我也会在 Github 上发布任何代码。

## 从零开始

因此，在开始处理这个问题之前，请确保您的环境已经设置好了。因为这是*。NET Core* ，这是跨平台的，所以如果你拥有一台 Mac 或者 Linux 的盒子，可以随意加入进来。我将在一台 *Surface Book* 上构建这个，但我会努力成为一名优秀的 Xpat 公民，并进行跨环境测试。

请确保安装了以下软件:

*   [Dotnet 核心](https://dotnet.microsoft.com/download) -我目前用的是 2.2。
*   [Docker](https://www.docker.com/get-started) -如果你没有的话，你还需要为 [Docker Hub](https://hub.docker.com/signup) 创建一个登录名。
*   [Powershell Core](https://docs.microsoft.com/en-gb/powershell/scripting/overview?view=powershell-6)-Powershell Core 是跨平台的，可以在 Windows、Linux 和 MacOS 上运行，而旧版本只能在 Windows 上运行(CLI)。可以选择安装在自己选择的操作系统上。

我们不需要安装 Mongo，因为我们将使用 *Docker* 运行它。

## [这里](#ide)

我将使用跨平台的 Visual Studio 代码。净核心支持。我在这里使用了一些扩展:

[https://gist . github . com/garfbradaz/be 010080 df 076d 19892 BF 4 EC 71127 b 53](https://gist.github.com/garfbradaz/be010080df076d19892bf4ec71127b53)

您可以使用 marketplace 上的以下*设置同步*扩展来导入这些:

[https://marketplace.visualstudio.com/items?itemName = shan . code-settings-sync](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)

如果你已经使用了 *VSCode* ，你可能只想找到 *C#* 、 *Docker* *GitLens* 和*。NET 核心测试资源管理器* [扩展](https://code.visualstudio.com/docs/editor/extension-gallery)。

注意:我的列表包含了像*杰基尔*支持这样的扩展，你不需要它。

## 下次

我们将创建*ASP.NET 核心 WebAPI* 模板，建立项目结构。下次加入我吧！。