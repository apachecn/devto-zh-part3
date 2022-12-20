# 使用 Azure DevOps 部署基于 Python 的 Azure 功能

> 原文：<https://dev.to/simonwaight/deploy-python-based-azure-functions-using-azure-devops-46dp>

8 月份，微软宣布 Azure Functions 中的 Python 支持已普遍可用(GA)，这意味着它已准备好用于生产场景。

在这篇文章中，我将简要介绍如何使用 Azure Pipelines(Azure devo PS 的一部分)快速部署 Python 函数。

#### 构建和打包

现在，您可以选择在 Azure Pipelines 中使用预构建的模板来打包 Python Azure 函数。

[![Python Functions Build Template](img/656099961758e06713ba92e43549e595.png "Python Functions Build Template")](https://res.cloudinary.com/practicaldev/image/fetch/s--ZPEzXev8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://siliconvalve.files.wordpress.com/2019/01/2019-10-23_18-48-10.png)

选择此选项后，您将拥有恢复 requirements.txt 文件中的任何 Python 模块所需的全部内容，并确保安装了任何 Azure 函数扩展。最终的封装会被压缩，以便在发布中使用。

如果像我一样，你将多个独立的 Azure 函数放入一个 Azure Git Repo 中，那么你将需要修改三个构建任务，以确保在运行命令时选择子文件夹。

首先，我们需要确保我们恢复了 Azure Functions 扩展，因此我们将添加一个“cd”命令并切换到正确的文件夹。

[![Extensions subfolder](img/d7ead1ad36ea97a90dd8260035cccff9.png "Extensions subfolder")](https://res.cloudinary.com/practicaldev/image/fetch/s--8LyKNojt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://siliconvalve.files.wordpress.com/2019/01/2019-10-23_20-08-09.png%3Fw%3D1024)

然后，我们必须确保根据子文件夹中的`requirements.txt`恢复 Python 模块。

[![Subfolder for requirements.txt](img/e4adf9a2c54c46c053d8a3587dc26901.png "Subfolder for requirements.txt")](https://res.cloudinary.com/practicaldev/image/fetch/s--_-U0CYnE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://siliconvalve.files.wordpress.com/2019/01/2019-10-23_20-14-25.png%3Fw%3D1024)

最后，我们需要编辑存档文件任务，确保您选择了与上一步相同的子文件夹。

[![](img/7aaf084a69f6b03c5287da4ab499bd91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GqOozVBX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://siliconvalve.files.wordpress.com/2019/01/2019-10-23_19-12-01.png%3Fw%3D1024)

是的，如果您运行这个构建，您将得到一个准备好部署的 zip 文件。

#### 释放到天蓝色

我们现在可以创建一个使用标准 Azure 函数模板的 Azure Pipelines 版本(如下所示)。在部署到已经为 Python 函数设置好的主机环境中时，不需要使用特殊的步骤。

[![Release Template](img/a1f09d813afec96993d03fd38886da74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cDi2Add4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://siliconvalve.files.wordpress.com/2019/01/2019-10-23_20-17-31.png)

一旦运行了发布版，您的功能就部署好了！如果您需要同时部署应用设置，您可以使用“部署 Azure Function 应用”任务的“应用程序和配置设置”部分来指定要部署的设置。

就是这样——比预览版中的这个功能简单多了！