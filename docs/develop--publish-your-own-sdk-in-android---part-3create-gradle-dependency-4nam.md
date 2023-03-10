# 在 Android 中开发和发布你自己的 SDK 第 3 部分(创建梯度依赖)

> 原文：<https://dev.to/mohitrajput987/develop--publish-your-own-sdk-in-android---part-3create-gradle-dependency-4nam>

这是*“开发&在 Android 中发布你自己的 SDK”*系列的第三部分也是最后一部分。我们已经了解了什么是 AAR 文件，以及它与 JAR 文件有何不同。我们还学习了如何创建一个新的库项目并对其进行模糊处理。如果你还没有读完最后两部分，你可以在-

*   [第 1 部分-了解一个 AAR 文件](https://dev.to/mohitrajput987/develop--publish-your-own-sdk-in-android---part-1know-an-aar-file-g9k)
*   [第 2 部分 SDK 开发入门](https://dev.to/mohitrajput987/develop--publish-your-own-sdk-in-android---part-2getting-started-with-sdk-development-3159)

有很多平台可以托管你的 Android 库并创建它的 gradle 或 maven 依赖项。您可以选择 JitPack、JCenter 或 Maven Central。我更喜欢 JCenter。这里解释了选择 JCenter 而不是其他的原因。

# 为什么是 JCenter？

选择 jCenter 的优点是，

*   甚至对于私有的 GIT 库也是免费的。事实上，你的项目是否在 GIT 中并不重要。
*   默认情况下，Android Studio 中的任何新项目都包含 jCenter，您一定在 Android 项目的根级 build.gradle 文件中看到过这些行

```
repositories {
    jcenter()
} 
```

Enter fullscreen mode Exit fullscreen mode

使用您的库的开发人员不必添加任何额外的配置，除了在应用程序级 build.gradle 文件中使用实现行的 gradle 依赖项。

*   第二件事是，jCenter 是世界上最大的 Java 存储库。它是 maven central 的超集。这意味着，maven central 中可用的东西在 jCenter 中已经可用了。
*   将你的库上传到 Bintray 是非常容易的。不需要签名或者做任何复杂的事情，就像你在 Maven Central 上做的那样。

因此，开始向 JCenter 发布您出色的 Android 库吧。

# 在 JFrog Bintray 上创建账户

因为 jCenter 托管在 JFrog Bintray 上，所以通过点击[注册](https://bintray.com/signup/oss)在上面创建一个新帐户。
你可以使用 GitHub、Twitter、Google 注册，或者直接输入你的电子邮件 ID 和其他信息。创建要为其创建存储库的组织。一旦你完成注册并创建了一个组织，登录你的账户并选择组织，你会看到下面的仪表板-
[![Dashboard](img/13220afdf3c9ec320830000594e9c37c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6-DZEDgW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/bintray/click-on-add-new-repo.png)

您将在顶部看到组织名称、存储库(我们将在下一步中创建)和您已填写的其他详细信息。

# 创建新的存储库

下一步是创建存储库。点击仪表板上的**添加新库**按钮；你会看到下面的表格-
[![Add New Repository](img/d23e2de14662095cc7fd7419d05e14b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jkMqvaQ6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/bintray/create-new-repo-form.png)

您可以为您的存储库提供任何名称，但是推荐使用“maven ”,因为它描述了它是所有 Android 和 Java 库的 maven 存储库。在类型中选择 **Maven** 。提供许可证、描述并选择头像。

创建后，您将被重定向到个人资料控制面板。您可以在“拥有的存储库列表”部分看到您的新存储库名称。点击存储库标题，您将进入以下存储库页面-
[![Repository Page](img/8c4424e5334583d43b1e91a9ce91f44c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8kZpGyU_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/bintray/click-on-add-new-package.png)

# 添加新的包

下一步是创建一个包。Bintray 中的包是为每个模块项目创建的。如果您的项目中有多个模块，您需要为它们创建单独的包。点击存储库页面上的**添加新包**按钮，您将得到如下表单-
[![Add New Package](img/8f61633a20fcaee3bba6a0d896cd959f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iiCfeHE---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/bintray/package-form.png)

包名一般是你项目的包，即`com.otb.utility`。
如果您在同一个组 id 下有多个项目，为了便于理解，您可以在包名中添加您的工件 id，即`com.otb.utility:preference`。
如果你有库项目的私有 GIT repo，可以提供公共样例 app GitHub 资源库的链接。你可以在这里看到示例实用程序项目[。填写所有详细信息，然后转到软件包页面。你会看到如下的包页面-](https://github.com/mohitrajput987/android-utility) [![Package Dashboard](img/f140e31ddce3a98bc035abbd1ff35e71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E9HQrawA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/bintray/package-dashboard.png)

# 创建新版本

每次创建库的新版本时，您都需要从 Bintray dashboard 创建版本。点击**新版本**会看到一个小表格如下:
[![Add Version](img/53abfc9b4abeedd360c2724b3f517c37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OeF_-34O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/bintray/add-version-form.png)

填写版本名称，即 0.0.1 及其描述，并点击**创建版本**。创建版本后，您将被重定向到软件包详细信息页面。现在转到版本页面。您将看到以下空白版本页面，因为您的库尚未发布-
[![Version Dashboard](img/487afda459149b02309f6c5a69af9b60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UTLqlWHu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/bintray/version-dashboard.png)

# 获取 API 密钥

API 密匙用于认证你的 AAR 文件上传到 Bintray。进入纸盒面板，点击右上角的你的名字。您将看到以下下拉菜单，其中包含与配置文件相关的选项。
点击**管理组织**，将转到以下编辑个人资料页面-
[![Get API Key](img/42336175892275d749dfe9bb003ed893.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wGRxeLqk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/bintray/get-api-key.png)

点击左侧面板中的最后一个选项 *API 键*。复制 API 密钥并随身携带。上传库时您将需要它。
我们已经完成了纸盒的设置。

*又到了跳库源码的时候了。*

# 在模块中配置 Bintray

在设置完 Bintray 包和版本之后，你需要在 Android Studio 中配置你的库。
首先，在依赖项部分的根级 build.gradle 文件中添加以下插件-

```
classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.8.0'
classpath 'com.github.dcendents:android-maven-gradle-plugin:2.1' 
```

Enter fullscreen mode Exit fullscreen mode

下一步是配置库细节，即组 id、工件 id、许可证等。您还需要添加 Bintray 凭据、软件包和版本详细信息。将以下信息添加到库的 build.gradle 文件-

```
apply plugin: 'com.android.library'
apply plugin: 'com.github.dcendents.android-maven'
apply plugin: 'com.jfrog.bintray'

ext {
    bintrayRepo = 'maven' // Name of the Bintray repo you created
    bintrayName = 'com.otb.utility:preference' // Bintray package name

    publishedGroupId = 'com.otb.utility' // Group ID of the SDK which will be added in implementation line of gradle
    artifact = 'preference' // Artifact ID of the SDK which will be added in implementation line of gradle
    libraryVersion = '0.0.1' // Version of the library you created in Bintray

    libraryName = 'Preference Utlity' // It is generally the module name
    libraryDescription = 'Utlity to use shared preference in your app.' // Small description of the library

    siteUrl = 'https://github.com/mohitrajput987/android-utility'
    gitUrl = 'https://github.com/mohitrajput987/android-utility.git'

    developerId = 'ourtechnobytes'
    developerName = 'Our Techno Bytes'
    developerEmail = 'info@ourtechnobytes.com'

    licenseName = 'The Apache Software License, Version 2.0'
    licenseUrl = 'http://www.apache.org/licenses/LICENSE-2.0.txt'
    allLicenses = ["Apache-2.0"]

    //Bintray Credentials
    Properties properties = new Properties()
    properties.load(project.rootProject.file('local.properties').newDataInputStream())

    bintrayUsername = properties.getProperty('BINTRAY_USERNAME')
    bintrayUserOrg = properties.getProperty('BINTRAY_ORG')
    bintrayApiKey = properties.getProperty('BINTRAY_API_KEY')
}

android {
    ...
}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    ...
}

apply from: 'bintray.script' 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 ext{...}一节中对这些变量的解释。你可以从[这里](https://github.com/mohitrajput987/android-utility/blob/master/preference/build.gradle)访问这个文件。
看看凭证部分，我更喜欢将凭证保存在`local.properties`文件或环境变量中，因为在代码中保存凭证是不安全的。
你的`local.properties`文件会是这样的-

```
ndk.dir=/home/mrajput/Android/Sdk/ndk-bundle
sdk.dir=/home/mrajput/Android/Sdk
BINTRAY_USERNAME=your_bintray_username
BINTRAY_ORG=your_bintray_organization
BINTRAY_API_KEY=your_bintray_api_key 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以看到上面 gradle 文件的最后一行。这里您需要应用 Bintray 脚本的路径，该脚本会将库文件上传到 Bintray。你可以从[这里](https://github.com/mohitrajput987/android-utility/blob/master/preference/bintray.script)下载这个脚本，并复制到模块的目录中。

# 编译库模块

完成所有配置和凭证设置后，就该生成 AAR 和其他文件了。打开一个终端，进入你的项目的根目录，运行下面的命令-

```
 gradlew install 
```

Enter fullscreen mode Exit fullscreen mode

如果您看到任何与 javadoc 相关的错误，有一个小的解决方法(如果您找到任何其他解决方法，请在评论中分享)。在根级 build.gradle 中添加以下行-

```
subprojects {
   tasks.withType(Javadoc).all { enabled = false }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想出任何解决这个错误的方法，请在评论区发表。

之后，再次运行安装命令。构建生成过程将会启动，您将会在成功时看到以下消息-

```
BUILD SUCCESSFUL in 46s
27 actionable tasks: 17 executed, 10 up-to-date 
```

Enter fullscreen mode Exit fullscreen mode

# 上传库到 Bintray

成功构建之后，就该把你的库上传到 Bintray 了。我们可以在同一个终端中运行下面的命令-

```
 gradlew bintrayUpload 
```

Enter fullscreen mode Exit fullscreen mode

成功上传后，您将会看到类似于上面提到的`BUILD SUCCESSFUL` like 消息。

现在转到 Bintray 上的版本页面，你会看到你的库的详细信息，即 maven 和 gradle 的依赖设置。

# 向 jCenter 添加库

如果您添加了刚刚在任何其他项目中创建的 gradle 依赖项，您将得到类似“未能解析 com.yourlibraryname ...”的错误。这是因为您的包只添加到 Bintray repo，而不是 jCenter。
要在 jCenter 中添加包，点击**添加到 jCenter** 按钮如下图右下方所示-
[![Add to JCenter](img/c464d14e336ab11ce254e017186a1281.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZaO5WV6l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/bintray/click-on-add-to-jcenter.png)

您将被重定向到以下表格，该表格需要您的包裹的详细信息-
[![Add Package info](img/426d513546cac2c78ba5ee535e289746.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GH9MdUqK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/bintray/fill-jcenter-detail.png)

写一份关于你的图书馆的简介并提交申请。提交后，jCenter 团队审查并批准该包，大约需要 **2-3 小时到**2-3 天。当他们批准后，您将在 Bintray 上收到通知。添加到 jCenter 按钮将不存在，您将看到以下 jCenter 徽章-
[![](img/fdefcf7d609dc019b89cbfa6de65be50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f4KEelAw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/bintray/added-to-jcenter.png)

如果您的包没有添加到 jCenter 中，但是您仍然希望集成您的库，那么您可以通过显式指定您的存储库 URL 来实现。为此，请点击蓝色的**设置我！** *按钮然后点击**使用 Gradle** 解析工件如下所示-
[![Set me up](img/7c73a01fd8c36722d093acb70eb40523.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pCsHZGRP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mohitrajput987/media-repository/master/sdk-development/bintray/set-me-up.png)

复制这个 maven URL 并添加到 repositories 部分。

*恭喜你！您的库已经可以使用了。*

# 结论

创建梯度依赖似乎是一个非常漫长的过程。但是不用担心，大多数步骤都是一次性的。我们已经了解了为什么选择 jCenter 而不是其他平台。我们已经看到了如何创建一个帐户并在 Bintray 中设置一个包。最后，在代码中添加了一些配置，并使用两个命令发布了库。我希望你会喜欢这篇文章，它将有助于你的 SDK 开发。如果你有任何疑问，可以在评论区提问。另外，在评论中分享你的图书馆的链接。下一篇文章再见。