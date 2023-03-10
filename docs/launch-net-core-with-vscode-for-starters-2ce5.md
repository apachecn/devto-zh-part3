# 发射。Net Core 和 VSCode

> 原文：<https://dev.to/0xshetty/launch-net-core-with-vscode-for-starters-2ce5>

将 VSCode 用于。你需要 Omnisharp 扩展，这样我们就可以像在 Visual Studio 中一样调试我们的应用程序。
使用 [dotnet new 命令](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-new?tabs=netcore21)我们创建了一个 web 和一个 API 项目

*dot net new web app appname . web
dot net new web API appname . API*

创建一个解决方案，以便将所有项目包装在一个地方

*dotnet new sln appname
dotnet sln add appname.web appname.api*

在构建 Web 和 API 项目之后，我们需要创建 launch.json，VScode 引用它来构建和调试应用程序。关于做这些项目的完整参考，请考虑这个[伟大的资源](https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-mvc-app/?view=aspnetcore-2.2)。

您也可以始终使用 VSCode 终端来运行上述命令，还要注意，您可以像这样打开多个终端的同一个 VSCode 实例[。](https://code.visualstudio.com/docs/editor/integrated-terminal#_managing-multiple-terminals)

[![](img/f7cef20c7080a1c1385bf5904a197ebb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--39eZNlHH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bhvammqwn0ne89v4lfh0.png)

通过点击 add configuration 并选择。Net core web app。我们需要添加两个这样的配置，一个用于 web，另一个用于 web api。
对于 web api，您需要在运行应用程序时启动浏览器，禁用

*launch browser . enabled:false*

我们需要用不同的端口更新我们两个应用程序的 Url

环境。ASPNETCORE _ URLS:"[https://localhost:5001](https://localhost:5001)"

项目的构建任务在 preLaunchTask 引用的 tasks.json 中定义。在下面的 launch.json 示例中，我创建了两个任务，在运行应用程序之前执行。这就是我们执行多个任务的方式，我们需要将每个任务与在 *preLaunchTask* 中提到的第一个任务名称链接起来，其余的在 tasks.json *dependsOn* 属性中。

F5 并在调试模式打开的情况下启动！

下面是完整的 launch.json 和 tasks.json