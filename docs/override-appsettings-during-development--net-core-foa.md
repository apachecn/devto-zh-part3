# 在开发期间覆盖 appSettings。网络核心

> 原文：<https://dev.to/ankitvijay/override-appsettings-during-development--net-core-foa>

不久前，我曾写过如何在传统 ASP.NET 应用程序的开发过程中覆盖 appSettings。

最近，我们开始开发一个 ASP.NET 核心应用程序，也遇到了类似的挑战。我们的开发人员在不同的操作系统上工作(Windows 和 Mac)。它们有不同的本地连接字符串和应用程序设置。我们遇到了与之前相同的问题:我们如何确保不在源代码控制中存储开发人员特定的应用程序设置/连接字符串。

与传统 ASP.NET 框架相比，ASP.NET 核心中的配置非常先进且可扩展。我们有多种方法来解决这个问题。从文档中:

> ASP.NET 核心中的应用配置基于由*配置提供者*建立的键值对。配置提供程序从各种配置源将配置数据读入键值对:
> 
> *   蓝色钥匙保险库
> *   命令行参数
> *   自定义提供程序(已安装或已创建)
> *   目录文件
> *   环境变量
> *   内存中。网络对象
> *   设置文件

此外，ASP.NET 核心提供了以下[两种推荐的](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-2.2&tabs=windows)方法，用于在开发期间存储应用机密:

*   环境变量
*   秘密经理

上述两种方法的一个问题是设置存储在 ide 之外。此外，由于这些设置覆盖了`appSettings.json file`，这可能会在调试时造成混乱，因为开发人员可能没有意识到设置被覆盖了。

## 我们的方法

对于我们的项目，我们在寻找没有任何额外开销的最简单的解决方案。对我们来说，最简单的解决方案是使用`appSettings.Developement.json`文件来存储本地设置，并通过添加到`.gitignore`来将其从源代码控制中排除(我们使用 Git)。

然而，我们已经有了一个单独的“`Dev`”环境，我们首先在这里部署我们的应用程序，然后再推进到`Test`和`Live`环境。使用默认的`Developement`环境来存储本地设置可能会让开发人员感到困惑。

结果，我们在项目中添加了一个新的 **`appSettings.Local.json`** 文件。然后，这个设置文件被添加到`.gitignore`中，以便从源代码控制中排除这个文件。

这里有一个例子`appSettings.Local.json`

然后，我们修改了我们的`launchsettings.json`并更新了`ASPNETCORE_ENVIRONMENT`以指向`Local`而不是`Development`

最后但同样重要的是，我们更新了`Startup.json`以在开发时使用开发者异常页面。

就是这样！这种方法帮助我们将所有的设置保存在一个地方(在我们的 IDE 中),结果证明这是一个非常好的解决方案。

希望这个提示对你有用。请在下面的评论区分享你的想法。

post [在开发过程中覆盖 appSettings。网芯](https://ankitvijay.net/2018/12/28/override-appsettings-during-development-net-core/)最早出现在[大家好，我是 Ankit](https://ankitvijay.net) 。