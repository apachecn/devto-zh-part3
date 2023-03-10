# 为侧面加载的 UWP 应用启用自动更新

> 原文：<https://dev.to/mhmd_azeez/enabling-automatic-updates-for-sideloaded-uwp-apps-6cp>

最近，我们想为一个客户开发的应用程序启用自动更新。这个应用程序是在 UWP 开发的，它被下载到客户的电脑里。尽管微软在 Windows 的`1803`版本中增加了自动更新功能，但我们还是遇到了一些问题:

*   这不太可靠
*   除了一个简单的自动更新策略，规定它应该多久检查一次更新，我们对它没有太多的控制。
*   故障排除非常困难，因为没有明确的方法来找出为什么自动更新程序不能正常工作。

所以我找到了 2016 年的一篇文章，并决定对其进行改进，并将其作为一个 T2 的 nuget 包。该库是开源的，可以在 [GitHub](https://github.com/dwrandaz/AutoUpdateComponent) 上获得。

## 如何使用

1.  首先，[配置 IIS 服务器并创建一个 web app](https://docs.microsoft.com/en-us/windows/uwp/packaging/web-install-iis) 。您也可以在示例 web 应用程序中查看[。](https://github.com/Dwrandaz/AutoUpdateComponent/tree/master/Sample/CustomStore)
2.  在 UWP app 中安装 nuget 包: [`Dwrandaz.AutoUpdateComponent`](http://nuget.org/packages/Dwrandaz.AutoUpdateComponent) 。
3.  将应用程序的最低版本设置为`1803`
4.  打开主 app 的包清单`.appmanifest`文件，声明一个 app 服务:
    *   名称:默认值为`Dwrandaz.AutoUpdate`。然而，您可以将其更改为您喜欢的任何名称，但是您应该注意，这个名称很重要，如果您不使用默认名称，它应该传递给`AutoUpdateManager.TryToUpdateAsync`。
    *   切入点:`Dwrandaz.AutoUpdateComponent.UpdateTask`
5.  右键单击包清单`.appmanifest`文件，然后单击`View Code`。
6.  添加此名称空间声明:`xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"`
7.  在`IgnorableNamespaces`上加上`rescap`，例如:`IgnorableNamespaces="uap mp rescap"`
8.  在`Package`标签中，确保这些元素存在:

```
<Capabilities>
    <Capability Name="internetClient" />
    <rescap:Capability Name="packageManagement" />
</Capabilities> 
```

有关更多信息，请查看示例应用清单文件。

1.  示例用法:

```
var path = "http://localhost:5000/install/AwesomeApp.appinstaller";
var info = await AutoUpdateManager.CheckForUpdatesAsync(path);
if (!info.Succeeded)
{
    // There was an error in getting the update information from the server
    // use info.ErrorMessage to get the error message
    return;
}

if (!info.ShouldUpdate)
{
    // The app is already up-to-date :)
    return;
}

// You can use info.MainBundleVersion to get the update version

var result = await AutoUpdateManager.TryToUpdateAsync(info);
if (!result.Succeeded)
{
    // There was an error in updating the app
    // use result.ErrorMessage to get the error message
    return;
}

// Success! The app was updated, it will restart soon! 
```

要了解更多信息，请看一下[示例应用程序](https://github.com/Dwrandaz/AutoUpdateComponent/blob/master/Sample/SampleApp/MainPage.xaml.cs#L35)。

## 创建更新包

1.  确保您选择了`Release`配置
2.  右击主 app 项目，点击`Store` > `Create App Packages...`
3.  选择`I want to create packages for sideloading.`并勾选`Enable automatic updates`复选框
4.  点击`Next`
5.  勾选`version`下的`Automatically Incremenent`复选框。
6.  选择`Generate App bundle`下的`Always`
7.  点击`Next`
8.  写下更新位置路径并选择`Check every 1 Week`或更多，这样本机自动更新机制就不会与我们的自动更新机制混淆。
9.  点击`Create`
10.  将`.appinstaller`、`.index.html`和包文件夹上传到在`AutoUpdateManager.CheckForUpdatesAsync`中使用的路径下的 web app 中。