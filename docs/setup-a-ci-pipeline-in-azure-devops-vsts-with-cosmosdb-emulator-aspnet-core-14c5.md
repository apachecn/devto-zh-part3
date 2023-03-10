# 使用 CosmosDB 模拟器、ASPNET 核心在 Azure DevOps (VSTS)中设置 CI 管道

> 原文：<https://dev.to/rembou1/setup-a-ci-pipeline-in-azure-devops-vsts-with-cosmosdb-emulator-aspnet-core-14c5>

在我的项目 [Toss](https://github.com/RemiBou/Toss.Blazor) 中，到目前为止，挑战更多的是关于与 CosmosDB 的集成，而不是复杂的业务逻辑实现，所以我的测试策略是更高层次的:集成和 E2E。当您想从 CI 管道中执行这类测试时，这是一个挑战，因为您必须在不直接访问系统的情况下设置整个环境。

我将为此使用的服务是 Azure DevOps，以前称为 Visual Studio Team Service。这个工具在过去的一个月里改变了很多，因为现在我们可以使用存储在源代码控制中的 yml 文件来设置整个管道。在这篇博文中，我们将看到如何设置所有的元素。

## 设置管道

这一步相当简单，但仍然是强制性的。

要设置管道，单击项目名称旁边的+,然后单击“新建管道”。在这里，你必须选择你的回购协议，并选择一个模板，以便 Azure DevOps 将检查它(是)，并读取 yml 定义和执行管道。

这个管道由多个步骤组成，这些步骤是由运行管道的计算机(代理)执行的事情，有许多内置任务，您可以在这里找到其中的一些:[https://docs . Microsoft . com/en-us/azure/devo PS/pipelines/tasks/index？view=vsts](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/index?view=vsts) 。

## 选择运行流水线的虚拟机

首先要做的是选择正确的虚拟机来运行管道。这里我选择了 Windows Server 上的 Visual Studio，因为我知道 CosmosDB 模拟器只能在 Windows 上运行(我想他们是在 Linux / MacOS 版本上工作)。

你的 yml 文件的第一行将会是

```
pool:
  vmImage: 'vs2017-win2016' 
```

*   所有可用图片都在这里:[https://docs . Microsoft . com/en-us/azure/devo PS/pipelines/agents/hosted？view = vsts&tabs = YAML # use-a-Microsoft-hosted-agent](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/hosted?view=vsts&tabs=yaml#use-a-microsoft-hosted-agent)
*   要知道:这个 VM 将在管道每次运行时从头开始创建，这会导致您的构建过程很慢，但是您确信它们之间没有副作用。

## 设置 SDK

下一步是开始创建您的构建步骤。第一个是检查。NET Core SDK 已安装。为此有一个任务:

```
variables:
  buildConfiguration: 'Release'

steps:
- task: DotNetCoreInstaller@0
  displayName: 'Install  SDK  2.1.403'
  inputs:
    version: 2.1.403 
```

*   为了找到任务名，我使用了 GUI 编辑器，它有一个工具可以找到对应于一个步骤的 YML 源代码。
*   所有的 SDK 版本都可以在这里找到[https://github . com/dot net/core/blob/master/release-notes/releases . CSV](https://github.com/dotnet/core/blob/master/release-notes/releases.csv)

## 构建项目

现在我们需要构建我们的项目，这不是很难

```
- script: dotnet build --configuration $(buildConfiguration)
  displayName: 'dotnet  build' 
```

*   buildConfiguration 指的是前面部分定义的变量。我本可以硬编码的

## 启动 CosmosDB 模拟器

对于我的集成测试，我需要访问 CosmosDB 服务器。为此，我可以使用付费的 COsmosDB 实例，但是我很便宜，所以我更喜欢使用 CosmosDB 模拟器。
在设置步骤之前，你需要在你的 Azure Devops 帐户上安装模拟器，如下:[https://marketplace.visualstudio.com/items?itemName = azure-cosmos db . emulator-public-preview](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview)

现在是台阶

```
- task: azure-cosmosdb.emulator-public-preview.run-cosmosdbemulatorcontainer.CosmosDbEmulator@2
  displayName: 'Run  Azure  Cosmos  DB  Emulator  container' 
```

*   为了找到任务名称，我再次使用了 GUI 设计器，因为这在任何地方都没有记录。
*   这是无可争议的
*   这在一个容器上运行 CosmosDB 模拟器，所以我们需要找到一种方法将容器主机名传递给我们的测试，这是下一步。

## 运行集成测试

我的集成测试是 xUnit 测试。这是我在这一步的 yml 配置:

```
- script: dotnet test Toss.Tests\Toss.Tests.csproj  -v n
  displayName: 'Tests'
  env: { 'CosmosDBEndpoint': "$(CosmosDbEmulator.Endpoint)" }  # list of environment variables to add 
```

*   我在 dotnet test 上强制执行该项目，这样它就不会扫描完整的回购协议来搜索测试程序集(dotnet 测试输出已经是一个噩梦，如果我们能减少一点就不错了)
*   "-v n "是为了调试的目的，我需要控制台。写一些事情
*   环境变量设置用于将 CosmosDB 主机名传递给测试
*   $(CosmosDbEmulator。端点)由前面的步骤设置

我是这样理解环境变量的:

```
var dict = new Dictionary<string, string>
  {
       { "GoogleClientId", ""},
       { "GoogleClientSecret", ""},
       { "MailJetApiKey", ""},
       { "MailJetApiSecret", ""},
       { "MailJetSender", ""},
       { "CosmosDBEndpoint", "https://localhost:8081"},
       { "CosmosDBKey", "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw=="},
       { "StripeSecretKey", ""},
      {"test","true" },
      {"dataBaseName",DataBaseName }
  };

var config = new ConfigurationBuilder()
    .AddInMemoryCollection(dict)
    .AddEnvironmentVariables()
    .Build();
 var startup = new Startup(config); 
```

*   环境变量将覆盖 localhost 设置，所以测试将在我的开发机器和代理上运行。
*   模拟器的 CosmosDBKey 总是一样的，幸运的我们:)

## 结果

你可以在这里看到我的测试:[https://dev.azure.com/remibou/toss/_build/results?buildId=23 & view=logs](https://dev.azure.com/remibou/toss/_build/results?buildId=23&view=logs) (我需要修复 E2E 测试，这是为了另一篇日志)。

## 结论

设置起来并不难，但是对于面向代码的配置来说，如果没有足够的工具/文档，就很难发现。在这里，我必须使用 GUI 编辑器找到任务名称，希望一个新的 VS 插件将连接 Azure DevOps 帐户和 yml 编辑器，以提供 intellisens(在 10 年内)。

你可以在这里找到完整的 yml:[https://github.com/RemiBou/Toss.blazor/blob/master/azure-pipelines . yml](https://github.com/RemiBou/Toss.Blazor/blob/master/azure-pipelines.yml)

## 引用

*   [https://docs . Microsoft . com/en-us/azure/devo PS/pipelines/targets/cosmos-db？view=vsts](https://docs.microsoft.com/en-us/azure/devops/pipelines/targets/cosmos-db?view=vsts)
*   [https://docs . Microsoft . com/en-us/azure/devo PS/pipelines/YAML-schema？view=vsts & tabs=schema](https://docs.microsoft.com/en-us/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema)
*   [https://weblog . west-wind . com/posts/2018/Feb/18/access-Configuration-in-NET-Core-Test-Projects](https://weblog.west-wind.com/posts/2018/Feb/18/Accessing-Configuration-in-NET-Core-Test-Projects)
*   [https://github . com/dot net/core/blob/master/release-notes/releases . CSV](https://github.com/dotnet/core/blob/master/release-notes/releases.csv)