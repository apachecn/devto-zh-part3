# 为 Android 创建 Ionic 4 发布版本

> 原文：<https://dev.to/niranjankala/create-ionic-4-release-build-for-android-4700>

# 简介

在本文中，您将学习如何为 Android 平台创建 ionic 4 发布版本。

## 什么是离子型？

Ionic framework 是一个免费的开源工具包，用于为原生 iOS、Android 和 web 开发高质量的跨平台应用程序——所有这些都来自一个代码库。它允许我们使用著名的框架来开发 UI，例如 Angular，Vue.js，React 等等。

# 为 Android 平台设置发布版本

**步骤 1 -**
运行根目录下的“npm 安装”

**步骤 2 -**
用 CLI 添加 android 平台:

```
ionic platform add android 
```

**步骤 3 -**
如果你在一个分布式开发环境中工作，那么确保安装所需的文件。运行[离子科尔多瓦准备](https://ionicframework.com/docs/cli/commands/cordova-prepare)命令。它将安装“config.xml”中列出的平台和插件。

```
ionic cordova prepare android 
```

**步骤 4 -**
现在使用 CLI 导航至平台/android:

```
cd platforms/android 
```

在 platforms/android 文件夹下创建/复制发布签名密钥存储文件
如果您已经使用 CLI 生成了密钥存储文件，请复制为 android 应用程序创建的密钥存储文件，并回答所有问题:

```
keytool -genkey -v -keystore YourApp.keystore -alias YourApp -keyalg RSA -keysize 2048 -validity 10000 
```

按照文档中建议的步骤将[部署到设备](https://ionicframework.com/docs/v3/intro/deploying/)。

### 创建/指定发布签约信息

**步骤 1 -**
在“platforms\android”文件夹下创建一个名为“release-signing.properties”的文件。

**步骤 2 -**
将以下信息添加到该文件中:

```
key.store=YourApp.keystore
key.store.password=<YourApp keystore password>
key.alias=YourApp
key.alias.password=<YourApp alias password> 
```

# 为应用程序创建构建

现在使用 CLI 返回到 Ionic 项目的根目录，并构建一个发布版本:

```
ionic cordova build android --prod --release 
```

如果命令运行成功，那么您将在-APP _ Root _ Folder \ platforms \ Android \ APP \ build \ outputs \ apk \ release 下找到 APK 版本

### 重置插件和平台

要使用 Ionic 安装或重新安装 Package.json 中的所有 cordova 插件，请使用管理员权限在 windows 命令提示符下运行此命令

```
rd  plugins /d/s && rd platforms /d/s  && ionic cordova prepare 
```

# 结论

创建 ionic 4 应用程序的构建步骤如下，然后你可以在 Android store 上托管你的应用程序。