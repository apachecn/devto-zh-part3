# Android ADB 命令(第 1 部分)电池

> 原文：<https://dev.to/neokleoys2005/android-adb-commands-part-1-3219>

# ADB 命令

根据文件:

> Android Debug Bridge (adb)是一个多功能的命令行工具，可以让您与设备进行通信。adb 命令有助于各种设备操作，如安装和调试应用程序，它提供了对 Unix shell 的访问，您可以使用该 shell 在设备上运行各种命令。

更多信息的链接。

我将尝试通过一系列的帖子来浏览我最喜欢的命令。

1)设置电池电量

> adb 壳牌 dumpsys 电池组 5 级

上面的命令将手机的电池电量设置为 5%。这可以用来测试应用程序在低电量下的表现，或者你的黑暗主题是否被启用等。

使用将电池电量重置回真正的**电量**

> adb shell dumpsys 电池复位

[第二部分现已推出](https://dev.to/neokleoys2005/android-adb-commands-part-2-1gog)