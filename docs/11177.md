# 如何用 GitLab 设置 AppCenter 而不推送机密文件

> 原文：<https://dev.to/adamgold/how-to-setup-appcenter-with-gitlab-without-pushing-secret-files-4mk8>

我们都同意，拥有一个自动化应用构建、分发甚至分析的工具是非常好的。嗯，这正是[应用中心](https://appcenter.ms)所做的。还有更多。

[![I want that](img/06834d2c8e56b872c05d01acb96f2f12.png)](https://i.giphy.com/media/621y3s994ksbnxZZip/giphy.gif)

#### 然而...

对于我们这些使用 GitLab 作为主要 Git 服务器的人来说，设置 GitLab 与 AppCenter 一起工作可能会非常令人沮丧。我们也不想将我们的应用程序秘密推送到我们的 Git 服务器，无论是我们的 Firebase JSON 还是 AppCenter 秘密。

### 位桶镜像

[![Screen Shot 2019-03-07 at 18.04.30](img/d9de2e5e36203ceafc253932ba7eb019.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZfByUtzU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/OdXekub.png)

为了让 AppCenter 与 GitLab“一起”工作，我们首先必须将我们的存储库镜像到 BitBucket。因此，继续在 BitBucket 中创建一个存储库，然后转到您的用户的`Settings` ( **不是回购设置**，我们仍然在 BitBucket 中)，在`App passwords`下创建一个新的应用程序密码，具有所有可用的权限，并复制您稍后获得的密码。

[![Screen Shot 2019-03-07 at 18.13.21](img/e898c691a8f69e0ac9d7b2a0e968d94f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lL13XTE5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/CGjWUMu.png)

然后进入 GitLab，`Settings`->-`Repository`，在`Mirroring Repositories`下，输入你的 BitBucket 克隆 URL，选择`Mirror Direction`下的`Push`，`Password`为 auth 方式，粘贴你在 BitBucket 中得到的密码。点击`Mirror Repository`，您应该会在下表中看到新的一行。点按右侧的“刷新”按钮开始同步。

### 设置应用中心

那是容易的部分。我们仍然需要配置我们的构建并隐藏一些秘密😎

转到[应用中心](https://appcenter.ms/)，如果还没有用户，创建一个用户。创建您的应用程序(对我来说，它是一个 Android 和一个 iOS)，并完成添加应用程序中心的 SDK 的步骤。现在转到`Build`，选择`Select a Service`下的`BitBucket`。选择您新创建的 repo，然后您应该会看到您所有的分支。

### 构建脚本

[![Screen Shot 2019-03-07 at 18.04.00](img/d2d30f958bf1dd2ff31b81994b3a114d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T0zoy5X6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/S5Jv17A.png) 
现在为了让 AppCenter 构建你的应用，它需要应用的 AppCenter secret(在设置中给你，如上所示)，如果你像我一样使用 Firebase，它还需要`google-services.json`文件(对于 Android)和`GoogleService-Info.plist`对于 iOS。所以我们需要做的是在构建之前注射它们…我们写点代码吧！

我们的文件将被命名为`appcenter-pre-build.sh`(这样 AppCenter 可以在构建之前识别并运行它):

```
echo "Injecting secrets..."
echo "Updating Google JSON"
echo $GOOGLE_SERVICES_JSON | base64 --decode > "$APPCENTER_SOURCE_DIRECTORY/android/app/google-services.json"
echo "Updating Google plist"
echo $GOOGLE_SERVICES_PLIST | base64 --decode > "$APPCENTER_SOURCE_DIRECTORY/ios/GoogleService-Info.plist"
echo "Updating android secret"
echo $ANDROID_SECRET > "$APPCENTER_SOURCE_DIRECTORY/android/app/src/main/assets/appcenter-config.json"
echo "Updating iOS secret"
echo $IOS_SECRET > "$APPCENTER_SOURCE_DIRECTORY/ios/Glitz/AppCenter-Config.plist"
echo "Finished injecting secrets." 
```

这段代码适用于两个平台，你可以随意编辑。

现在要做的最后一件事是插入环境变量！

### 环境变量

如我们的代码所示，我们有 4 个环境变量。让我们再次进入构建配置，展开`Environment Variables`，并添加我们的 4 个变量:

`GOOGLE_SERVICES_JSON`、`GOOGLE_SERVICES_PLIST`、`ANDROID_SECRET`、`IOS_SECRET`。

#### 确保在输入 JSON 值之前对其进行 base64 编码！

(你可以使用[这个站点](https://www.base64encode.org/)

现在你的构建应该运行了，它应该在中间的某个地方显示`Injecting secrets…`:
[![Easy](img/9b5fc284cd96a31650f56df08a7168b3.png)](https://i.giphy.com/media/zcCGBRQshGdt6/giphy.gif)