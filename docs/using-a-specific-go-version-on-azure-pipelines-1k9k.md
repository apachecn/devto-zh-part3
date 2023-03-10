# 在 Azure 管道上使用特定的 Go 版本

> 原文：<https://dev.to/azure/using-a-specific-go-version-on-azure-pipelines-1k9k>

有了 Azure Pipelines，我们可以用许多不同的语言构建应用程序，其中之一就是 T2 Go T3。

如果您使用的是[托管代理](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/hosted?view=azure-devops&WT.mc_id=devto-blog-aapowell)，这是推荐的方式(因为您不需要管理自己的机器)，那么您将受到代理上安装的软件的支配。但是，如果您正在构建一个针对特定运行时的应用程序，比如 Go 1.12.3(撰写本文时的最新版本)，那么您可能就不太走运了，因为代理没有安装这个版本。

因此，让我们来看看如何将 Go 设置为您的管道的一部分。

*边注:你也可以使用 [Docker 来构建](https://docs.microsoft.com/en-us/azure/devops/pipelines/languages/docker?view=azure-devops&WT.mc_id=devto-blog-aapowell)，但是 Docker 并不适合所有人，所以我在这里将重点放在非集装箱代理上。如果你需要几个运行时，Docker 也是一个挑战，比如说，[用 Go + WASM 支持](https://dev.to/aaronpowell/learning-golang-through-webassembly---go-wasm-typescript-and-react-klm)构建一个 web 应用。*

我们将修改标准的 [Go Azure 管道](https://docs.microsoft.com/en-us/azure/devops/pipelines/languages/go?view=azure-devops&WT.mc_id=devto-blog-aapowell)，所以从模板开始。

## 设置我们的变量

我们要做的第一件事是修改 Go 将会期望的变量，特别是`GOPATH`和`GOROOT`。默认情况下，它们指向代理上的一个 Go 版本，但是我们将修改它们以使用我们的 Go 版本。

```
variables:
  GOPATH: '$(Agent.BuildDirectory)/gopath' # Go workspace path
  GOROOT: '$(Agent.BuildDirectory)/go' # Go installation path
  GOBIN: '$(GOPATH)/bin' # Go binaries path
  modulePath: '$(GOPATH)/src/github.com/$(build.repository.name)' # Path to the module's code 
```

我们将使用一个[代理预定义的变量](https://docs.microsoft.com/en-us/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml&WT.mc_id=devto-blog-aapowell#agent-variables)，`Agent.BuildDirectory`，因为它是我知道我可以在代理上写入的地方，并且它的范围特别是我的构建。

`GOPATH`将是我们创建的一个新文件夹，而`GOROOT`将是 Go 解压到的地方。这些现在将是环境变量，所以当 Go 最终执行时，它将是 Go 的正确版本。

## 下载 Go

接下来，我们将添加一个新步骤来下载和解包我们想要的 Go 版本:

```
steps:
- script: |
    wget "https://storage.googleapis.com/golang/go1.12.3.linux-amd64.tar.gz" --output-document "$(Agent.BuildDirectory)/go1.12.3.tar.gz"
    tar -C '$(Agent.BuildDirectory)' -xzf "$(Agent.BuildDirectory)/go1.12.3.tar.gz"
  displayName: 'Install  Go  1.12' 
```

因为我们知道我的代理是一个 Linux 代理(从模板中定义的`pool`开始),我们将使用`wget`下载 Linux 二进制文件，并把它们放到`Agent.BuildDirectory`中。完成后，我们可以用`tar`解包它，指定我们想要解包到`Agent.BuildDirectory`，并且给定`tar.gz`的结构包含一个名为`go`的文件夹，我们将最终得到一个与`GOROOT`完美匹配的路径。

最后，我们需要确保代理的`PATH`知道这个版本的 Go，我们通过[设置输出变量](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch&WT.mc_id=devto-blog-aapowell#set-in-script)来实现:

```
- script: |
    echo '##vso[task.prependpath]$(GOBIN)'
    echo '##vso[task.prependpath]$(GOROOT)/bin' 
```

*文档指南中的[设置 Go 工作区](https://docs.microsoft.com/en-us/azure/devops/pipelines/languages/go?view=azure-devops&WT.mc_id=devto-blog-aapowell#set-up-a-go-workspace)步骤对此进行了介绍。*

现在，管道的其余部分可以按照模板描述的准确地跟随[！](https://docs.microsoft.com/en-us/azure/devops/pipelines/languages/go?view=azure-devops&WT.mc_id=devto-blog-aapowell)

## 结论

依赖项的版本固定对于确保我们随着时间的推移拥有可重复的构建非常重要。因为我们不希望最终陷入手动管理代理池的境地，所以我们希望我们的构建定义为我们期望的环境准备好代理，包括设置适当的运行时。

你可以在我的 GitHub 上看到完整的管道定义[，它利用了这种方法。](https://github.com/aaronpowell/oz-dev-events/blob/dcd3fc3c6c74f1da037d17993380c51ffe996bda/azure-pipelines.yml)

您也可以使用这种方法来准备多个不同版本的 Go，以创建一个针对多个运行时版本的构建矩阵，但是亲爱的读者，我将把它作为一个练习留给你😉。