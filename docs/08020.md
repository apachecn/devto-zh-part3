# 调试您的。带有 VS 代码的 Docker 应用程序中的 NET Core

> 原文：<https://dev.to/azure/debugging-your-net-core-in-docker-applications-with-vs-code-3g5d>

构建应用程序的最大好处之一。NET 的核心在于它的跨平台支持意味着我们可以将我们的应用程序[部署为 Docker 容器](https://docs.microsoft.com/en-us/dotnet/core/docker/intro-net-docker?WT.mc_id=devto-blog-aapowell)。如果你用的是 Visual Studio，它内置了对 Docker 的支持[，但如果你用的是 Mac 或 Linux，那就不行了，或者像我一样，你更喜欢用](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/docker/visual-studio-tools-for-docker?view=aspnetcore-2.2&WT.mc_id=devto-blog-aapowell) [VS Code](https://code.visualstudio.com/?WT.mc_id=devto-blog-aapowell) 作为你的编辑器。

所以如果你为。NET 它看起来是这样的:

```
FROM mcr.microsoft.com/dotnet/core/sdk:2.2
WORKDIR /app
COPY ./bin/Debug/netcoreapp2.2/publish .
ENTRYPOINT ["dotnet", "MyApplication.dll"] 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们现在可以通过构建映像和启动容器来运行我们的应用程序，但是如果我们想调试它，会发生什么呢？

## 启用远程调试

如果你从逻辑上考虑，当在 Docker 中运行一个应用程序时，它本质上是远程运行的。当然，它可能远程地在同一台机器上，但是它仍然是“远程的”，这就是我们需要考虑调试的方式！

要做到这一点，我们需要将 [MIEngine](https://github.com/Microsoft/MIEngine) 安装到我们正在构建的 Docker 映像中，为此我们将在我们的`Dockerfile` :
中添加一个新层

```
FROM mcr.microsoft.com/dotnet/core/sdk:2.2
RUN apt update && \
    apt install unzip && \
    curl -sSL https://aka.ms/getvsdbgsh | /bin/sh /dev/stdin -v latest -l /vsdbg
WORKDIR /app
COPY ./bin/Debug/netcoreapp2.2/publish .
ENTRYPOINT ["dotnet", "MyApplication.dll"] 
```

Enter fullscreen mode Exit fullscreen mode

新的`RUN`层将首先更新`apt`以获得所有最新的包引用，然后安装`unzip`，最后执行`curl`哪些管道到`/bin/sh`。这可能看起来有点混乱，但这是因为我们将三个命令链接到一个层中，以减小 Docker 图像的大小。真正最重要的部分是这一行:

```
curl -sSL https://aka.ms/getvsdbgsh | /bin/sh /dev/stdin -v latest -l /vsdbg 
```

Enter fullscreen mode Exit fullscreen mode

这将从`https://aka.ms/getvsdbgsh`下载一个`sh`脚本，并通过管道直接传输到`/bin/sh`执行，并提供一些参数，最重要的是`/vsdbg`，远程调试器将被提取到这里。

现在我们的映像已经安装了调试器，我们需要设置 VS 代码来附加它。

## 将 VS 代码附加到远程调试器

我们将在我们的`launch.json`文件中添加一个新条目，它属于`"type": "coreclr"`和`"request": "attach"`。这将导致 VS 代码启动进程选择器，并允许我们选择我们的。NET 核心进程。

但是等等，那是在 Docker 容器中运行的，我如何选择那个进程呢？

幸运的是，进程选择器对话框能够执行一个命令来获取进程列表，并且可以在远程机器上执行。

它将执行`docker exec -i <container name> /vsdbg/vsdbg`来列出容器中的进程，但是我们会做得更好一点:

```
{  "name":  ".NET Core Docker Attach",  "type":  "coreclr",  "request":  "attach",  "processId":  "${command:pickRemoteProcess}",  "pipeTransport":  {  "pipeProgram":  "docker",  "pipeArgs":  ["exec",  "-i",  "sunshine-downloader"],  "debuggerPath":  "/vsdbg/vsdbg",  "pipeCwd":  "${workspaceRoot}",  "quoteArgs":  false  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您运行您的容器，然后在 VS 代码中启动调试器，您将能够在您的应用程序正在使用的容器中选择`dotnet`进程。

## 结论

现在你可以选择使用 VS 代码作为编辑器，也可以调试 Docker 容器中运行的应用程序。您可以处理更高级的场景，包括通过 SSH 进行调试，所有这些都包含在 OmniSharp 的 wiki 中。

事实上，我正在用它来调试一个正在运行的 F#应用程序。网芯。😉

调试愉快！😁

## 额外创意:移除带有体积的附加层

当我在内部分享这个帖子时，我的同事[肖恩·博耶](https://twitter.com/spboyer)提出了一个想法，如何在不增加新层的情况下解决这个问题，事实上，可以调试预建的图像(假设它们有调试符号)。

你可以通过下载你的映像所基于的发行版(Ubuntu，Alpine，ARM 等)的 vsdbg 包来实现。)，这可以通过阅读 shell 脚本来确定(或者下载到一个容器中😉)安装到您的机器上，然后在启动您的容器时将该路径作为一个卷安装:

```
docker run --rm -v c:/path/to/vsdbg:/vsdbg --name my-dotnet-app my-dotnet-app 
```

Enter fullscreen mode Exit fullscreen mode

现在，您已经在启动调试器时将它插入到容器中，而不是将其捆绑到映像中。