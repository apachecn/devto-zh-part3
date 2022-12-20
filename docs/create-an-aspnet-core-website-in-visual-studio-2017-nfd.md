# 在 Visual Studio 2017 中创建 ASP.NET 核心网站

> 原文：<https://dev.to/cstuartedwards/create-an-aspnet-core-website-in-visual-studio-2017-nfd>

> 这篇文章是[创建一个果园核心站点并使用 Azure DevOps](http://charles.stuartedwards.me.uk/blog/create-an-orchard-core-site-and-deploy-using-azure-devops) 系列部署的一部分。

我们将在 visual studio 2017 中从头开始创建一个. net 核心网站。

1.  在 visual studio 中创建新项目。
    [![](img/1fa22df7cd8287220ce6e70174c27116.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--a5kg9YwL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25201/1-1.png)

2.  在 **Web** 部分下选择【ASP.NET T2】核心 Web 应用模板。
    [![](img/5f3f9287f79a365d50122aa849a10867.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1PwyeHxc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25201/1-2.png)

3.  设置**名称**、**解决方案名称**和**位置**您希望解决方案创建的位置
    [![](img/cdac41636e693d68de25908cae9b2854.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tt7sTCxh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25201/1-3.png)

4.  确保**。选择网芯**和 ASP.NET 芯**2.1**。然后选择**空**模板，点击确定。
    [![](img/084c2dfbc022583042386aeccc7e8c82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QYcHffuu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25201/1-4_2b.png)

5.  一旦设置完成，你会看到下面的项目结构:
    [![](img/8ff594172c60af6ce626186449c95c51.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wvWofJlp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25201/1-5.png)

6.  单击播放。
    [![](img/8fd6ecb5d45f87c10432b2cde07ba588.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--_wKvYDs---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25201/1-6.png)

7.  然后嘣！完成了。
    [![](img/31bf4e3003c7579ff00317bf27c0d46f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--3BBDkI29--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25201/1-7.png)

打开 Startup.cs 文件来看看神奇之处:

[![](img/3182fdfd7acbf5bd1523a08f9dd72196.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DTmnXzm1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25201/1-8.png)

厉害吧！....好的，它在做一个反应。写这不是一个伟大的方式来建立网站，但你在这里看到的是非常基础的。网芯。接下来你要做什么完全取决于你自己，例如，你可以把它变成一个 MVC 网站或者一个 Web API 网站或者一个 Orchard 核心网站。以了解更多关于您可以用做什么的信息。Net Core 查看[微软对 ASP.NET Core 的介绍](https://docs.microsoft.com/en-us/aspnet/core/?view=aspnetcore-2.1)

在我们添加 Orchard Core 之前，我们将把我们的代码添加到源代码控制中:[在 Azure DevOps (VSTS)中将项目添加到源代码控制中](http://charles.stuartedwards.me.uk/blog/add-project-to-source-control-in-azure-devops-vsts)

> 原发表于我的个人博客:[在 Visual Studio 2017 中创建一个 ASP.net 核心网站](http://charles.stuartedwards.me.uk/blog/create-an-orchard-core-site-and-deploy-using-azure-devops)