# 第 2 集——使用 ASP.NET 核心、Docker 和 MongoDB 的 JSON API:项目结构

> 原文：<https://dev.to/garfbradaz/episode-2-json-api-dotnet-core-docker-project-structure-3i0c>

## 前情提要

在第一集第一集，我为这个系列将要包含的内容设定了场景。今天的专题只是设置你的*目录*结构并创建相关项目。

## 目录结构

首先，为您的 *Source (src)* 创建一个目录结构，我们的 **dotnet 项目**将保存在这里，*测试*将保存我们的**单元**和**集成**测试，最后， *docker* 将保存我们的 **docker-compose** 。

```
 .
    ├── src
    ├── tests
    ├── docker 
```

### Src

将目录更改为 *src* ，我们将运行下面的 *dotnet* 命令来新建一个项目。在我们继续深入之前，让我们为没有使用过的人谈一谈*网络新闻*。

此命令将基于默认项目模板创建一个点网项目。你可以用 SDK ( [)得到一堆默认值，你也可以创建自己的](https://docs.microsoft.com/en-us/dotnet/core/tutorials/create-custom-template)。运行以下命令:

```
dotnet new --help 
```

这将给出您可以使用的可用模板列表:

[![templates](img/3b89d00bf2091e9e30029d75df25c1db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d_0Gp1qm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://garfbradaz.github.io/assets/img/posts/ep2-dotnet-new.png)

请注意*的简称*，因为这是我们接下来要用到的。现在我们知道了`dotnet new`做什么，让我们创建项目:

```
dotnet new webapi --auth Individual -o api -n BookStore.WebApi 
```

这将创建一个名为 *api* 的文件夹，并在其中创建一个使用身份验证的新的 *webapi* 项目(我们将在后面的章节中进一步探讨)。该项目将被称为*书店。WebApi* 。

**提示:**你可以做一个`dotnet new webapi --help`来查看这个短名字的所有可用开关。

您的目录结构现在应该如下所示:

```
 .
    ├── src
    | ├── api
    | |
    | ├── BookStore.WebApi
    ├── tests
    ├── docker 
```

### 测试

将目录切换到*测试*文件夹。首先主要创建一个*集成*测试文件夹。这些将举行我们的**邮递员**测试(再次为另一集)。其次，我们需要创建我们的单元测试项目。我将使用 *Xunit* ，但是正如你所看到的，当你使用`dotnet new --help`时，有几个不同的测试项目。

```
dotnet new xunit -o unit -n BookStore.Tests 
```

这将再次在名为*单元*的文件夹中创建一个 *xunit* 项目类型。项目名称将是*书店。测试*。在*集成*文件夹中添加一个空的 **postman.json** 文件。

```
 .
    ├── src
    | ├── api
    | |
    | ├── BookStore.WebApi
    ├── tests
    | ├── integration
    | ├── unit
    | ├── BookStore.Tests
    ├── docker 
```

### 码头工人

将目录切换到 *docker* 文件夹，现在只创建两个空文件:

> 坞站-组合. yml
> 
> 坞站-拨号。dev.yml

是的，你猜对了，这个文件将会在未来的一集中变得明显(并且更胖)。

## 下次

现在我们已经建立了我们的项目目录结构，我们可以继续使用 *Dockerfiles* 和 *docker-compose* 来建立我们的 **Docker** 环境。下次和我一起。

**边注:**第一集可以在我的 [Github 库](https://github.com/garfbradaz/webapi-episodes)找到