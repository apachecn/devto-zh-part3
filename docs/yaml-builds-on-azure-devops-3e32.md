# Yaml 构建于 Azure DevOps 之上

> 原文：<https://dev.to/funkysi1701/yaml-builds-on-azure-devops-3e32>

我使用 Azure DevOps(或 VSTS 或 VSO 等)已经有一段时间了，其中一个很棒的功能是在每次签入时自动构建。这通常被称为 CI(持续集成)构建。

最近，我开始尝试使用 YAML 文件来创建我的构建，而不是使用 web 用户界面来创建我的构建。

## 为什么？

你可能想知道为什么要努力学习 YAML 语法，当你可以在 Azure DevOps 中创建构建，然后忘记它。

主要是因为构建会随着时间的推移而改变，你不应该就这么忘记它。如果某个东西随着时间的推移发生了变化，那么您可能希望对它进行版本控制，或者查看以前的版本。

假设您创建了一个用. net 核心 web 服务替换. net 4.7 web 服务的 pull 请求。如果您有 CI 构建，此 PR 将失败，因为它不会构建。如果您首先更改了构建，其他任何正在进行的构建都将失败。在这种情况下，您想要的是与那个分支或者 PR 相关联的构建。在合并此更改之前的任何版本都将继续工作，在此更改之后的任何版本也将工作。

## 如何？

您如何开始使用 YAML 版本？首先要确保 YAML 版本是打开的，在我写这篇文章的时候，我相信它们仍然是一个你可以打开或关闭的功能。查看预览功能，并确保它们已打开。

接下来查看您现有的构建，并单击查看 YAML 链接。这将向您展示您现有构建的一个示例 YAML 文件。你可以将它保存为 azure-pipelines.yml 并签入到你的项目的根目录。您还可以单击 add new build pipeline 选项，这将为您提供一些模板。

YAML 文件由一系列通常称为任务的构建步骤组成，在配置参数或构建代理之前有一些设置。关于文件语法的详细文档可以在[这里](https://docs.microsoft.com/en-us/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema)找到。

对于我的移动应用程序，我的 YAML 文件包括下载 nuget 包，构建解决方案，构建具有所需设置的特定项目，运行 powershell 或其他脚本进行设置，最后将构建结果作为工件发布，以便可以在任何版本中使用。

## 安全了！

避免将密码和安全密钥提交到源代码控制中。我发现你可以通过 Azure DevOps 上传安全文件，然后在构建开始时添加一个下载安全文件的步骤。这允许在构建过程中使用安全文件，但是任何有权访问源代码的人都不能查看文件的内容。

我发现经常需要考虑如何实现这一点，但是保证密钥和秘密的安全通常是可能的。