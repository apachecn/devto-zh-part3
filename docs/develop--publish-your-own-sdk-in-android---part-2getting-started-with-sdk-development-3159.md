# 在 Android 中开发和发布您自己的 SDK——第 2 部分(SDK 开发入门)

> 原文：<https://dev.to/mohitrajput987/develop--publish-your-own-sdk-in-android---part-2getting-started-with-sdk-development-3159>

在前一部分中，我们学习了 AAR 文件的结构和 gradle 依赖关系。在本教程中，您将学习-

*   如何开发新的模块/库
*   如何混淆模块

如果你没看过上一篇文章，可以从[这里](https://dev.to/mohitrajput987/develop--publish-your-own-sdk-in-android---part-1know-an-aar-file-g9k)开始读。如果你已经阅读过它或者对 AAR 文件和 gradle 依赖关系有很好的了解，你可以在本教程的帮助下开始开发你的 SDK。

## 发展

使用 Android Studio 中的以下步骤创建一个新的 Android 库项目-

1.  创建一个新的 Android 项目或使用一个现有的项目，您将在其中创建一个模块。要创建一个新模块，进入**文件→新建→新建模块**。你会看到下面的对话框- ![Create new module](img/f8b0ce41b8c734f9eb7c5bd41de0d0d3.png)

选择**安卓库**，点击**下一步**。

1.  点击下一步按钮后，你会看到一个小对话框，询问关于库- ![Fill Details](img/1cb3fde86d73a8380e0d8d42e56836fd.png)的一些细节

填写库名、模块名和包名。选择支持的最低 SDK 版本并点击**完成**。您将看到新创建的模块添加到项目中，如下所示-
[![New module created](img/98a79b687db4c49b99b5edb4c86e9d90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vswcv6Oc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/development/created-module.png)

1.  在您的应用程序级别中添加以下行`build.gradle`，以便在您的项目中包含新创建的模块-

```
implementation project(":preference") 
```

用您的模块名替换`preference`。

有许多架构可以用来构建 Android 应用程序，如 MVP、MVVM 等。但是当您没有任何视图时，选择这些架构并不是一个好主意。有不同种类的图书馆-

*   一些图书馆包含视图和用户界面元素，如脸书，谷歌地图；
*   有时你的库是 REST APIs 的抽象，比如微软认知 SDK
*   有时你会提供小工具或自定义视图来美化用户的应用程序，如 Android 支持库、设计库等。

开发过程中的另一个挑战是从用户处获取 API 密钥来认证 SDK。您还需要通知用户它是否是正确的密钥。Google 在 Manifest 中获取 API key，而 Microsoft Cognitive SDK 在其 REST 客户端的构造函数中获取它。准确地说，决定好的架构、API 密钥管理、异常处理将取决于你的需求。

## 混淆视听

Proguard 是用来缩小，混淆，优化代码。Proguard 对你的库来说是必要的，它可以删除未使用的代码，并使逆向工程变得不那么困难。
库的 Proguard 规则不同于普通应用程序。如你所知，Proguard 使用无意义的名字重命名类、变量和方法。您希望保留开发人员将调用的那些方法和类的名称。您将需要测试和验证从生成的 AAR 文件混淆代码。

1.  在您的项目浏览器中，您将看到两个 proguard 文件，一个用于“app”模块，另一个用于您现在创建的新库模块(在我们的例子中，它是“preference”)-
    [![Proguard File](img/1eab3e306153b0f9673aba3fd29e707e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M23N5Sbp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/development/proguard-location.png)

2.  复制并粘贴该 [Proguard 文件](https://github.com/mohitrajput987/android-utility/blob/master/preference/proguard-rules.pro)的内容，该文件包含库特定的 Proguard 规则。您可以根据需要定制规则。

3.  现在打开模块的`build.gradle`文件，按如下方式启用 proguard

```
buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    } 
```

现在同步项目，您可以在主项目中使用这个库模块中的任何方法或类。因此，我们终于完成了本库的开发和模糊处理。
现在，下一步是创建这个库的 gradle dependency，我们将在本文的[第 3 部分](https://dev.to/mohitrajput987/develop--publish-your-own-sdk-in-android---part-3create-gradle-dependency-4nam)中看到，这是本系列的最后一部分。
请看[如何创建梯度依赖](https://dev.to/mohitrajput987/develop--publish-your-own-sdk-in-android---part-3create-gradle-dependency-4nam)。