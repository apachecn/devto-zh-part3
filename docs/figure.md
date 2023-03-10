# android

> 原文:# t0]https://dev . to/book bee/andidx-3 id7

众所周知，Android Support Library 在 SDK 版本的基础上会有一个不断更新的版本，它是为了解决各种巴克，就像我们的应用程序需要的那样。

```
The stable release of 28.0.0 will be the final feature release packaged
as android.support. All subsequent feature releases will only be made
available as androidx-packaged artifacts. 
```

Enter fullscreen mode Exit fullscreen mode

Android 支持库只提供版本 28。0.0 是最后的版本，并在此之后更新它将进入所有 AndroidX 包，因此我们将来了解 AndroidX，以帮助您决定它是什么。

AndroidX 是 Google 的[【Android jetpack】](https://developer.android.com/jetpack/)的一部分，它可以让我们更好地开发 Android 应用程序，这是 quent。

[Android Jetpack](https://developer.android.com/jetpack/)

是由 androidX 自己开发的，因为开发人员希望将包装命名为以 Android 开头的。Android OS 本身就有一个元件，而开发人员提供的 AndroidX 支援图书馆正是用来辨识 androidx 的。..更长的时间，烦恼，头痛到另一个。

对于读者来说，您希望迁移到 AndroidX 中的哪一个？

重要的是

1.  Android Studio 版本 3 .2 或以上
2.  compileSdkVersion 28 (android 9 .)。0)或以上
3.  Gradle Plugin 版本 3。2.0 (com)。android .. .工具构建:gradle:3 .2.0)或以上

该方法简单、简单、简单，只需读者在 Android Studio 中打开一个想移动的项目，然后在工具栏上选择
230。

```
Refactor -> Migrate to AndroidX... 
```

Enter fullscreen mode Exit fullscreen mode

<figure>t1⾹T2㎡T3ӁT4㎡向穿心莲的方法 t5¨T6㎡</figure>

就这样，我们的 Android Studio 将会把我们的 Migrate 代码变成 AndroidX

对于那些害怕我们已经输入的各种库的读者来说，那些代码还不是 AndroidX，那么我们的项目不会崩溃，也不会被告知。

```
gradle.properties 
```

Enter fullscreen mode Exit fullscreen mode

会发现两个新的属性一起增加: