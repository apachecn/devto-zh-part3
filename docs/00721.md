# 快速点击:在 R 中处理 Cocoa 核心数据时间戳

> 原文：<https://dev.to/hrbrmstr/quick-hit-handling-cocoa-core-data-timestamps-in-r-28g2>

这个周末，我们第一次有了一台新的乘式割草机。我们总是讨价还价，以保留卖家在我们多年来购买的任何特定房屋中使用的那一个(那对于一个“需要”乘式割草机的院子来说足够大)。

我们最终从 John Deere 那里得到了一个模型，手册(是的，我真的读过手册)提到他们有一个[割草机加应用程序](https://www.deere.com/en_US/corporate/our_company/news_and_media/press_releases/2016/residential/2016march29-mowerplus-app.page)来跟踪“东西”。鉴于缺乏内置传感器，我认为这只是一些俗气的东西，但它并不坏(他们不会做任何超级邪恶的追踪器),如果你在割草时使用它，你的移动设备可以追踪任何东西，并会通知你规定的服务事件。

在实地使用之前，我通过本地代理服务器运行了这款应用程序，它只给家里打电话检查序列号和获取天气信息，这意味着没有隐藏的 API 来获取和模仿割草数据。但是，这意味着它把所有需要的东西都存储在本地应用程序中。由于我使用的是 iOS 移动设备(你也应该使用，Android 的安全性很差)，我认为这意味着它将所有东西都存储在设备上的 SQLite 数据库中。

对即时备份的快速回顾证实了这个假设是正确的，我开始变得疯狂(正如你在前面提到的 Twitter 帖子中看到的)。

然而，它有这些克雷克雷时间戳:

```
$ ZTIMESTAMP <dbl> 581100271, 581100272, 581100270, 581100281, 581100290, 581100328, 581100308, 581… 
```

Enter fullscreen mode Exit fullscreen mode

多亏了 [@dabdine](http://twitter.com/dabdine) 的快速搜索，原来这些是[苹果可可核心数据时间戳](https://www.epochconverter.com/coredata)。它们有两种口味:

*   自 2001-01-01 00:00:00 以来的秒数
*   自^^以来的纳秒

这篇文章的全部原因(以及一些不必要的冗长)是为了让它进入互联网的长期记忆库(谷歌/互联网档案馆)，这样其他人也可以参考它。

如果你遇到了这些野兽中的一个(对于 2019 年的日期，他们可能会以 56，57，58，59 或 60 开始——列举它们以使谷歌搜索更能找到这篇文章)。2020 年的日期将以 60、61、62 或 63 开始(这对精明的搜索者来说应该足够了)。但是，如果您在使用 Excel 日期技巧、`{lubridate}`函数或`{anytime}`时运气不好，请尝试用
转换一个示例时间戳

```
as.POSIXct(sample_timestamp, origin = "2001-01-01") 
```

Enter fullscreen mode Exit fullscreen mode

看看您是否获得了一个更“期望的”/实际的值，并且知道您已经获得了一个核心数据时间戳。甚至可以贴:

```
from_coredata_ts <- function(x, tz = NULL) {
  .POSIXct(ifelse(
    test = floor(log10(x)) >= 10, # If you're still using R in 2317 then good on ya and edit this
    yes = as.POSIXct(x/10e8, origin = "2001-01-01"), # nanoseconds coredata
    no = as.POSIXct(x, origin = "2001-01-01") # seconds coredata
  ), tz = tz)
} 
```

Enter fullscreen mode Exit fullscreen mode

到您的 RStudio snippets 文件或个人的“misc”包中，以便快速将这两种核心数据时间戳转换成适当的 R `POSIXct`对象。