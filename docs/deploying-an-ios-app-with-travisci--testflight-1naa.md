# 使用 TravisCI & TestFlight 部署 iOS 应用程序

> 原文：<https://dev.to/geojow/deploying-an-ios-app-with-travisci--testflight-1naa>

*本文原帖[此处](https://medium.com/@geojow/deploying-an-ios-app-with-travisci-testflight-a734f31fa9d6)* 。

> Travis CI 是一个托管的持续集成和部署系统。
> 
> 持续集成(CI)是一种开发实践，它要求开发人员每天数次将代码集成到共享存储库中。然后，每个签入都由一个自动构建来验证，允许团队尽早发现问题。

用外行的话来说，我设置的这个部署过程从 git 推送开始，然后 Travis 从 GitHub 中提取我的项目，构建它，并将其部署到 TestFlight，而我只编写了一条命令！

事实是，它花了我大约 2 周的时间和 52 次 git 推送，所以这里是我在这个问题上做的笔记，所以希望它不会花你那么长时间！

# 开始前:

本指南假设您已经有一个准备发布到 TestFlight 的 iOS 项目，您已经在 iTunesConnect 中为它设置了一个应用程序，并且您已经在 GitHub 中为您的 iOS 项目的 repo 打开了 TravisCI。

# 初始设置

## 创建一个. travis.yml 文件

Travis CI 在您的 GitHub repo 中查找这个 **.travis.yml** 文件，以便它知道需要执行什么脚本。

至少，您需要为您的项目指定语言。请注意，即使您的项目完全用 Swift 编写，您也需要将语言指定为 Objective-C。

在项目的根目录下创建一个 **.travis.yml** 文件，并添加以下内容:

```
language: objective-c 
```

以上应该足以构建您的项目，但是，如果您正在构建目标并将其推送到不同的环境(例如，开发)，您将需要指定要推送到 TestFlight 的目标，我们将使用 build.sh 脚本进一步做这件事。

# App 签约

由于我们希望将我们的应用程序放在 TestFlight 上，并最终放在 AppStore 上，我们需要在 Travis 上签署我们的应用程序，为了做到这一点，我们需要创建所有必要的证书和配置文件。稍后，我们将编写一些脚本来完成这个签名。

# 证书&简介

## 苹果全球开发者关系认证机构

如果您的钥匙串中已经有它，您可以导出它并将其存储在项目中的 **scripts/certs/apple.cer** 下。或者，你可以从苹果页面下载，点击这个[链接](http://developer.apple.com/certificationauthority/AppleWWDRCA.cer)。

## iPhone 分发证书+私钥

从“钥匙串访问”中导出此证书，并将证书存储在项目中的 **scripts/certs/dist.cer** 下。然后导出私钥并保存到 **scripts/certs/dist.p12** ，输入您选择的密码。

Travis 需要知道这个密码来访问这个概要文件，所以在终端中运行下面的命令来安装 Travis gem 之后:

```
gem install travis 
```

您可以通过在项目根目录下运行以下命令(同样在终端中)将密码添加到 Travis 文件中。

```
travis encrypt "KEY_PASSWORD={password}" -add 
```

这将向您的 **.travis.yml** 添加一个名为 KEY_PASSWORD 的加密环境变量。然后可以在 Travis CI 执行的任何脚本中使用它。

您的 **.travis.yml** 文件现在应该看起来像这样:

```
language: objective-c
osx_image: xcode9.3
env:
  global:
  - secure: {VERY LONG STRING} 
```

请注意，我还指定了使用哪个版本的 Xcode，并且请注意您的 **.travis.yml** 文件中元素的顺序并不重要。

# iOS 预置描述文件(分发)

如果尚未创建，请在[developer.apple.com](//developer.apple.com)上创建一个新的分发预配描述文件，如下所示:

请确保您在第一页选择了 App Store，否则，当您稍后尝试使用预置描述文件时，它将不起作用。

下载概要文件，并将其保存在**脚本/概要文件**下，例如**脚本/概要文件/分发.移动供应**。

因为我们需要在我们的一个脚本中访问这个概要文件，所以我们需要在 **.travis.yml** 中存储这个名称作为一个全局环境变量。例如，如果文件是**distribution . mobile provision**，我们的 **.travis.yml** 文件现在应该看起来如下:

```
language: objective-c
osx_image: xcode9.3
env:
  global:
  - |
      - APP_NAME="TestApp"
      - 'DEVELOPER_NAME="iPhone Distribution: Geojow Ltd (12345678)"'
      - PROFILE_NAME="Distribution"
  - secure: {VERY LONG STRING} 
```

我们还添加了另外两个环境变量， **APP_NAME** ，它通常与您的目标同名。和 **DEVELOPER_NAME** ，这是你在 Xcode 上在**代码签名身份>发布**下检查你的构建设置时看到的。

**注意，如果您的 GitHub repo 不是私有的，您可能需要加密您的证书和配置文件。有关如何操作的信息，请参见以下链接:**

[https://www.objc.io/issues/6-build-tools/travis-ci/](https://www.objc.io/issues/6-build-tools/travis-ci/)

# 添加脚本

完成本部分后，我们将有 3 个脚本:

```
1\. add-key.sh
2\. build.sh
3\. remove-key.sh 
```

这些脚本的名称是不言自明的，但是我们将在后面解释发生了什么。

在**脚本/** 目录中创建所有这些脚本。

## add-key.sh

```
#!/bin/sh
# Create a custom keychain
security create-keychain -p travis ios-build.keychain
# Make the custom keychain default, so xcodebuild will use it for signing
security default-keychain -s ios-build.keychain
# Unlock the keychain
security unlock-keychain -p travis ios-build.keychain
# Set keychain timeout to 1 hour for long builds
security set-keychain-settings -t 3600 -l ~/Library/Keychains/ios-build.keychain
# Add certificates to keychain and allow codesign to access them
security import ./scripts/certs/apple.cer -k ~/Library/Keychains/ios-build.keychain -T /usr/bin/codesign
security import ./scripts/certs/dist.cer -k ~/Library/Keychains/ios-build.keychain -T /usr/bin/codesign 
security import ./scripts/certs/dist.p12 -k ~/Library/Keychains/ios-build.keychain -P $KEY_PASSWORD -T /usr/bin/codesign
# Set Key partition list
security set-key-partition-list -S apple-tool:,apple: -s -k travis ios-build.keychain
# Put the provisioning profile in place 
mkdir -p ~/Library/MobileDevice/Provisioning\ Profiles
echo "My profile name is $PROFILE_NAME"
cp "./scripts/profile/$PROFILE_NAME.mobileprovision" ~/Library/MobileDevice/Provisioning\ Profiles/ 
```

在这里，我们正在创建一个名为 **ios-build** 的临时钥匙链，它将包含我们对应用程序进行代码签名所需的所有证书。注意，在第 18 行，我们使用了我们在 **.travis.yml** 中定义的加密环境变量 **KEY_PASSWORD** 。

在最后一行，我们将移动配置文件复制到**库**文件夹中。

一旦我们创建了这个脚本，我们需要确保它有可执行的权限，所以在终端 cd 到项目根目录并运行下面的命令。

```
chmod a+x scripts/add-key.sh 
```

既然已经导入了证书和配置文件，我们就可以签署我们的应用程序了。

## build.sh

```
#!/bin/sh
if [[ "$TRAVIS_PULL_REQUEST" != "false" ]]; then
  echo "This is a pull request. No deployment will be done."
  exit 0
fi
security list-keychains -s ios-build.keychain
rm ~/Library/MobileDevice/Provisioning\ Profiles/$PROFILE_NAME.mobileprovision 
mkdir -p ~/Library/MobileDevice/Provisioning\ Profiles/
cp ./scripts/profile/$PROFILE_NAME.mobileprovision ~/Library/MobileDevice/Provisioning\ Profiles/
echo "*********************"
echo "*     Archiving     *"
echo "*********************"
xcrun xcodebuild -workspace TestApp.xcworkspace -scheme TestApp\ Stg -archivePath $ARCHIVE_NAME.xcarchive archive
echo "**********************"
echo "*     Exporting      *"
echo "**********************"
xcrun xcodebuild -exportArchive -archivePath $ARCHIVE_NAME.xcarchive -exportPath . -exportOptionsPlist ExportOptions.plist 
```

在这里，我们首先检查这是否是一个拉请求，如果是，我们要停止部署！

接下来，我们需要将正确的配置文件放在第 10–12 行可以找到的位置。

然后，我们将归档构建。请注意，在这个文件中，我使用了 **$PROFILE_NAME** 和 **$ARCHIVE_NAME** ，这些是全局环境变量，我已经将它们添加到我的. travis.yml 文件中，以便更容易地管理不同环境的构建。这超出了本操作指南的范围。

这是在第 18 行完成的。我们将归档文件输出到当前的回购协议中，这样我们就可以在下一步中轻松地将其导出。

在我们导出归档文件并生成**之前。ipa** 文件将被推送到 iTunesConnect，我们需要用 **ExportOptions** 定义一个 **plist** 文件。

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>method</key>
        <string>app-store</string>
    <key>teamID</key>
        <string>123456789</string>
    <key>uploadBitcode</key>
    <false/>
    <key>compileBitcode</key>
        <false/>
    <key>uploadSymbols</key>
        <true/>
    <key>signingStyle</key>
        <string>manual</string>
    <key>signingCertificate</key>
        <string>iPhone Distribution: Geojow Ltd</string>
    <key>provisioningProfiles</key>
        <dict>
            <key>co.geojow.TestApp</key>
            <string>Distribution</string>
        </dict>

</dict>
</plist> 
```

这是相当标准的，我们只需要确保方法是“app-store ”,并且我们在这里有所有相关的配置文件。

然后，脚本将导出带有上述选项的应用程序，我们准备将它推送到 iTunes Connect！

然而，在我们这样做之前，我们应该用下面的脚本删除临时的 keychain。

## [T1】remove-key . sh](#removekeysh)

```
#!/bin/sh
security delete-keychain ios-build.keychain
rm -f "~/Library/MobileDevice/Provisioning Profiles/$PROFILE_NAME.mobileprovision" 
```

此脚本只是移除临时钥匙串并删除移动预置描述文件。这不是绝对必要的，但在本地测试时会有所帮助。

现在我们可以进行试飞了！

# 发布到 iTunesConnect (TestFlight)

现在我们将下面的内容添加到 **build.sh** 的底部。这将导航到 altool 所在的文件夹并执行命令。

请注意，它需要用户名和密码，但是它们需要加密，要将它们添加到 Travis 文件中，请在项目根目录中执行以下命令。

```
travis encrypt "USERNAME={your-apple-account-email}" -add
travis encrypt "PASSWORD={a-app-specific-password}" --add 
```

请注意，您可以在[appleid.apple.com](//appleid.apple.com)生成特定于应用程序的密码。

```
#!/bin/sh
if [[ "$TRAVIS_PULL_REQUEST" != "false" ]]; then
  echo "This is a pull request. No deployment will be done."
  exit 0
fi
security list-keychains -s ios-build.keychain
rm ~/Library/MobileDevice/Provisioning\ Profiles/$PROFILE_NAME.mobileprovision 
mkdir -p ~/Library/MobileDevice/Provisioning\ Profiles/
cp ./scripts/profile/$PROFILE_NAME.mobileprovision ~/Library/MobileDevice/Provisioning\ Profiles/
echo "*********************"
echo "*     Archiving     *"
echo "*********************"
xcrun xcodebuild -workspace TestApp.xcworkspace -scheme TestApp\ Stg -archivePath $ARCHIVE_NAME.xcarchive archive
echo "**********************"
echo "*     Exporting      *"
echo "**********************"
xcrun xcodebuild -exportArchive -archivePath $ARCHIVE_NAME.xcarchive -exportPath . -exportOptionsPlist ExportOptions.plist
echo "************************************"
echo "*     Upload to iTunesConnect      *"
echo "************************************"
ln -s "/Applications/Xcode.app/Contents/Applications/Application Loader.app/Contents/Frameworks/ITunesSoftwareService.framework/Support/altool" /usr/local/bin/altool
ln -s "/Applications/Xcode.app/Contents/Applications/Application Loader.app/Contents/itms" /usr/local/bin/itms #itms is needed, otherwise altool will not work correctly
altool --upload-app -f "$IPA_NAME.ipa" -u $USERNAME -p $PASSWORD 
```

现在您需要更新您的 Travis 文件，以便在正确的时间运行正确的脚本！

# 更新. travis.yml 文件

将以下内容添加到您的 **.travis.yml** 文件中。请注意，它看起来不会完全像这样，但这是一个开始！

```
after_script:
- "./scripts/remove-key.sh"
before_script:
- "./scripts/add-key.sh"
branches:
  only:
  - master
  - development
env:
  global:
  - |
      - APP_NAME="TestApp"
      - 'DEVELOPER_NAME="iPhone Distribution: Geojow Ltd (12345678)"'
      - PROFILE_NAME="Dev_Distribution"
      - IPA_NAME="TestApp"
      - ARCHIVE_NAME="TestApp_Dev"
  - secure: {VERY LONG STRING}
  - secure: {VERY LONG STRING}
  - secure: {VERY LONG STRING}
language: objective-c
osx_image: xcode9.3
script:
- "./scripts/build.sh" 
```

现在，当你推送 GitHub 时，Travis 应该会将你的应用程序存档、导出并推送至 iTunesConnect，然后它就可以传递给 TestFlight 上的测试用户了。

# 相关文章

更多信息请见[https://www.objc.io/issues/6-build-tools/travis-ci/](https://www.objc.io/issues/6-build-tools/travis-ci/)。