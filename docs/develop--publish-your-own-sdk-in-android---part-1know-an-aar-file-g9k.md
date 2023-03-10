# 在 Android 中开发和发布你自己的 SDK 第 1 部分(了解 AAR 文件)

> 原文：<https://dev.to/mohitrajput987/develop--publish-your-own-sdk-in-android---part-1know-an-aar-file-g9k>

“安卓是技术人员毕生梦想的那种大获成功的产品。”——丹尼尔·莱昂斯

几天前，我们发布了一个名为 [App Utility](https://github.com/mohitrajput987/android-utility) 的 Android 库，其中有各种实用程序 API，几乎用于所有的 Android 项目，如验证、共享偏好等。这个库正处于初始开发阶段，是专门为编写本教程而推出的。

除了 JAR 文件，Android 还利用了一种叫做 Android ARchive (AAR)的二进制分发格式。的。aar bundle 不过是一个 Android 库项目的二进制发行版。

AAR 类似于 JAR 文件，但是它可以包含资源和编译后的字节码。类似于 JAR 文件，AAR 文件可以包含在 Android 应用程序的构建过程中。

众所周知，在 Android Studio 中，如果您希望包含任何库，例如 SmilePass Face SDK，您可以通过在 build.gradle 文件中添加以下行来实现

```
dependencies {
implementation 'com.otb.utility:preference:0.0.1'
} 
```

Enter fullscreen mode Exit fullscreen mode

你有没有想过 gradle 是怎么把库从存储库中拉出来的？基本上，我们需要知道一个 gradle 依赖字符串由三部分组成-

```
dependencies {
implementation 'GROUP_ID:ARTIFACT_ID:VERSION'
} 
```

Enter fullscreen mode Exit fullscreen mode

上例中， *GROUP_ID* 为`com.otb.utility`， *ARTIFACT_ID* 为`preference`，*版本*为`0.0.1`。

**GROUP_ID-** 它定义了存储库组的名称。执行相同上下文作业的多个库可能属于同一个 GROUP_ID。例如，Android 支持库对于多个支持库具有相同的 GROUP_ID com.android.support。下面是例子——

```
dependencies {
    implementation 'com.android.support:design:' + supportLibVersion
    implementation 'com.android.support:appcompat-v7:' + supportLibVersion
    implementation 'com.android.support:recyclerview-v7:' + supportLibVersion
    implementation 'com.android.support:support-v4:' + supportLibVersion
    implementation 'com.android.support:exifinterface:' + supportLibVersion;
} 
```

Enter fullscreen mode Exit fullscreen mode

当您向 build.gradle 文件添加 gradle 依赖项并同步项目时，gradle 将询问 maven repository 是否存在相应的 AAR 和其他文件。例如，对于 gradle dependency com . smile pass . mobiles dk:faces dk:1 . 0 . 0，您可以从这里的[找到它的库文件。
然后 Android Studio 将这些文件下载到我们的机器上，并编译这个项目。现在您可以在您的项目中访问这个 SDK 的功能。](http://jcenter.bintray.com/com/otb/utility/preference/0.0.1)

## 知道一个 AAR 文件格式

AAR 文件是在 JAR 文件的基础上开发的。发明它是因为 JAR 文件不能包含 Android 特定的文件，如资源、清单、资产、JNI 等。一个 AAR 文件包含所有这些东西，它是一个像 JAR 一样的普通压缩文件。它包含名为 classes.jar 的 JAR 文件。AAR 文件的结构如下-

```
- /AndroidManifest.xml (mandatory)
- /classes.jar (mandatory)
- /res/ (mandatory)
- /R.txt (mandatory)
- /assets/ (optional)
- /libs/*.jar (optional)
- /jni/<abi>/*.so (optional)
- /proguard.txt (optional)
- /lint.jar (optional) 
```

Enter fullscreen mode Exit fullscreen mode

## 开发&发布自己的安卓库

当你第一次有机会开发自己的 SDK 时，很可能会想到几个问题-
构建和开发 SDK 的最佳实践是什么？
如何混淆 SDK，与正常的应用项目有何不同？
在哪里以及如何发布 SDK 来创建它的 Gradle 依赖？
如何为集成指南的开发者提供教程和样例应用？

就这些吗？这些问题足够入门了吗？
答案是——不
你可能没有意识到，但是每个问题都是其他子问题的根。当你在这些问题的海洋中潜水时，你会感觉到许多事情向你走来。为了让你的船漂得更好，我们在本教程中总结了这些问题。

要开始开发自己的 Android 库，请阅读本系列文章的第 2 部分。