# 探索 Firebase 物料清单(BoM)

> 原文：<https://dev.to/rosariopfernandes/exploring-firebase-bill-of-materials-bom-1hij>

*葡萄牙语版[developingwith.firebaseapp.com](https://developingwith.firebaseapp.com/Exploring-Firebase-Bill-Of-Materials/)T3】*

曾几何时，在不太遥远的过去，人们可以很容易地将 Firebase SDK 添加到他们的 Android 项目中，而不用担心每个模块的不同版本。每个模块(核心、授权、firestore、存储等)都将使用相同的版本，因此我们的`build.gradle (app)`文件将如下所示:

```
 implementation 'com.google.firebase:firebase-core:12.0.0'
    implementation 'com.google.firebase:firebase-firestore:12.0.0'
    implementation 'com.google.firebase:firebase-auth:12.0.0' 
```

或者更好，我们可以在`build.gradle (project)` :
中声明 firebase 版本

```
buildscript {
    ext.firebase_version = '12.0.0'
    // ... Other configurations
} 
```

然后在`build.gradle (app)` :
中使用它

```
 implementation 'com.google.firebase:firebase-core:$firebase_version'
    implementation 'com.google.firebase:firebase-firestore:$firebase_version'
    implementation 'com.google.firebase:firebase-auth:$firebase_version' 
```

但是这个王国的幸福并没有持续多久。一段时间后，一些模块开始比其他模块更频繁地获得更新，迫使 sdk 开发人员修改他们的版本，因此每个模块都有不同的版本。

好吧，如果你希望自己生活在那个童话里，我有好消息告诉你！感谢 [Gradle 5.0](https://docs.gradle.org/current/userguide/upgrading_version_4.html#rel5.0:bom_import) 中引入的**物料清单(BoM)** 功能，现在你可以对所有 Firebase Android SDK 模块使用一个单一版本。

物料清单(BoM)允许 SDK 开发人员指定(在一个清单中)哪些模块版本已知可以相互协作，然后为该清单设置一个版本。

因此，每次 Firebase 团队发布新的清单时，您都可以确信您将使用每个模块的最新版本(在 BoM 发布时)。

你可以在 [Firebase Android 发行说明](https://firebase.google.com/support/release-notes/android)中找到最新的 BoM 版本。

* * *

# 入门

## 升级到 Gradle 5.0

由于 BoM 特性是在 gradle 5.0 中引入的，您可能想要升级您的 gradle 版本(如果您还没有升级的话)。

要升级到 gradle 5.0 并将 Firebase BoM 添加到您的 Android 项目，请遵循以下步骤:

1.  首先升级到版本 4.10，并检查是否有任何错误弃用警告。最好在升级到新版本之前修复所有错误和警告。

2.  在您的项目根文件夹中使用以下命令升级到版本 5.0:

```
./gradlew wrapper --gradle-version 5.0 
```

1.  同步项目并将依赖项添加到 gradle 文件:

```
dependencies {
    // Import the BoM
    implementation platform('com.google.firebase:firebase-bom:16.0.0')

    // Import the modules not minding their versions. Hooray!
    implementation 'com.google.firebase:firebase-core'
    implementation 'com.google.firebase:firebase-firestore'
    implementation 'com.google.firebase:firebase-auth'

    // ... Other dependencies
} 
```

再来一次项目同步，就这样了！您不再需要担心每个模块的不同版本。

## 具有 Gradle 4.6.x 或更高版本

如果您不打算升级到 Gradle 5.0，您仍然可以利用该功能，因为它从 gradle 4.6.x 开始就处于实验模式，您只需启用它:

1.  打开项目的`settings.gradle`文件并添加行`enableFeaturePreview('IMPROVED_POM_SUPPORT')`:

```
include ':app'
enableFeaturePreview('IMPROVED_POM_SUPPORT') 
```

1.  同步项目并将依赖项添加到 gradle 文件:

```
dependencies {
    // Import the BoM
    implementation 'com.google.firebase:firebase-bom:16.0.0'

    // Import the modules not minding their versions. Hooray!
    implementation 'com.google.firebase:firebase-core'
    implementation 'com.google.firebase:firebase-firestore'
    implementation 'com.google.firebase:firebase-auth'

    // ... Other dependencies
} 
```

再来一次项目同步，就这样了！

* * *

# 备注

请注意:

*   如果指定了模块版本并使用了 Firebase BoM，则指定的模块版本会覆盖 BoM 中包含的版本。

*   Firebase BoM 仍然是一个实验性的特性，这意味着可能会有一些问题。如果您想报告问题或提供反馈，请考虑在 [Firebase Android SDK 问题跟踪器](https://github.com/firebase/firebase-android-sdk/issues)中这样做。