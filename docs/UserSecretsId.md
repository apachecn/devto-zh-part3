# 如何在我们的. NET Core 应用程序中存储“秘密”(不会误将它们发送到 github 视频

> [https://dev . to/campus MVP/as-guarding secrets in our-net-core-safe-to-github-per-error video-28n 7](https://dev.to/campusmvp/como-guardar-secretos-en-nuestras-aplicaciones-de-net-core-sin-peligro-de-enviarlos-a-github-por-error-video-28n7)应用程序中的秘密

如果您曾经使用过 ASP.NET Core，您会发现它有一个非常有趣的选项叫做**【用户机密】**，它允许我们生成一个`secrets.json`存储在本地，并且允许我们使用公共源代码控制作为 GitHub，我们就不会

在 ASP.NET Core 中，要使用此功能，只需转到 Visual Studio 中项目的上下文菜单即可:

[![La imagen muestra el menú contextual de un proyecto, con el submenú de administrar secretos de usuario resaltado](img/3f26f2a606450925adbec6c421e6cf43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xG2rl2XK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.campusmvp.es/recursos/image.axd%3Fpicture%3D/2019/1T/secretos-net-core/MenuSecretos.png)

我们还添加了 JSON 中我们希望保持私密的部分，这样我们就不必把它们写在‘t0’上了。

有了这个，如果我们去`.csproj`，就会看到增加了这样一行:

```
<UserSecretsId>2a2fc01e-0fc6-4b74-b887-0217454d7b14</UserSecretsId> 
```

它只是指定给项目的 GUID(唯一全局标识符)。这样，就可以访问“用户秘密”并使用该专用 JSON 进行开发。实际上，它所做的是将一个文件与 JSON 一起保存，除了在路径`%APPDATA%\Microsoft\UserSecrets\ <user_secrets_id>\secrets.json`中设置的文件({ t0 })。

不幸的是，这一强大的功能，**并未在控制台应用程序**(您必须等到 release 3 才能看到它们是否实现)。不管怎么说，稍微摸摸一些东西，**我们就能让我们的控制台应用程序使用**的秘密功能，只需 5 个简单的步骤。

## 步骤 1:我们手动创建了“secrets.json”文件

首先，我们在路径“`%APPDATA%\Microsoft\UserSecrets`”内创建了一个新文件夹，例如名为“`AppConsolaCampusMVP`”。在其中，我们创建了一个名为“`secrets.json`”的新文本文件。

> 记住该文件夹的名称很重要，因为我们稍后将需要它。

[![secrets.json file](img/2b737866a0e987fd78696b1140732981.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XtxuHJLv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.campusmvp.es/recursos/image.axd%3Fpicture%3D/2019/1T/secretos-net-core/json.PNG)

## 步骤 2:我们写了一个“秘密”配置参数

在文件中，我们将保存一个示例“secreta”设置，在本例中是一条简单的消息:

```
{
  "Mensaje": "CampusMVP"
} 
```

## 步骤 3:加入必要的相依性

在我们的. Net Core 应用程序中，我们添加了软件包:

`Microsoft.Extensions.Configuration.UserSecrets`。

这可以从 Visual Studio 中的项目文件包管理器用户界面或使用“
”的文件包管理器控制台实现

```
PM->Install-Package Microsoft.Extensions.Configuration.UserSecrets 
```

## 步骤 4:我们实例化配置对象

而由此，我们只剩下一个能让我们读出秘密设置的对象，并实现界面`IConfiguration` :

```
IConfiguration config = new ConfigurationBuilder()
     .AddUserSecrets("AppConsolaCampusMVP") //Nombre de la carpeta que hemos creado
     .Build(); 
```

这使我们能够添加我们创建的秘密配置文件，利用类“`ConfigurationBuilder`”的可扩展性。

## 步骤 5:我们检查结果

有了这一点，如果在我们应用的`IConfiguration`方法中，在创建`IConfiguration`之后加入:

```
Console.WriteLine($"Mensaje desde los secretos de usuario: '{config["Mensaje"]}'"); 
```

我们可以看到，我们已经使控制台应用程序与 web 应用程序一样使用用户密码。

[![Resultado de ejecutar la aplicación de consola, mostrando el "mensaje secreto"](img/1c7295a8e69849c6459471f5a7244879.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iCsUiNyJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.campusmvp.es/recursos/image.axd%3Fpicture%3D/2019/1T/secretos-net-core/result.PNG)

## 程序完成

```
using Microsoft.Extensions.Configuration;
using System;

namespace AppConsolaCampusMVP
{
    class Program
    {
        static void Main(string[] args)
        {
            //Obtenemos el nombre de la carpeta que hemos creado
            IConfiguration config = new ConfigurationBuilder()
                    .AddUserSecrets("AppConsolaCampusMVP")
           .Build();

            Console.WriteLine($"Mensaje desde los secretos de usuario: '{config["Mensaje"]}'");
            Console.Read();
        }
    }
} 
```

通过这些简单的步骤，我们成功地建立了一个开发配置，该配置不会在我们的版本控制中公开，因为它单独存储在用户的相关文件夹中。

为了更好地了解整个过程，我们还将在实践中观看视频:

[https://www.youtube.com/embed/FJGIz9JFzTU](https://www.youtube.com/embed/FJGIz9JFzTU)