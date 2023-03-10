# 在 Azure DevOps (VSTS)中将项目添加到源代码管理

> 原文：<https://dev.to/cstuartedwards/add-project-to-source-control-in-azure-devops-vsts-1kci>

> 这篇文章是[创建一个果园核心站点并使用 Azure DevOps](http://charles.stuartedwards.me.uk/blog/create-an-orchard-core-site-and-deploy-using-azure-devops) 系列部署的一部分。

这就是你如何把你的新项目添加到 Azure DevOps 的方法，这个项目叫做 Visual Studio Team Services (VSTS)。

1.  在 visual studio 右下角点击**添加到源代码控制**链接，然后选择 **Git** 。

    [![](img/4756810a240f472448e2a51b704671e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UfzKuWNj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25202/2-1.png)

2.  团队资源管理器侧面板将打开 git 服务选项。
    [![](img/664ae93777aa052dc2e5e2bc046206fd.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--bNkI-YY1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25202/2-2.png)

3.  我们正在添加到 Azure DevOps 项目，所以你需要一个帐户:
    [visualstudio.microsoft.com/team-services/](https://visualstudio.microsoft.com/team-services/)

4.  点击团队服务下的 **Push Git Repo** 。
    T3![](img/f0fb093f470c54e24f844aed8aab0863.png)T5】

5.  输入您的帐户详细信息和存储库名称，然后单击发布存储库。

    [![](img/bb7c6c5484c365d7108273b1a22bc8fb.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--qxOOR8N4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25202/2-5.png)

6.  这将把你所有的代码推到一个新的 Azure DevOps repo。

搞定了。

您可以通过进入**团队资源管理器**选项卡:

[![](img/771d6e2c4773588bbc3fea399adcdf32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dznrM3S1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25202/2_6.png) 从 visual studio 获得 Azure DevOps repo

点击 **Azure DevOps** 栏目下的链接:

[![](img/bca2ce36887cec137c3b5fbbffe74b5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5is3rVRU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25202/2-7.png)

从这里你可以看到所有你推送到回购的代码。
[![](img/4af9d3562a6f54809445de91a128db80.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ATeRnyNf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25202/2-8.png)

Azure DevOps 能做的还有很多，我们稍后会回来设置一个构建管道。

但是首先让[使用 Nuget](http://charles.stuartedwards.me.uk/blog/setup-orchard-core-in-visual-studio-using-nuget) 在 Visual Studio 中设置 Orchard Core

> 原发表于我的个人博客:[在 Visual Studio 2017 中创建一个 ASP.net 核心网站](http://charles.stuartedwards.me.uk/blog/add-project-to-source-control-in-azure-devops-vsts)