# 如何可靠地将文件从 android 复制到 pc

> 原文：<https://dev.to/c33s/how-to-reliable-copy-files-from-android-to-pc--3mh7>

我不敢相信我们正在把人送上月球，但是不可能把文件从安卓系统可靠地复制到个人电脑上。

mtp 协议是一个真正的 s**t，文件不在那里，我知道他们在那里。我的解决方法通常是将文件复制到外部 sd，并通过读卡器将 sd 连接到 pc。还尝试了像 SD Scanner 这样刷新文件数据库的应用(？)以便文件在 pc 上正确显示。

真正有帮助的是安装了`adb`，启用了开发者模式，并简单地使用`pull`函数来复制整个目录。

```
adb pull /storage/emulated/0 
```

链接:

*   封面图片由 pix abay[https://www . pexels . com/photo/man-person-face-portrait-34667/](https://www.pexels.com/photo/man-person-face-portrait-34667/)
*   sd 扫描仪[https://f-droid . org/en/packages/com . Gmail . jerickson 314 . SD scanner/](https://f-droid.org/en/packages/com.gmail.jerickson314.sdscanner/)