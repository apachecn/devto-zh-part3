# 如何跑两个？使用 Visual Studio 代码调试的. net 核心应用程序？

> 原文：<https://dev.to/0xshetty/how-to-run-twonet-core-apps-with-debugging-on-using-visual-studiocode-2g4l>

开发 web 应用程序时，我们用*分隔应用程序。web 是最初的服务器端应用程序，我们提供*。api 应用程序，它将帮助我们获取/更新客户端操作的数据，并避免页面重载。

这是。我正在开发的 NET Core 应用程序也有类似的问题，我想使用 VSCode 来调试我的应用程序。

我们是这样做的，首先，我们需要创建一个解决方案，以便将 API 和 Web 项目放在一个地方

*dotnet 新 SLN-n appname*
T3】dot net SLN 添加 src/appname/appname . web . csprojT5】T6】dot net SLN 添加 src/appname/apname . API . csproj

打开“launch.json ”,点击添加配置和添加，然后选择

*。NET Core Launch (web)*

不，继续重命名为 web 或 web-api，并添加一个不同于 web 配置的开发 URL“env”属性

*" env ":{*
*" ASPNETCORE _ ENVIRONMENT ":" Development "，*
*" ASPNETCORE _ URLS ":"[https://localhost:5001](https://localhost:5001)"*
* }

现在只需用调试模式运行每个配置，调试愉快！

添加完整的 launch.json
以供参考