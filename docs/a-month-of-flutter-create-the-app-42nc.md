# 动荡的一个月:创建应用程序

> 原文：<https://dev.to/abraham/a-month-of-flutter-create-the-app-42nc>

*最初发表于[bendyworks.com](https://bendyworks.com/blog/a-month-of-flutter-create-the-app)。*

任何 [Flutter](https://flutter.io) 项目的开始都包括安装 [Flutter SDK](https://flutter.io/docs/get-started/install) 和[选择编辑器](https://flutter.io/docs/get-started/editor)。我将使用 Linux 风格的 SDK 和安装了 Flutter 和 Dart 插件的 Android Studio。

按照链接说明[安装 Flutter SDK](https://flutter.io/docs/get-started/install) 并设置 Android 模拟器或 iOS 模拟器。

一旦你安装了 SDK，运行`flutter doctor`来确保一切都正确安装。如果您当前没有运行模拟器，“连接的设备”可能会失败。

```
$ flutter doctor
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel beta, v0.11.11, on Linux, locale en_US.UTF-8)
[✓] Android toolchain - develop for Android devices (Android SDK 28.0.3)
[✓] Android Studio (version 3.2)
[✓] Connected device (1 available)

• No issues found!
~~~{% endraw %}

Now create the base app itself. {% raw %}`flutter create <name>`{% endraw %} is a handy command to do this.{% raw %}

~~~bash
$ flutter create birb
Creating project birb...
  # truncated...
  birb/.metadata (created)
  birb/.gitignore (created)
  birb/README.md (created)
Running "flutter packages get" in birb...                    1.2s
Wrote 64 files.

All done!
[✓] Flutter is fully installed. (Channel beta, v0.11.11, on Linux, locale en_US.UTF-8)
[✓] Android toolchain - develop for Android devices is fully installed. (Android SDK 28.0.3)
[✓] Android Studio is fully installed. (version 3.2)
[✓] Connected device is fully installed. (1 available)

In order to run your application, type:

  $ cd birb
  $ flutter run

Your application code is in birb/lib/main.dart.
~~~{% endraw %}

One change I'll make here is renaming the {% raw %}`birb` directory to `app`. In the future I plan on having the project as a [monorepo](https://hackernoon.com/one-vs-many-why-we-moved-from-multiple-git-repos-to-a-monorepo-and-how-we-set-it-up-f4abb0cfe469) and include Firebase Functions code in a sibling directory.

Make sure that `flutter test` passes.

~~~bash
$ flutter test
00:03 +1: All tests passed!
~~~

If you haven't already, set up a virtual device to run the app in. Alternatively you could connect a real device.

![Android Studio Virtual Device Configuration](https://thepracticaldev.s3.amazonaws.com/i/sdify7hp691wcf3uz1gw.png)

[Run the generated app](https://flutter.io/docs/get-started/test-drive) in the Android Emulator (or iOS Simulator) to make sure the app works. For this series I'll be using a Pixel 2 profile with the Play Store. The running app should look something like this:

![Screenshot of sample app running in Android Emulator](https://thepracticaldev.s3.amazonaws.com/i/2vgpmckmnl0ig8vh9wbf.png)

Tomorrow I'll [work on configuring continuous integration](https://github.com/abraham/birb/issues/2) to make sure tests are always passing.

## Code changes

- [#4 Create initial app](https://github.com/abraham/birb/pull/4) 
```

Enter fullscreen mode Exit fullscreen mode