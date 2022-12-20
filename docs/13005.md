# 在中使用机密管理器保护敏感数据。网络核心

> 原文：<https://dev.to/0xshetty/protecting-sensitive-data-using-secret-manager-in-net-core--44m1>

意外地将存储在应用程序配置中的敏感数据推入源代码控制中！！！谢天谢地，这发生在很多人身上，不仅仅是你😜。当你有一个公司犯错误或其他任何事情时，感觉很好。

因此，这里是如何确保这种情况不会再次发生。有多种[方法](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2#providers)来保护，我们现在要学习的方法是使用#dotnetcore 中的 Secret Manager 工具。

我们所要做的就是使用 *`dotnet user-secrets`* 这个命令。在使用它之前，请删除您试图隐藏的属性的敏感值。

在我的例子中，它是 *appsettings.json* 文件中的*" twilioauttoken ":"*。

现在在终端中运行下面的命令，

`dotnet add package Microsoft.Extensions.SecretManager.Tools`

我们有运行*用户机密*命令所需的必要工具。

让我们在* *中为我们的项目创建一个键值存储库。像这样的 csproj* 文件

```
<PropertyGroup>
  <UserSecretsId>LocalKeyVault</UserSecretsId>
</PropertyGroup> 
```

Enter fullscreen mode Exit fullscreen mode

创建存储库后，我们现在可以使用此命令添加/删除键值

`dotnet user-secrets set TwilioAuthToken <secretcodegoeshere>`

如果您有这样分组的设置

```
"Telegram":{  "TelegramBaseAddress":"https://api.telegram.org/bot",  "TelegramAPIKey":""  } 
```

Enter fullscreen mode Exit fullscreen mode

以这种方式使用命令

`dotnet user-secrets set "Telegram:TelegramAPIKey" "<secretcodegoeshere>"`

搞定了。现在使用`configuration["TwilioAuthToken"]`访问它，从`%AppData%\Microsoft\UserSecrets\LocalKeyVault\secrets.json`得到配置值，我确信它不会被错误地检入😜

——最初在 [Bitsmonkey](https://bitsmonkey.blogspot.com/2019/02/protecting-sensitive-data-using-secret.html)
上发表——照片由迈卡·威廉姆斯在 Unsplash 上拍摄