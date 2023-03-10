# 在中创建一个角度为 6 的站点。网络核心部分 1

> 原文：<https://dev.to/cstuartedwards/create-an-angular-6-site-in-net-core-part-1-198c>

除了几年前看过 AngularJS，我还没有用过 Angular，所以这是我第一次尝试。我们将从头开始创建一个 angular 6 web 应用程序。我有意不在 visual studio 中使用 Angular 模板，因为我喜欢理解项目是如何构造的。

让我们开始吧:

1.  在 visual studio 中新建一个**ASP.NET 核心 Web 应用**
    [![](img/ef34b6daad9e97fc62945fe350469953.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T3HUOFpo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25203/3-1.png)

2.  选择**清空**项目模板
    [![](img/43d83b119ed2d5bf9bb59ffc1b6768de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tT_cIUN8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25203/3-2.png)

3.  安装节点，这样我们就有了 npm(节点包管理器):[https://nodejs.org/en/](https://nodejs.org/en/)

    **更新**:您可能需要在下一步之前重新启动 visual studio。

4.  我们需要安装 angular cli，为此在 visual studio 中打开**包管理器控制台**并运行以下命令:

    ```
    npm install -g @angular/cli

    ```

    当这完成时，你可能会得到一些警告…我不知道这是好是坏:-|但一切似乎都是后来的工作。
    [![](img/5eb6119da2473e4161739132ae843989.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--JbNTC7AF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25203/3-4.png)

5.  现在我们需要创建角度应用程序。为此，运行 ng new 命令，但首先我们需要导航到**。包管理器控制台中的 Web** 文件夹。默认情况下，软件包管理器控制台运行在。sln 文件已定位。

    在**软件包管理器控制台**中输入 CD[您的项目名称]:

    ```
    cd AngularSix.Web

    ```

6.  运行以下命令创建 angular 应用程序:

    ```
    ng new ClientApp

    ```

    这将需要几分钟的时间，因为 node_modules 文件夹的大小必须增长大约 200 MB

    [![](img/4937f11821aedb5f18c43a552454d58c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xNg46c6E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25203/3-6.png)

    在 visual studio 中，ClientApp 文件夹下应该有一整批新文件

    [![](img/9e285ade6a3c46a173e7c2d293a72597.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C2po73Dh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25203/3-7.png)

7.  在 Startup.cs 中，将 **ConfigureServices** 方法更改为以下内容:

    ```
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSpaStaticFiles(configuration =>
        {
            configuration.RootPath = "ClientApp/dist";
        });
    } 
    ```

8.  在**配置**方法中，将其更新为:

    ```
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseStaticFiles();
        app.UseSpaStaticFiles();

        app.UseSpa(spa =>
        {
            // To learn more about options for serving an Angular SPA from ASP.NET Core,
            // see https://go.microsoft.com/fwlink/?linkid=864501
            spa.Options.SourcePath = "ClientApp";

            if (env.IsDevelopment())
            {
                spa.UseAngularCliServer(npmScript: "start");
            }
        });
    } 
    ```

9.  按下播放键，去喝杯咖啡，第一次构建需要一点时间。
    [![](img/df784efb4f465301cfc91646e16b6db0.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--DvPJJXJO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25203/3-9.png)

耶！！

[![](img/45f6042f007613ef20a72d9c25dcc1a5.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--DIMDkI1a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25203/3-10.png)

检查角钻头是否正常工作。当应用程序仍在运行时，打开 app.component.ts 文件

[![](img/ac4b4ff998dc2e206962854311bdafb9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nUYm5kC4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25203/3-11.png)

修改 AppComponent 标题并保存更改。看网页。
[![](img/cabe003971437132c82f92bb6cb9c7ad.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--2nof6Jux--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25203/3-12.png)

OMG！网页会自动更新。

由于对 Angular 一无所知，我做了英雄之旅教程([v6.angular.io/tutorial](https://v6.angular.io/tutorial))，它涵盖了 Angular 的基础知识。我建议您按照教程来构建我们刚刚创建的 ClientApp。当您回来时，您的 ClientApp 应该如下所示:

**注意:**当您在英雄之旅模块中创建组件时，您需要位于 ClientApp 目录中。

[![](img/6e0fb88e6017ba4256716807111aa5bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YpSRaSTA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://charles.stuartedwards.me.uk/media/Blog%2520Images/Post%25203/3-13.png)

如果你没有做角度教程，那么代码可以在这里找到[stackblitz.com/angular/vvxldjlmnna](https://stackblitz.com/angular/vvxldjlmnna)

等一下，在哪里？网芯！这个帖子已经写了很长时间了，所以我要写了。下一个帖子里的 net core bit。

[第二部分](http://charles.stuartedwards.me.uk/blog/create-an-angular-6-site-in-net-core-2-1-from-scratch-pt2)

> 最初发表于我的个人博客:[在。网芯 2.1 从零开始第 1 部分](http://charles.stuartedwards.me.uk/blog/create-an-angular-6-site-in-net-core-2-1-from-scratch-pt1)

#### 附加资源:

*   [www . code project . com/Articles/1255288/How-to-Set-Up-Environment-for-Angular-6-with-NET-C](https://www.codeproject.com/Articles/1255288/How-to-Set-Up-Environment-for-Angular-6-with-NET-C)
*   [arunendapaly . com/post/how-to-create-a-app-with-angular-6-and-ASP.NET-core-2.0](http://arunendapally.com/post/how-to-create-an-app-with-angular-6-and-asp.net-core-2.0)
*   [angular.io/guide/quickstart](https://angular.io/guide/quickstart)