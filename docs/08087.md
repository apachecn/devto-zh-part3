# 使用 Azure DevOps 管道在 CI 中构建应用程序的 11 个绝佳资源

> 原文：<https://dev.to/azure/11-awesome-resources-for-building-your-applications-in-ci-using-azure-devops-pipelines-41mk>

这不是你一般的文章。把这篇文章归类为一个我希望你会发现一些价值的大脑垃圾场可能更准确。这是一个精简的链接列表，我发现它对于创建 CI 构建管道非常有用。

这是我在学习如何用 Azure DevOps 创建 CI 管道时喜欢的。我想使用 YAML 文件创建管道，虽然快速入门很有帮助，但我的场景更加细致入微。我敢打赌你的情况也有特例。

当我把我的应用程序放入 Azure DevOps 管道时，我写下了所有我认为非常有用的链接。因此，我决定把它们列成一个清单，与你分享，也许它们也会对你有所帮助。

> 如果你好奇的话，[这是我在创作](https://johnpapa.visualstudio.com/vscode-peacock/_build/results?buildId=186)[孔雀](https://marketplace.visualstudio.com/items?itemName=johnpapa.vscode-peacock&wt.mc_id=devto-blog-jopapa)的时候建造的管道。

## 免费给 OSS？

OSS 的成本是多少？这是一个很棒的问题，最佳答案直接来自这里的[来源](https://azure.microsoft.com/en-us/blog/announcing-azure-pipelines-with-unlimited-ci-cd-minutes-for-open-source/?wt.mc_id=devto-blog-jopapa)。

> 随着今天 Azure DevOps 的推出，我们为开发人员提供了一种新的 CI/CD 服务，称为 Azure Pipelines，使您能够持续构建、测试和部署到任何平台或云。

这里有更多关于[定价的细节，你可以免费得到什么，什么有资格](https://azure.microsoft.com/en-us/pricing/details/devops/azure-devops-services/?WT.mc_id=devto-blog-jopapa)。

## [T1】给我链接](#gimme-the-links)

下面，我试图解释为什么每一个都有帮助，所以你可以决定哪一个适合你的用例。

希望这有帮助！

### - 1 -所有 DevOps 的起始页

从这里开始。说真的，这是一个很好的起点，可以帮助你了解 Azure DevOps 工具以及你能做什么。

*   从这里开始学习 [Azure Pipelines](https://docs.microsoft.com/en-us/azure/devops/pipelines/?view=azure-devops&wt.mc_id=devto-blog-jopapa)

### - 2 -多项工作

如果您想以不同的方式运行您的构建，这是一个很好的资源。例如，假设您想在 Windows、Linux 和 macOS 上运行您的构建。如果是这样，这就是开始的地方。

*   了解如何[创建多个工作岗位](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/multiple-phases?view=azure-devops&tabs=yaml&wt.mc_id=devto-blog-jopapa)

### - 3 -触发一个构建

如果你想触发从 GitHub repo 到 Azure DevOps 的构建，这是一个很好的资源。它展示了如何为分支和拉请求的触发器设置 YAML。

*   了解如何为分支和拉取请求的触发器设置 [YAML](https://docs.microsoft.com/en-us/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=yaml&wt.mc_id=devto-blog-jopapa)

### - 4 - YAML 的例子

如果你想知道 DevOps 中 YAML 文件的样子，不用再找了。

*   看看这个[管道 YAML 文件](https://github.com/MicrosoftDocs/pipelines-javascript/blob/master/azure-pipelines.yml?wt.mc_id=devto-blog-jopapa)的例子

### - 5 -建立多个分支

如果您想要构建多个分支或者从构建中排除一些分支，那么这是一个很好的资源。

*   学会[建立多个分支](https://docs.microsoft.com/en-us/azure/devops/pipelines/build/ci-build-git?view=azure-devops&tabs=yaml&wt.mc_id=devto-blog-jopapa)

### - 6 -使用多个节点版本构建

如果您喜欢针对 Node.js 的多个版本进行测试，这是一个很好的资源。

*   了解如何[为多个节点版本创建构建策略](https://docs.microsoft.com/en-us/azure/devops/pipelines/languages/javascript?view=azure-devops&wt.mc_id=devto-blog-jopapa&tabs=yaml#use-multiple-node-versions)

### - 7 -托管代理和 YAML

如果你想了解更多关于如何创建托管代理(为你做所有工作的东西)，这是一个很好的资源。

*   了解 YAML 的[主机代理语法](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/hosted?view=azure-devops&wt.mc_id=devto-blog-jopapa&tabs=yaml#use-a-microsoft-hosted-agent)

### -8-VS 代码扩展的示例管道

我正在构建一个 VS 代码扩展，所以这个例子对我很有帮助。如果您正在构建 Node.js 应用程序，它可能也会对您有所帮助。

*   参见 VS 代码扩展管道的[示例](https://code.visualstudio.com/api/working-with-extensions/continuous-integration#azure-pipelines?&wt.mc_id=devto-blog-jopapa)

### - 9 -多个构建作业

如果您想以不同的方式运行您的构建，这是一个很好的资源。一个很好的例子是为多个操作系统运行多个作业。

*   了解如何运行[多个作业](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/multiple-phases?view=azure-devops&tabs=yaml&wt.mc_id=devto-blog-jopapa)

### -10-VS 代码扩展

如果你想在 VS 代码中获得 Azure DevOps 管道和 YAML 的额外帮助，试试这个资源。

*   了解 VS 代码的 Azure Pipelines 扩展

*   获取 Azure DevOps 管道扩展

### - 11 -万物 YAML

如果你被困在 YAML(我知道我这样做了)，那么这是一个很好的资源来找出什么去哪里。

*   依靠 [YAML 方案参考](https://docs.microsoft.com/en-us/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema&viewFallbackFrom=vsts&wt.mc_id=devto-blog-jopapa)

我希望这些能像帮助我一样帮助你！

*本文是[从 johnpapa.net](https://johnpapa.net/11devopslinks/)横贴来的*