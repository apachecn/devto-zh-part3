# React js ➡ Azure DevOps ➡ Azure App 服务

> 原文：<https://dev.to/jakob_vdh/react-js-azure-devops-azure-app-service-56oo>

# 如何使用 Azure DevOps 构建你的 React js Web App 并部署到 Azure

这篇文章一步一步地指导你使用 Azure DevOps 将 React js Web App(或任何其他基于 Node.js 的 Web App)部署到 Azure Web App 服务的简单过程。

## ✅要求:

1.  Azure 帐户([如果你是学生，请点击此处了解如何获得免费的 Azure 资源](https://link.medium.com/ANGMMY2P5T)
2.  正在运行的 Azure Web 应用服务
3.  React Web 应用程序
4.  [Azure devo PS 服务帐户](https://azure.microsoft.com/de-de/services/devops/)

## 构建和部署

在 Azure DevOps 门户中，转到`Pipelines`页面的`Builds`选项卡。在那里，您将看到您以前构建的概述，并且稍后还会看到您的 React Web 应用程序构建的构建状态。

创建新的构建管道，并选择 web 应用程序存储库的位置/服务。如果您没有使用 Azure Repository，并且之前没有授权您的访问权限，您将必须授权对存储库的访问权限。

[![Screenshot of the possible repository services](img/dc202e27d30d27107fb81a05a52b459f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wn99bCPL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Jakob-vdH/BlogPosts/master/devTo/reactAzureDevOps/media/01-repository.jpg)

然后从列表中选择您的 web 应用程序的存储库(在截图中，我使用的是 Azure 存储库，因此该菜单的外观可能会因您的存储库而异)。

[![Screenshot of the menu to choose your single repository](img/3c4e4b3f1d8ec06d56b9e31cc5dd3bec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6UgNeLax--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Jakob-vdH/BlogPosts/master/devTo/reactAzureDevOps/media/02-chooseRepo.jpg)

选择存储库之后，Azure DevOps 试图自动创建一个 Yaml 文件。在我们的例子中，它首先定义了我们将使用哪种 VM 池来构建我们的 web 应用程序。然后，它定义了我们的构建步骤，从安装 Node.js 开始，继续安装我们的 web 应用程序的依赖项(`npm install`)，然后运行我们的 web 应用程序的构建命令(`npm run build`)。
最终，标准输出被设置为归档工作目录，换句话说就是我们的构建。

[![Screenshot of the automatically generated azure-pipelines Yaml file](img/3bef21b85c4ecceec8b10d8da08b45b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LZ2r51hT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Jakob-vdH/BlogPosts/master/devTo/reactAzureDevOps/media/03-yaml1.jpg)

我们的目标是直接触发 Azure Web App 服务的部署，所以我们需要将最后一个任务改为:

```
- task: AzureRmWebAppDeployment@3
  displayName: 'Azure App Service Deploy: <YourWebAppNameHere>'
  inputs:
    azureSubscription: <YourAzureSubscriptionId>
    WebAppName: <YourWebAppNameHere>
    Package: '$(System.DefaultWorkingDirectory)/build' 
```

你仍然需要输入你的 Azure 订阅 Id 和你的 web 应用的名称，这样 Azure DevOps 就知道你想在哪里部署这个版本。此外，我们在最后一行定义了我们的构建所在的位置，以确保只部署我们的最终构建。

[![Screenshot of the edited azure-pipelines Yaml to deploy to Azure file](img/b6769aed85ebcaa99f136a7db16b4b03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BPnV0aZb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Jakob-vdH/BlogPosts/master/devTo/reactAzureDevOps/media/03-yaml2.jpg)

您刚刚创建的 Yaml 文件将作为`azure-pipelines.yml`添加到存储库中。因此，如果您需要配置额外的步骤，比如测试，您只需编辑存储库中的 Yaml 文件。此外，如果你在 Azure DevOps 中在线编辑文件，它会向你显示要添加的任务列表，你也可以使用 UI 配置每个任务。

[![Screenshot of the task list provided by Azure DevOps](img/5f96c11051bade03280220dbba7ee250.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K5RqOUGM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Jakob-vdH/BlogPosts/master/devTo/reactAzureDevOps/media/04-deploy1.jpg)

[![Screenshot of deploy to Azure task configuration using a GUI](img/5bad768c4f88c8c8c678941efbc1a8c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DIthTSxO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Jakob-vdH/BlogPosts/master/devTo/reactAzureDevOps/media/04-deploy2.jpg)

当然，您可以(对于更大的项目应该)明确地划分构建和部署。现在，每当您将新的提交合并到您的生产分支时，都会触发一个新的构建。

[![Screenshot of an automatically triggered build](img/7b453a6347ee9c1334288457b9fa8525.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y-QJ91jc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Jakob-vdH/BlogPosts/master/devTo/reactAzureDevOps/media/05-trigger.jpg)

这就是运行基本的构建和部署流程所需要做的一切，祝您愉快！🐱‍💻