# 新徽章即将推出:连续 4 周

> 原文：<https://dev.to/devteam/new-badge-coming-soon-4-week-streak-26db>

我们将推出一个新的个人资料徽章，如果人们连续四周每周在 [dev.to](https://dev.to/) 上发布一次，就可以获得这个徽章。由于这是 2019 年的第一周，我们想制作一个新的徽章，奖励人们在新的一年里写下目标。

我们尽量不要让平台游戏化，但我们认为这是对那些努力坐下来写作的人的一种有趣的奖励。

* * *

我们还没有编写功能，但我保证我们会在 1 月 28 日完成，届时将颁发第一批徽章。

如果你想贡献自己的功能，请随意制作一个 PR。

以下是该功能所在的文件:

### [app/labor/badge _ rewarder . Rb](https://github.com/thepracticaldev/dev.to/blob/master/app/labor/badge_rewarder.rb)

测试应该放在这里:

### [spec/labor/badge _ reward _ spec . Rb](https://github.com/thepracticaldev/dev.to/blob/master/spec/labor/badge_rewarder_spec.rb)

该功能应设计为每天运行一次，并授予任何在过去四周内每周与`published: true`有一次`article`的人徽章。没有徽章可以通过验证被授予一次以上，因此没有必要在代码中对此加以防范。

快乐编码