# 用 ADB 录制您的 Android 屏幕视频

> 原文：<https://dev.to/walkingriver/record-video-of-your-android-screen-with-adb-4poh>

最近，我需要为一个运行在 Android 测试设备上的应用程序录制一个演示视频，这个设备是一台较旧的三星 Galaxy S4。我不知道该怎么做。Mac 版 Camtasia 支持直接从 iOS 设备上捕捉即时视频，但不支持 Android。怎么办？本文中的说明描述了一个简单的解决方案，它应该可以在 MacOS 和 Windows 上运行。

## 背景

我最近一直在使用 Ionic 框架编写移动应用程序。我的目标是能够几乎同时为 iPhone 和 Android 发布应用程序。作为向 Google Play 和 Apple App Store 提交程序的一部分，需要(或者至少强烈推荐)提交一个应用程序运行的演示视频。说起来容易做起来难。

## 搜索

经过一番搜索，我在几个不同的网站上找到了说明。他们都没有我需要的所有步骤。因此，与其试图记住我在哪里找到了所有的东西，我想我会把所有的说明放在我自己的博客上的一个地方，这样我就可以很容易地再次找到它。

## 先决条件

*   Android 调试桥(adb)或 Android SDK。如果你已经在做 Android 开发，你可能已经有了其中一个或者两个。
*   USB 转电话线(最好是设备附带的那根)。

## 记录屏幕

1.  通过 USB 将设备连接到计算机。
2.  打开您最喜欢的终端模拟器，找到您想要保存视频的文件夹。
3.  运行下面的命令:`adb shell screenrecord /mnt/sdcard/Movies/test.mp4`(把`test.mp4`改成你想要的任何东西)。您可能会看到一些警告，但不会收到任何错误。我把视频放在标准电影文件夹中，以便在设备上容易找到，以防我想在复制到我的电脑之前预览它。
4.  用手机录你想录的任何东西。您所做的一切都将保存到文件`test.mp4`中。
5.  按 Ctrl+C 停止录制。

## 将视频复制到你的电脑上

1.  在终端输入以下命令:`adb pull /mnt/sdcard/Movies/test.mp4 .`这里的路径应该与来自`screenrecord`命令的路径相匹配。确保在该命令的末尾包含尾随点。
2.  您可以随意使用该视频。

也许有更好的方法，但是我很便宜，而且这是免费的。我确实找到了其他方法，大多是付费 app。如果你知道一些更简单的东西，请在这里或在 twitter 上告诉我。我是 [@walkingriver](https://dev.to/walkingriver)