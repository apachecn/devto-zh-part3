# 使用 Azure Devops 将 Wyam 站点部署到 Netlify。

> 原文：<https://dev.to/mrbliz/deploying-a-wyam-site-to-netlify-with-azure-devops-4boi>

Netlify 是托管静态网站的一个很好的工具。免费的话，你可以托管一个拥有自定义域名和 HTTPS 的网站，如果你点击了黑客新闻的首页，你也不用担心会被缩放。对于大量的静态站点生成器，Netlify 甚至可以为你生成你的站点。只需将您的代码推送到 Netlify 支持的源代码库，Netlify 就会获取更改，生成并部署您的站点。

不幸的是，目前 Netlify 没有可用的 dotnet，因此无法生成一个 [Wyam](https://wyam.io/) 站点，所以在 Netlify 支持之前。NET Core，您将需要一个单独的 CI 步骤来生成站点，然后将一个 zip 文件推送到 netlify。我们将使用的 Ci 工具是微软 [Azure Devops](https://azure.microsoft.com/en-gb/services/devops/)

在这个过程中，我们将遵循以下步骤。

*   创建 Wyam 站点
*   发布到 Github
*   创建 Netlify Api 密钥和站点 ID
*   设置构建管道以创建生成站点的 zip 文件
*   将站点推送到 netlify。
*   重复进行分段

假设您已经安装了 dotnet core，并使用`dotnet tool install -g Wyam.Tool`安装了 wyam global 工具，请执行以下操作。

`mkdir wyamblog`

`cd wyamblog`

`wyam new --recipe Blog`

`wyam -p -w`

当导航到默认地址 [http://localhost:5080](http://localhost:5080) 时，您应该会看到博客的首页

现在创建一个新的 git repo 和 remote，并将其作为您选择的源代码库(我将使用 Github)

如果您还没有帐户，请在 Netflify 注册一个，然后导航到`OauthApplications`并创建一个新的个人访问令牌，并记住将其保存在某个地方以供将来参考。

如果你还没有 Azure Devops 账户，去创建一个(开源免费使用)，然后创建一个项目。

导航到侧面菜单中的管道，并创建一个管道。

出于这篇文章的目的，我们将使用新的 Azure Devops YAML 设计器
我们现在需要指定我们的代码在哪里(顺便提一下，我使用 Github 而不是 Azure repos 的原因是，如果 Netlify 在未来支持构建 wyam 站点，它将无法访问 Azure repos 中的代码)。您需要授予 Azure devops 访问您的存储库的权限。

我将把 Azure Pipelines 应用程序安装到 github 上，并允许它访问 Github 中的 wyamblog 项目。

选择 starter pipeline 模板，并用下面的代码片段替换模板中的代码。

```
trigger:
- master

pool:
  vmImage: 'Ubuntu-16.04'

variables:
  configuration: debug
  platform: x64

steps:
- task: DotNetCoreInstaller@0
  displayName: Install .NET Core SDK
  name: install_dotnetcore_sdk
  enabled: true
  inputs:
    packageType: 'sdk'
    version: '2.2.101'

- script: dotnet tool install -g Wyam.Tool
  displayName: Install Wyam

- script: wyam
  displayName: Build Site 

- task: ArchiveFiles@2
  displayName: Zip Site
  inputs:
    rootFolderOrFile: '$(Agent.BuildDirectory)/s/output' 
    includeRootFolder: true
    archiveType: 'zip'
    archiveFile: '$(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip' 
    replaceExistingArchive: true

- script:  >-
      curl
      -H 'Authorization: Bearer $(netlifyAccessToken)' 
      -H 'Content-Type: application/zip'
      --data-binary '@$(Build.BuildId).zip'
      https://api.netlify.com/api/v1/sites/$(netlifySiteId)/deploys
  workingDirectory: '$(Build.ArtifactStagingDirectory)'
  displayName: 'Upload to Netlify' 
```

这将执行以下操作

*   当提交到指定的分支时，使用 ubuntu VM 触发构建。
*   安装相关的 SDK
*   安装 Wyam 全局工具。
*   执行 Wyam 工具并构建站点
*   压缩输出目录
*   使用 cURL 将输出目录推送到 Netlify

如果我们保存脚本并运行它。它将失败，因为脚本中有两个 Azure Devops 找不到的变量

`$(netlifyAccessToken)`

`$(netlifySiteId)`

我们已经创建了访问令牌，但是我们需要一个站点 id。导航到`https://app.netlify.com/account/sites`,这将显示你 Netlify 账户中的网站列表。

Netlify 允许您从 GIT repo 创建站点，但它也允许您通过拖放一堆文件来创建站点。如果您将输出文件夹拖到拖放区，它将立即为我们创建一个站点。您可以验证站点是否工作，但我们最感兴趣的是站点 ID。导航到站点设置选项卡，复制 API ID 并将其保存在某个地方。

接下来在 Azure devops 中，在 YAML 编辑屏幕上，点击“在可视化设计器中编辑”，然后在下一个屏幕上转到变量选项卡。

我们需要创建两个新变量。第一个名为`netlifyAccessToken`的函数复制您之前在 Netlify 中创建的访问令牌的值，并通过单击锁将其设置为 secret。这将确保令牌被加密，并且不会在任何日志中打印出来。其次，创建一个名为`netlifySiteId`的变量，并从 Netlify 的 API ID 中复制值。现在保存并排队构建。完成后，返回 Netlify，您应该在 deploys 选项卡上看到您的构建的新记录。

## 暂存

这是我们的主分支排序，但如果你想预览一些变化之前，他们去生活呢？也许看看一个新的主题是什么样子？我们可以通过为临时分支创建一个新的管道来实现这一点。

首先，通过从 github 中提取，确保您的本地存储库中有`azure-pipelines.yml`文件。这是 Azure Devops 添加到您的 repo 中的文件，其中包含构建管道步骤。

接下来签出一个新的分支，我称之为“staging ”,编辑 yml 文件，这样新构建的触发器就是您刚刚创建的分支的名称。对你的网站做一个视觉上的改变，以区别于主页上的内容，然后提交并推动这些改变。

通过再次拖放在 Netlify 中创建新站点，并从 settings 选项卡中复制新的 API ID。回到 Azure Devops，点击侧边栏中的“Builds”。

从右边的菜单中，选择“克隆”并给克隆的管道一个新名称。我在我的末尾加上了分支机构的名称。

如果你点击管道的第一步。如果你没有重命名它，它可能会被称为“获取资源”。我们需要更改分支，构建将从该分支获得其对 staging 的更改。

接下来单击 variables 选项卡，并将`netlifySiteId`变量更改为您的临时站点的新 API ID。

保存一个构建并对其进行排队，您应该会看到您的新更改得到了传播。

现在，让我们通过在 staging 分支上进行更多的更改来测试触发器的工作情况。在发布您的更改和构建之后，您应该可以看到它们正在运行！

## 总结

在这篇文章中，我们使用 Wyam 创建了一个静态站点，并在 Azure Devops 中创建了一个构建管道，以避开 Netlify 尚不支持 dotnet 的事实。希望在几个月内，他们会添加这种支持，这篇文章将变得多余，但同时，我希望你发现它有用。

这是我第一次在 Azure Devops 中使用 YAML 创作体验，所以我在这个过程中学到了很多。

本文最初发布于 [blizard.io](https://www.blizard.io/posts/deploying-a-wyam-site-using-azure-devops-to-netlify)