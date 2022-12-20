# Azure DevOps 的自托管代理:节省成本的小技巧

> 原文：<https://dev.to/akuryan/self-hosted-agents-at-azure-devops-a-little-cost-saving-trick-4297>

Azure DevOps 在提供托管代理服务方面做得非常好。它们装有所有必需的软件，关心更新和其他一切，但它们有一些主要缺点:

*   没有静态外部 IP 地址(因此不可能有额外的安全层))
*   你每次都有一个新的虚拟机，所以你需要重新克隆你的库，安装一套新的 NPM 软件包，安装所有那些基本的 Docker 映像(对于基于 alpine 的映像来说没什么大不了的，但是对于 Microsoft one 来说，这真的是个大麻烦)
*   对于闭源项目，每个托管作业每月有 1800 分钟的硬性限制(我不喜欢限制，即使我从来没有达到它们😀)等等——凡是你能想到的

为了克服这一点，可以部署自托管代理，但这样你就必须处理更新、工具安装和**额外成本**。如何处理更新和实际管理在[这篇博文](https://wouterdekort.com/2018/02/25/build-your-own-hosted-vsts-agent-cloud-part-1-build/)中有所涉及，但它仍然留下了部分成本的问题。我花了一些时间对 Wouter de Kort 在他的知识库中的一个分支做了一些改进，请查看[我的知识库](https://github.com/akuryan/self-hosted-azure-devops-agents)。在这里，我做了一些脚本优化和改进，但我希望在这篇博客中涵盖的主要内容是我为我们的自我管理代理车队构建的成本优化工具。

## 问题

我们只有 2 个托管的 VS2015/17 作业来执行 10 个项目的构建和发布，每个项目都需要 5 到 20 分钟来构建，15 到 30 分钟来发布。这相当费力，尤其是当队伍越来越长的时候。

## 想法

在我们的特殊情况下，我们有 7 个自托管代理的并行作业，这些作业是通过订阅 Visual Studio“免费”提供的，所以我开始研究如何利用这些作业来提高我们的构建和发布速度。初始设置严格遵循 Wouter de Kort 博客系列，在每晚和周末自动切换虚拟机规模以节省成本。但是，当我开始收到在周末启动一些虚拟机的请求，或者在晚上晚些时候启动它们以完成一些非工作时间的任务，或者在早上早些时候进行紧急部署时，我就开始寻找自动执行这些任务的方法。这导致了持续 web 作业的想法，它将持续监控目标池中的队列，并在需要时启动虚拟机(并在不需要时停止它们)。

## 实现

我来到 Azure DevOps 时有很强的 TeamCity 背景，所以我希望在 TeamCity 中找到类似于构建队列的东西，但是，唉，他们有另一种方法:所有的任务都被分配到一个池中，在那里它们被第一个代理以先进先出的方式(先进先出)挑选出来以在线和免费。根本没有队列，所有的任务只是有一个属性“结果”。如果为空，则该任务尚未执行。如果分配给池的所有任务都有一个非空属性“Result”，那么就没有什么可做的。因此，如果池中的一些任务具有空属性“Result ”,那么代码将检查池中有多少在线代理。如果座席计数大于或等于任务计数，则同样不采取任何措施。如果代理计数小于任务计数，我们需要在为代理设置的虚拟机规模中启动更多的虚拟机。如果我们的池中在线代理的数量超过了分配任务的数量，我们需要在扩展集中的虚拟机中停止额外的代理。此外，还有一个选项来定义一周中的营业时间和工作日，此时在线代理的数量最少，以加速开发(因此，团队不必等待代理变得活跃并消耗任务，但任务会立即开始)。配置更多虚拟机的检查每 2 分钟进行一次，以最大限度地减少等待时间，并且不会过度滥用 API。每 15 分钟检查一次是否取消虚拟机资源调配，这为代理提供了更多运行时间。通常，我观察到，在成功的构建之后，开发人员会希望立即部署到测试环境中。

依我拙见，这种解决方案比静态地在某个时间表关闭/打开虚拟机要好，因为它允许在任何时候以一种及时的方式完成任何任务(编译、测试、发布，无论在您的代理上执行什么)。虽然，很自然地，如果所有的代理都关闭了，他们需要一些时间才能上线，但是由于工作时间/天的选择，这只会发生在非工作时间。

此处描述了自动缩放应用程序的所有设置和部署说明[。我不会在这篇博文中重复它们，因为随着时间的推移，它们可能会发生变化，自述文件将保持最新。](https://github.com/akuryan/self-hosted-azure-devops-agents/blob/master/autoscalingApp/README.md)

在[这个位置](https://github.com/akuryan/self-hosted-azure-devops-agents/tree/master/autoscalingApp/AgentsMonitor)可以看到 Autoscaler 应用的代码。

还有一个 [ARM 模板](https://github.com/akuryan/self-hosted-azure-devops-agents/tree/master/autoscalingApp/arm-template)用于 web 应用的基线配置，如果您在 Azure DevOps 中只有一个池需要监控，这是合适的，因为它在 web 应用本身的应用设置级别定义设置。如果有多个池，则只需在 web app 的应用设置中定义共享设置，而特定设置应添加到单个 web 作业的应用设置中。您可以根据需要托管任意数量的 web 作业，但要注意 web 应用程序的限制。

请注意，ARM 模板默认情况下将部署到 D1 web 应用程序(共享 web 应用程序，允许有限的 CPU 时间和 1 Gb 的 RAM **，而不总是打开**)。“始终开启”功能确保托管进程始终处于唤醒状态，并且不会在 20 分钟不活动后关闭。因此，如果在没有额外预防措施的情况下部署 web 作业，它将不会工作，因为 web 应用程序运行时将在 20 分钟不活动后关闭 Kudu 进程。有一个很好的技巧来保持它的运行:你需要至少每 20 分钟 ping 一次你的网络应用的 Kudu 主页。我正在使用 https://www.happyapps.io/的[和](https://www.happyapps.io/)访问我的网络应用程序的 Kudu 主页，每 5 分钟一次，地址是 https://webappName.scm.azurewebsites.net/的

## 部署提示

默认情况下，Azure web app 运行时不会从部署它的路径执行连续的 Web 作业，但是我仍然希望在部署它时确保它没有运行，所以我使用以下 PowerShell 脚本来停止/启动 Web 作业

要停止网络作业:

```
Invoke-AzureRmResourceAction  -ResourceGroupName  resourceGroupName  -ResourceType  Microsoft.Web/sites/continuouswebjobs  -ResourceName  webAppName/webJobName  -Action  Stop  -Force  -ApiVersion  2018-02-01 
```

要启动网络作业:

```
Invoke-AzureRmResourceAction  -ResourceGroupName  resourceGroupName  -ResourceType  Microsoft.Web/sites/continuouswebjobs  -ResourceName  webAppName/webJobName  -Action  Start  -Force  -ApiVersion  2018-02-01 
```

重建虚拟机规模集时使用相同的脚本，以确保在虚拟机注册到池之前，web 作业不会尝试停止虚拟机。

这篇博文由我创建，由我的朋友罗布·哈布拉肯编辑。