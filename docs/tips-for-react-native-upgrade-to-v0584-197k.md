# react-native 升级到 v0.58.4 的提示

> 原文：<https://dev.to/sasurau4/tips-for-react-native-upgrade-to-v0584-197k>

## 什么是文章

我尝试将 react-native v0.57.7 .升级到 v0.58.4。

这篇文章是升级的传奇。

## 步骤

首先，通过 yarn 或 npm 将 react-native 升级到 v0.58.4，react 升级到 16.6.1，将 metro-react-native-babel-preset 升级到 0.51.1。

您可能会在`yarn react-native ios or android`时遇到错误。

此处为附加步骤。

### 用于 iOS

变更日志中提到的[，需要手动将`JavaScriptCore.framework`链接为 librariy。](https://github.com/react-native-community/react-native-releases/blob/master/CHANGELOG.md#058)

当我的用例。

### 用于安卓系统

为 android 升级有点复杂，遵循 [changelog](https://github.com/react-native-community/react-native-releases/blob/master/CHANGELOG.md#058)

我遇到的错误`More than one file was found with OS independent path 'lib/x86_64/libjsc.so'`与[相同本期评论](https://github.com/facebook/react-native/issues/2814#issuecomment-458866869)

所以，我通过修改 build.gradle 文件来修复错误。

文件的最终结果如下。

*   `android/build.gradle`

```
...
ext {
    ...
    buildToolsVersion = "28.0.2"
    compileSdkVersion = 28
    ...
} 
```

*   `android/app/build.gradle`

```
android {
    ...
    packagingOptions {
        pickFirst 'lib/x86_64/libjsc.so'
        pickFirst 'lib/arm64-v8a/libjsc.so'
    }
    ...
} 
```

## 结论

希望这些小技巧对你有帮助。有一个 habby 反应-本地开发！