# 使用 ADB 测试深层链接 URL

> 原文：<https://dev.to/varunbarad/testing-deep-link-urls-using-adb-151e>

亚行是一个宝库，我经常从中发现一些宝石。最近，我正在为我工作的公司创建一些新的深度链接集成。下面的命令触发一个事件，类似于我们单击设备本身的链接时触发的事件。

触发深层链接的命令模式如下:

```
adb shell am start -a android.intent.action.VIEW -d "your-link-goes-inside-these-quotes" 
```

示例

```
adb shell am start -a android.intent.action.VIEW -d "https://varunbarad.com/blog" 
```

这只是我学到的亚行的一个小技巧，希望与你分享。如果你有更多这样的技巧或诀窍，我很乐意在这篇文章的 twitter 帖子中听到。如果你有任何想谈论的事情，请随时联系我 [@varun_barad](https://twitter.com/varun_barad) 。