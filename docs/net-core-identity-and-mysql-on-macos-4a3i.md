# 。MacOS 上的 NET Core、Identity 和 MySQL

> 原文：<https://dev.to/ijason/net-core-identity-and-mysql-on-macos-4a3i>

我最近经历了在 MacOS 上开发一个. NET 核心 MVC 网站的痛苦。这篇文章旨在帮助你开始使用身份认证和 MySQL 作为你的后台数据库。

我假设你有。已安装 NET Core SDK。如果你不确定你的[安装了最新版本的](https://dotnet.microsoft.com/download/dotnet-core)。当前版本是 2.2，因为 3.0 还在预览中。为了确认你已经正确安装，打开终端并输入`dotnet --info`。我只是试了一下，发现我甚至没有运行最新版本的。网芯。我一直以为我运行的是 2.2 版，但实际上我运行的是 2.1.9 版。这解释了我所有的问题。

[![.NET Core version](img/25afef15d88f76903ca41fce57a389fb.png)](https://i1.wp.com/blog.ijasoneverett.com/wp-content/uploads/2019/06/coreversion.png) 

在我们开始之前，这是我目前使用的环境:

*   。网芯 2.1.9(见谅)
*   Mac 版 Visual Studio 2019(免费社区版)
*   MAMP 专业版(MySQL 版)

当然你不需要 MAMP 专业版来运行 MySQL。你可以直接安装 MySQL，但我过去一直在做一些 PHP 开发，MAMP Pro 当时就派上了用场。

好了，我们开始吧！打开终端并运行以下命令:

`mkdir IdentityProject`

`cd IdentityProject`

`dotnet new mvc --auth Individual`

您可以通过 Visual Studio 界面创建一个新项目，但从 2019 年 6 月 2 日起，您无法选择验证类型。命令行是可行的方法。现在我们已经创建了项目，让我们安装所需的 Nuget 包。

1.  微软。实体框架工作核心工具
2.  微软。实体框架工作核心设计
3.  pomero . entityframworkcore . MySQL

在终端中运行以下命令，确保您仍然在 IdentityProject 目录中:

`dotnet add package Microsoft.EntityFrameworkCore.Tools`

立即我遇到了版本冲突错误。这是因为我没有运行最新版本的。网芯。如果你使用的是最新版本，那么你应该不会有这个错误。至少我是这么假设的。

[![Version conflict error](img/3a0263bdb46689d8fd181dd026cd0350.png)](https://i0.wp.com/blog.ijasoneverett.com/wp-content/uploads/2019/06/Screen-Shot-2019-06-02-at-4.01.37-PM.png)

好了，让我们移除这个包并安装正确版本。

`dotnet remove package Microsoft.EntityFrameworkCore.Tools`

`dotnet restore`

为了省去你弄清楚需要安装哪个版本的麻烦，我就直接告诉你我需要安装 2.1.8 版本。经过反复试验后，我明白了这一点，所以让我们再试一次。

`dotnet add package Microsoft.EntityFrameworkCore.Tools --version 2.1.8`

`dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.1.8`

`dotnet add package Pomelo.EntityFrameworkCore.MySql --version 2.1.4`

`dotnet restore`

现在我们应该可以出发了在 Visual Studio 中打开项目。你面前应该有一个普通的 MVC 项目。

[![Visual Studio for Mac](img/e4b2701e879175a6a62d13b111084d7b.png)](https://i1.wp.com/blog.ijasoneverett.com/wp-content/uploads/2019/06/Screen-Shot-2019-06-02-at-4.17.38-PM.png)

下一步是建立你的 MySQL 数据库。打开 MySql 并创建一个新的数据库。让我们称之为 IdentityDB。现在我们有了一个没有表的空白数据库。在 Visual Studio 中打开`appsettings.json`,将连接字符串添加到数据库中。

```
{
  "ConnectionStrings": {
    "DefaultConnection": "server=127.0.0.1;port=8889;database=IdentityDB;user=root;password=<YOUR_PASSWORD>;CharSet=utf8;SslMode=none;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
} 
```

让我们对`Startup.cs`做一些修改，从使用 SqlLite 切换到 MySQL。

```
services.AddDbContext<ApplicationDbContext>(options =>
                options.UseMySql(
                    Configuration.GetConnectionString("DefaultConnection"))); 
```

现在我们需要创建初始迁移，以允许实体框架为我们创建身份表。从终端和 IdentityProject 目录中运行以下命令:

`dotnet ef migrations add InitialCreate`

您应该能够从 Visual Studio 中看到您的迁移文件。通常它们位于 Data > > Migrations 下，但有时 Migrations 文件夹会在根目录下创建。不管怎样，文件应该在那里。现在运行以下命令在数据库中创建身份表:

`dotnet ef database update`

我收到了以下错误:

[![EF Error](img/8f31daeb5ecf2709b396e162dba06352.png)](https://i2.wp.com/blog.ijasoneverett.com/wp-content/uploads/2019/06/Screen-Shot-2019-06-02-at-4.50.18-PM.png)

我认为这是由于运行了一个旧版本的 MySql 造成的。我目前使用的是 MySql 5.5，最新版本是 8.0，所以我远远落后。要修复此错误，请从您的迁移文件夹中打开`CreateIdentitySchema.cs`。找到创建`AspNetUsers`表的代码。 ***注意:如果你没有`CreateIdentitySchema.cs`文件，那么代码位于`InitialCreate.cs`下。

还要记住这些文件都是以数字为前缀的。***

定位以下直线:

```
LockoutEnd = table.Column<DateTimeOffset>(nullable: true), 
```

并将其更改为:

```
LockoutEnd = table.Column<DateTimeOffset>(nullable: true, type: "DateTime"), 
```

我们应该可以走了。在我们执行迁移之前，打开您的数据库，删除在执行第一次迁移时可能已经创建的任何表。这是为了确保你不会遇到任何冲突。现在运行下面的代码:

`dotnet ef database update`

如果没有收到任何错误，那么数据库中应该有所有的身份表。

[![Identity Tables](img/a968ec559a998ad381c0a186ce158061.png)](https://i1.wp.com/blog.ijasoneverett.com/wp-content/uploads/2019/06/Screen-Shot-2019-06-02-at-5.11.32-PM.png)

应该就是这样！从 Visual Studio 运行项目并注册一个新用户。

**奖金**

我在工作时注意到一件事。NET Core 是我经常看到的缺失的帐户控制器。自从。NET Core 2.1，所有这些页面现在都是 Razor 类库的一部分。如果您需要访问这些页面来修改 UI 或更改后端代码，您需要将它们移植出去。为此，您首先需要安装代码架子:

`dotnet tool install -g dotnet-aspnet-codegenerator`

一旦安装完毕，将包添加到您的项目:

`dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.8`

`dotnet restore`

如果您使用的是最新版本的，您可以省略`--version 2.1.8`。网芯。现在运行以下命令，列出可以添加到项目中的页面。

`dotnet aspnet-codegenerator identity --listFiles`

您可以通过运行以下命令来选择要在项目中公开的文件:

`dotnet aspnet-codegenerator identity --files="Account.Manage.ChangePassword;Account.Register;Account.ResetPassword;Account.ResetPasswordConfirmation"`

您现在可以查看您在区域> >身份> >页面> >帐户下选择的所有身份文件