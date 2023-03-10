# 如何在 React Native 中管理试运行和生产环境

> 原文：<https://dev.to/calintamas/how-to-manage-staging-and-production-environments-in-a-react-native-app-4naa>

作为 React 本地开发人员，您的生活将变得更加轻松。至少我的孩子是这样，当我学会如何在 React Native 中管理暂存和生产环境时。它不会改变 React 原生应用的外观、感觉或销售方式。但是它为您节省了大量开发人员的时间。

*我最初在[大约 25 岁的博客](https://around25.com/blog/manage-staging-and-production-environments-for-react-native-app/)* 上发布这个。

当只需要做一两处修改时，修改配置**是一件轻而易举的事情。你把`API host`切换到生产模式，砰，你就完成了。当你不得不改变`API host`，设置支付网关`key`，支付网关`host`，设置通知`.plist`文件，更新 crashlytics `key`和更新分析`key`时，事情只会变得更加困难和容易出错。每次你手工制作的时候都这样做，在某些时候我保证你会错过一些东西。**

为了避免麻烦，我们将使用环境变量。

为了向`react-native`公开环境变量，我将使用 [react-native-config](https://github.com/luggit/react-native-config) 库。通过这样做，我使配置变量远离代码。最有可能的是，这些变量在不同的环境中会有所不同，而代码则不会。

首先，我要将库添加到项目中。当我写这篇文章时，`react-native-config`的最新版本是`0.11.7`。然后，我必须在项目的`root`中创建 3 个 env 文件:一个用于本地环境，简称为`.env`，另外两个称为`.env.staging`和`.env.production`。

```
// .env
IS_PRODUCTION=false
API_HOST=https://api.staging.foobar.com

// .env.staging
IS_PRODUCTION=false
API_HOST=https://api.staging.foobar.com

// .env.production
IS_PRODUCTION=true
API_HOST=https://api.foobar.com 
```

现在，我只在配置中保留了`API host`,旁边还有一个`IS_PRODUCTION`标志。我们稍后会用到它。显然，`https://api.staging.foobar.com`和`https://api.foobar.com`都是虚构的🙂。

现在，为了将这些变量用于我的 React 本机应用程序，下面是我的配置文件通常的样子:

```
// src/config/index.js

import env from 'react-native-config'

const config = {
  api: {
    host: env.API_HOST,
    timeout: 20000
  }
};

const API_HOST = config.api.host;

export {
  API_HOST
}

export default config 
```

仅此而已。默认情况下，`react-native-config`从`.env`文件中读取，但是通过下面的命令可以从运行项目的任何文件&中读取:

```
$ ENVFILE=.env.staging react-native run-ios 
```

到目前为止一切顺利，但我希望我的构建过程尽可能自动化。所以手动指定`.env`文件是不可接受的。避免这是我们接下来要做的。

## iOS 设置

在 iOS 上，我将利用**方案**的概念。Xcode 方案定义了一个目标集合来构建&一个在构建时使用的配置。默认的可以在 Xcode 的菜单栏找到:

```
Product -> Scheme -> FooBar 
```

其中 *FooBar* 是我的应用程序的名称。

[![show ios product scheme](img/5e8745171ff88ba8b11fd5dfb6199b41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RbYSYqua--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://around25.com/blog/conteimg/2019/02/1.png)

我要做的是复制那个默认方案并创建两个新方案:一个用于**试运行**，另一个用于**生产**。我们将使用默认的**本地**环境。您可以转到:
进行操作

```
Product -> Scheme -> Edit Scheme -> Duplicate Scheme 
```

[![xcode 2](img/f1ca8780b381b71b687fbf709ef293d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DKX9aMG4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://around25.com/blog/conteimg/2019/02/2.png)

我将称它们为`FooBar.staging`和`FooBar.production`。当我们稍后编写自动化构建脚本时，命名将变得非常重要。

我将选择`FooBar.staging`并返回到`Edit Scheme`，以确保我的方案加载了我想要的`.env`文件。在这里，在`Build -> Pre-actions`上，我添加了一个脚本来完成这个任务。

[![add pre build script](img/69aeabfebc490d981654ae15527a6c0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hrNnDvBh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://around25.com/blog/conteimg/2019/02/3.png)

[![add pre build script part 2](img/52091106af0e545602e5c752bcff6c2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--26IbIuez--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://around25.com/blog/conteimg/2019/02/4.png)

我正在为`FooBar.production`经历完全相同的过程。

现在，我已经为 iOS 做好了一切准备。每当我构建应用程序时，通过选择一个方案，我就可以加载正确的环境变量。恭喜，我们去安卓吧。

## 安卓设置

对于 Android，我们有[构建类型](https://developer.android.com/studio/build/build-variants?utm_source=android-studio#build-types)。在`android/app/build.gradle`中，在`buildTypes`下我们有两个默认的构建类型，**发布**和**调试**。

```
buildTypes {
    release {
        minifyEnabled enableProguardInReleaseBuilds
        proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
        signingConfig signingConfigs.release
    }
    debug {
        debuggable true
    }
} 
```

和以前一样，默认使用本地环境变量。

对于新的构建类型，添加这些行:

```
stagingrelease {
    initWith release
}
productionrelease {
    initWith release
} 
```

其中的每一个都将被用于制作一个*发布类型的*构建，所以请确保[在继续之前生成一个签名密钥](https://facebook.github.io/react-native/docs/signed-apk-android)。

**命名在这一步非常重要**。首先，您必须在构建变体名称中使用令牌 *release* 来应用常规的发布构建行为，更多信息请参见[这里](https://chase-seibert.github.io/blog/2016/12/09/react-native-android-staging-variant.html)。第二，当我最初设置名称为 *releaseStaging* 和 *releaseProduction* 时，我遇到了问题，因为没有加载正确的`.env`文件。我谷歌了一下，在这里找到了问题的解决方案。

现在，在同一个`android/app/build.gradle`文件的最顶端，添加这个:

```
project.ext.envConfigFiles = [
        debug: ".env",
        release: ".env",
        stagingrelease: ".env.staging",
        productionrelease: ".env.production"
] 
```

您可能会在发布版本的 Proguard 中遇到问题。解决方法是[这里的](https://github.com/luggit/react-native-config#problems-with-proguard)。

要使用上面定义的构建类型之一运行应用程序，在构建之前，转到`View -> Tool Windows -> Build Variants`并选择变体(在新显示的窗口中):

[![android build variants window](img/80e95cd6d59af8c04dd0a16aaff4e224.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vyxG9WUM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://around25.com/blog/conteimg/2019/02/5.png)

Android 也是如此。接下来，我们将通过创建`fastlane`脚本来进一步自动化我们的构建过程。

> 我的目标是为这两个平台都创建一个单行的构建脚本。

## 奖励:快车道脚本

首先，我们将在[安装 fastlane](https://docs.fastlane.tools/#getting-started) 并完成设置。`fastlane init`必须在 ios 或 Android 项目中运行，因此我们将在 iOS 文件夹中运行，然后将整个`fastlane`文件夹移动到项目的根目录。

在`fastlane/Fastfile`中，我在 ios 平台下创建了一个通道，将构建上传到 TestFlight。浪子自动加载。运行脚本时传递的 env 文件。如果我们这样做:

```
$ fastlane ios beta --env=production 
```

它将加载`.env.production`文件。所以我们都准备好了。为了测试这一点，我将添加几行代码，只打印出环境变量和加载的文件。

```
// fastlane/Fastfile

platform :ios do
  desc "Submit a new build to TestFlight"
  lane :beta do
    app_identifier = "com.app.identifier"

    api_environment = "staging"
    if ENV["IS_PRODUCTION"] == "true"
      api_environment = "production"
    end

    ENV["ENVFILE"]=".env.#{api_environment}"

    puts "API_HOST: #{ENV['API_HOST']}"
    puts "IS_PRODUCTION: #{ENV['IS_PRODUCTION']}"
    puts "ENVFILE: #{ENV['ENVFILE']}"
    end
  end 
```

如果一切正常，我们应该会看到这样的输出:

```
[18:16:52]: Loading from './/../.env.production'
[18:16:52]: Driving the lane 'ios beta' 🚀
[18:16:52]: API_HOST: https://api.foobar.com
[18:16:52]: IS_PRODUCTION: true
[18:16:52]: ENVFILE: .env.production 
```

在进行新的构建之前，我们可能希望增加构建号和/或构建版本。将这些行添加到文件:

```
increment_version_number(
    xcodeproj: './ios/FooBar.xcodeproj',
    bump_type: "patch",
    # bump_type: "minor",
    # bump_type: "major",
    # version_number: "1.0.0"
)
increment_build_number(
  xcodeproj: './ios/FooBar.xcodeproj',
  # build_number: '74'
) 
```

现在，要创建一个构建，添加下面几行。请记住，在文章的开头，我提到过**方案的命名**在后面会很重要。嗯，这就是为什么:

```
gym(
    xcodeproj: './ios/FooBar.xcodeproj',
    scheme: "FooBar.#{api_environment}"
) 
```

最后，我希望我的脚本也将构建上传到`TestFlight`，所以我这样做:

```
pilot(
    app_identifier: app_identifier,
    email: "itunesconnect_email",
    first_name: "itunesconnect_first_name",
    last_name: "itunesconnect_last_name",
    ipa: "./FooBar.ipa",
    distribute_external: true,
    skip_submission: true,
    skip_waiting_for_build_processing: false
) 
```

对于 Android 来说，这几乎是相同的过程，但是我们使用了`gradle`和`supply`，而不是`gym`和`pilot`动作。这条小巷看起来像这样:

```
platform :android do
  desc "Submit a new build to Google Play Console"

  lane :beta do
    app_identifier = "com.app.identifier"

    api_environment = "staging"
    if ENV["IS_PRODUCTION"] == "true"
      api_environment = "production"
    end
    ENV["ENVFILE"]=".env.#{api_environment}"

    puts "API_HOST: #{ENV['API_HOST']}"
    puts "IS_PRODUCTION: #{ENV['IS_PRODUCTION']}"
    puts "ENVFILE: #{ENV['ENVFILE']}"

    gradle_file = "./android/app/build.gradle"

    android_set_version_name(
      version_name: "1.0.0",
      gradle_file: gradle_file
    )

    android_set_version_code(
      gradle_file: gradle_file
    )

    gradle(
      project_dir: './android',
      task: 'assemble',
      build_type: 'release'
    )

    supply(
      json_key: 'google_play_console_key',
      track: 'beta',
      apk: './android/app/build/outputs/apk/release/app-release.apk',
      package_name: app_identifier
    )
  end
end 
```

## 关于 React 本机应用中的试运行和生产环境的最终想法

在我离开之前，我想表达一下我的想法，我认为这是关于源代码控制的最佳实践。

我建议将真实值提交给`.env`(本地环境)，但是将`.env.staging`和`.env.production`保留为虚拟/非敏感值，这些值仅在构建时在构建机器上被替换。

因此，回购协议中的三个文件如下所示:

```
// .env
IS_PRODUCTION=false
API_HOST=https://api.staging.foobar.com

// .env.staging
IS_PRODUCTION=false
API_HOST=api_host

// .env.production
IS_PRODUCTION=true
API_HOST=api_host 
```

完整的源代码可以在 [Github](https://github.com/calintamas/react-native-envs-poc) 上找到。

请随时告诉我你的想法，或者对我上面提出的解决方案有什么建议。

谢谢，