# 调试。Docker 中的网络核心

> 原文：<https://dev.to/garfbradaz/debug-dotnet-core-in-docker-5c66>

## 用 Visual Studio 代码在 Linux Docker 中调试 ASP.NET 内核

所以，你已经开始使用 Docker 为你的。NET 核心应用程序-好消息。您使用 Linux 容器和 Visual Studio 代码，然后调试您的容器…..哦。(我用的是 Windows)。

这不是我发现的最直接的过程，如果你想让它运行起来的话。首先，我不是靠自己弄明白的，我写博客的原因是因为我把我所掌握的知识结合在一起，再加上这些非常有用的帖子:

*   乔纳森·钱宁
*   [理查德·班克斯](https://www.richard-banks.org/2018/07/debugging-core-in-docker.html)

是上面两个帖子给我粘在一起的。也好好读一读。在我们继续之前，只是为了确认这些指令是针对使用 *Docker Compose* 的 *Visual Studio 代码*和 *Linux 容器*，我不确定这在 Visual Studio 上是如何设置的。我假设你有 *VSCode* 、 *Docker* 和*。NET Core SDK* 已安装。而且你用的是 Linux 容器。

## DockerFile

首先，让我们来看看我构建项目的多阶段(多个`FROM`)docker 文件。

### 第一阶段构建

Dockerfile `build-env`阶段的第一部分是`buildconfig`的`ARG`，它控制`dotnet publish`如何配置。是的，我知道，我有一个 todo 项来重构 if/else，以将`buildconfig`作为`-c`值传递。

### 第二阶段运行

所以第一阶段处理构建，第二阶段处理 Web API 项目的运行。如你所见，这里又有一个`Debug`开关，用来在`Debug`中运行时安装一些依赖项。忽略 **mongo** 安装，我们感兴趣的是 *vsdbg* 安装。

`curl -sSL https://aka.ms/getvsdbgsh | bash /dev/stdin -v latest -l`

> 调试器平台在 Visual Studio 2017 15.3 和以前版本的 Visual Studio 之间进行了扩展。Visual Studio 现在可以使用调试适配器协议进行通信，该协议由 Visual Studio、Visual Studio 代码和 Visual Studio for Mac 使用。这个协议现在用于跨平台调试。NET 核心应用程序。
> 
> [来源](https://github.com/Microsoft/MIEngine/wiki/Offroad-Debugging-of-.NET-Core-on-Linux---OSX-from-Visual-Studio)

因此，vsdbg 被安装到容器中，以允许使用 Visual Studio 代码从您的主机进行远程调试。

```
FROM microsoft/dotnet:2.2-sdk AS build-env
ARG buildconfig
WORKDIR /app
COPY ServiceApp.WebApi.csproj .
RUN dotnet restore
COPY . .
RUN if ["${buildconfig}" = "Debug"]; then \
        dotnet publish -o /publish -c Debug; \
    else \
        dotnet publish -o /publish -c Release; \
    fi

FROM microsoft/dotnet:2.2-aspnetcore-runtime
ARG buildconfig
ENV DEBIAN_FRONTEND noninteractive
WORKDIR /publish
COPY --from=build-env /publish .
RUN if ["${buildconfig}" = "Debug"]; then \
        apt-get update && \
        apt-get install -y --no-install-recommends apt-utils && \
        apt-get install curl unzip procps mongodb -y && \
        curl -sSL https://aka.ms/getvsdbgsh | bash /dev/stdin -v latest -l /publish/vsdbg; \
     else \
        echo "*Whistling*"; \
    fi
ENV DEBIAN_FRONTEND teletype
ENTRYPOINT ["dotnet","ServiceApp.WebApi.dll"] 
```

## 为 Visual Studio 代码启动任务配置

所以这是我一直在努力解决的问题，以至于我[记录了一个 Github 问题](https://github.com/OmniSharp/omnisharp-vscode/issues/2724)并且随后有了一个灵感！

乔纳森和理查兹都为自己的岗位工作和调查，再次帮了十倍的忙。

重要的部分是`sourceFileMap`,它将项目代码映射到容器中的二进制文件。我的引用中的 */app* 与 Dockerfile 中声明的 */app* 目录相关。

另一个重要的片段是`debuggerPath`选项。这是容器中安装 vsdbg 的目录。如果您愿意，您可以在 docker 文件的`curl`命令中设置不同的目录。

最后，注意容器名`webapi_debug`，我在相关的 compose 文件中设置了它。

下面的将存在于您的`.vscode`目录下的`launch.json`中。如果配置正确，您应该会看到一个漂亮的启动图标:

[![icon](img/bb6ad3160490694f10098740701caf82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bWiwSSFg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://garfbradaz.github.io/assets/img/posts/debug-vscode-docker.png)

```
{
    "name": "Attach to webapi (Docker)",
    "type": "coreclr",
    "request": "attach",
    "sourceFileMap": {
        "/app": "${workspaceFolder}/src/api"
    },
    "processId" : "${command:pickRemoteProcess}",
    "pipeTransport": {
        "debuggerPath": "/publish/vsdbg/vsdbg",
        "pipeProgram": "docker",
        "pipeCwd": "${workspaceFolder}/src/api",
        "quoteArgs": false,
        "pipeArgs": [
            "exec","-i", "webapi_debug"
        ]
    }
} 
```

## 作曲

因为我为 *MongoDB* 和 *Identity Server* 使用了独立的 Docker 容器，所以我使用`docker-compose`将我的容器编织在一起。

**注意:**我计划接下来写一篇身份服务器的帖子，谈谈我在设置身份服务器方面的经历。

以下来自我的`docker-compose.dev.yml`文件。

因此，具有有趣配置的容器是`webapi`，这是我的 ASP.NET 核心 Web API 项目。如你所见，我将容器名设置为`webapi_debug`。正如我之前提到的，启动任务需要远程访问那个特定的容器。

您还可以看到我从我的**主机** Windows 机器使用`volumes`挂载我的源代码目录，使用我的源代码目录的相对路径(*../api* 到 *:/app* ，允许我们在遇到断点时显示代码。如果您错误地配置了该位，则在引发断点时会出现以下错误:

[![file-not-found](img/865dfc655f6eda768d0fdba27ebaefca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yfzq8JjQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://garfbradaz.github.io/assets/img/posts/not-found-debug.png)

其他有趣的配置点是用于`Development`的 **ASPNETCORE_** *环境变量和`Debug`的`buildconfig`参数，正如我们之前了解的，它们被传递到`Dockerfile`中并被使用。

```
version: "3"
services:
  identity:
    image: garfbradaz/serviceident:develop
    container_name: identity
    build:
      args:
        buildconfig: Debug
      context: ../quickidentity
      dockerfile: Dockerfile
    volumes:
      - ../quickidentity:/app2
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=http://identity:5002  
      - AuthorityUrl=http://identity:5002
    ports:
      - "5002:5002"
  webapi:
    image: garfbradaz/serviceapp:develop
    container_name: webapi_debug
    build:
      args:
        buildconfig: Debug
      context: ../api
      dockerfile: Dockerfile
    volumes:
      - ../api:/app
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=http://+:5001
      - AuthorityUrl=http://identity:5002
    ports:
      - "5001:5001"
    depends_on:
      - mongodb
      - identity
  mongodb:
    image: mongo:latest
    container_name: mongodb
    ports:
      - "27017:27017" 
```

## 跑步

因此，将这些放在一起，您可以使用 Compose 运行您的容器:

`docker-compose -f docker-compose.dev.yml up -d --build`

在你的代码中设置一个断点(我在我的控制器中设置的例子)，并在 *Visual Studio 代码*中运行你的调试任务。这将要求您为您的`dotnet`代码选择您的进程。然后，当断点被命中时，您可以像平常一样检查它。

我发现它可能会挂起，有时需要半分钟才能启动调试会话，所以请耐心等待。

无论如何，我希望这能帮助到一些人，非常感谢乔纳森和 T2 理查德。