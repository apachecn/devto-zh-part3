# XSS 桑卡库综合体

> 原文：<https://dev.to/therealdarkmage/sankaku-complex-xss-40jk>

### 首写于 2019 年 1 月 19 日

这是我发现的第一个公认的 XSS 病毒。我非常兴奋，因为这基本上证实了我总体上在做正确的事情，只是需要更多的耐心。就像他们说的，“一切都是时间”:)

所以我在这里，在早晨感到寒冷，检查网站，我决定成为一个德根，访问我最喜欢的动漫/芒果/非常网站， [Sankaku Complex](https://www.sankakucomplex.com) 。

Sankaku 在他们的网站上发布成人内容已经有很长时间了，并且在某个时候实现了一个简单的免责声明弹出窗口。我碰巧注意到，当你滚动链接时，显示在底部栏中的 URL 是不同的，它指示了一个带有重定向的中间人 URL。

右键单击，复制链接位置。粘贴到新标签页。这样的黑客，哇。

所以，有中间人！

```
https://www.sankakucomplex.com/mature-content-disclaimer/?then= 
```

Enter fullscreen mode Exit fullscreen mode

基本上,“then”参数用于在该页面上生成是/否链接，这很疯狂，因为它没有得到适当的清理。

我玩了几分钟，直到我打破了系统，可以观察到一些行为。令人惊讶的是，如果你只是尝试输入一个双引号和一个大于号`">`，会有多少东西被破坏。

原来在那个字段上，你可以注入任意的 HTML，包括`<script>`标签！

字符串`">`是所有需要打破网页。

然而，完整的有效载荷可以短到:

```
"><script>alert()</script>

https://www.sankakucomplex.com/mature-content-disclaimer/?then="><script>alert()</script> 
```

Enter fullscreen mode Exit fullscreen mode

公开披露日期为 2019 年 2 月 17 日，报告位于:[https://www.openbugbounty.org/reports/726249/](https://www.openbugbounty.org/reports/726249/)

* * *

# [如果你需要一名计算机科学导师、代码审核员，或者仅仅是一个可以和程序配对的人，请联系我](https://codementor.io/mikebell66)