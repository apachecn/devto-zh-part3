# 用 Razor 添加阅读. NET 核心博客的估计时间

> 原文：<https://dev.to/saigkill/adding-a-estimated-time-to-read-to-a-net-core-blog-with-razor-c41>

这个星期我实现了一个功能，向你展示，你需要花多少时间来阅读一篇博客文章。[![](img/a3481c97bbd076ef1c00eae2b7050a37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rUqS6Gf9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://saschamanns.de/img/markers/ASPNetCore.jpg)

第一步，我使用我的模型 BlogStory 获取内容。然后 Razor 执行以下操作:

1.  它计算单词之间的空格并加 1。所以现在我们知道这篇文章中有多少单词。
2.  最多的人一分钟能看 200 到 250。所以我们需要把统计出来的单词除以 250。然后我们知道，阅读需要多少分钟。
3.  然后我们将模和除结合起来得到秒。

```
@{
    var word_count = @Model.Body;
    var counts = word_count.Count(ch => ch == ' ') + 1;    
    var minutes = counts / 250;
    var seconds = counts % 250 / (250 / 60);
    var str_minutes = (minutes == 1) ? "Minute " : "Minutes ";
    var str_seconds = (seconds == 1) ? "Second " : "Seconds ";    
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们放置显示预计阅读时间的代码

```
<i class="fas fa-clock"></i> @minutes @str_minutes  @seconds @str_seconds 
```

Enter fullscreen mode Exit fullscreen mode

在这个地方，这个剪贴的地方，将是预计的阅读时间。

目前它被放置在客户端。计划将来将该功能转移到服务器端。

[![Creative Commons License](img/0e21f9959916f90e7c4111af92415413.png)](https://creativecommons.org/licenses/by-sa/3.0/de/deed.en)

Sascha Manns 的这个作品是在[归属-共享 3.0 德国许可证(CC BY-SA 3.0 DE)](https://creativecommons.org/licenses/by-sa/3.0/de/deed.en/) 下授权的。

根据 [saschamanns.de](https://saschamanns.de) 的一部作品改编。