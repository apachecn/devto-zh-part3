# Android ADB 命令(第 2 部分)警报

> 原文：<https://dev.to/neokleoys2005/android-adb-commands-part-2-1gog>

# ADB 命令

以前的职位:[第一部分](https://dev.to/neokleoys2005/android-adb-commands-part-1-3219)

根据文件:

> Android Debug Bridge (adb)是一个多功能的命令行工具，可以让您与设备进行通信。adb 命令有助于各种设备操作，如安装和调试应用程序，它提供了对 Unix shell 的访问，您可以使用该 shell 在设备上运行各种命令。

更多信息的链接。

2)检查是否设置了警报，以及何时触发警报

> adb shell dumpsys 警报

该命令非常冗长，我建议使用 grep 进行过滤，例如

> adb shell dumpsys 报警| grep com.package.name

**提示 1:** 如果你不打算使用 **grep** 或类似的东西，使用 **cmd+F** 或 **ctrl+F** 通过搜索包名找到你的应用。

**提示 2:** 如果你的终端的缓冲区大小不够大，并且你没有使用 grep 命令，那么最好输出到一个文件中。

即`adb shell dumpsys alarm > AlarmsOutput.txt`

输出如下所示:

```
Batch{28b15ce num=18 start=60346095 end=65172917 flgs=0x8}:

    RTC #17: Alarm{ae74957 type 1 when 1491279601728 com.package.name}

      tag=*alarm*:Alarm_Intent

      type=1 whenElapsed=+11h57m57s410ms when=2019-04-04 07:20:01

      window=+8h59m59s997ms repeatInterval=0 count=0 flags=0x0

      operation=PendingIntent{d1cec44: PendingIntentRecord{d3307ba com.package.name broadcastIntent}} 
```

正如你从上面看到的, **when=** 告诉我们什么时候被触发。

这对调试和验证 AlarmManager 的行为非常有帮助。

一个关于**如何阅读**输出的优秀答案，[来看看](https://stackoverflow.com/a/31600886/3330058)

敬请期待第三部即将上映！