# 最近项目中的编程位

> 原文：<https://dev.to/anonjr/programming-bits-from-recent-projects-40db>

我已经得到了咖啡工艺的第一个现场直播，将于本周一美国东部时间下午 7 点开始。我想？我讨厌夏令时)。前阵子我做了一个[测试流](https://www.coffeecraft.us/2019/03/Test-Stream.html)，[下载了一些音乐](https://www.coffeecraft.us/2019/03/AnonJr-Live-Playlist-v1.html)在后台运行。我有一个计划，现在我需要消耗掉一些紧张的能量。可能还有另外一个帖子，但是我今天花了太多时间找事情做，而不是写我想写的东西。再多做一点工作，我会让紧张懈怠与实际写作的比例向另一个方向倾斜。😃

> 旁注:在我离开即将到来的直播太远之前，因为这是频道的开始，我真的不指望有多少人出席——所以，如果你想聊天，问一些编程或职业问题，我很乐意回答。我不是像 Scott Hanselman 或 Troy Hunt 那样的技术大师，但我有 14 年的实际工作经验，为一个中等规模的医疗系统管理系统。我可能有一两个值得分享的金块。

那么，如果这不是这篇文章，那是什么呢？这篇文章是我的[项目更新](///2019/03/Projects-Projects-Projects-Updates.html)文章的后续。还有一个燃烧掉一些紧张能量的机会…开始做一些事情，嗯，是一些事情。

## an njr . com

GitHub repo 有它所有的美丽和恐怖。就像我和其他所有人参与的其他项目一样，现在的我对过去的我认为非常聪明的一些事情感到尴尬。没关系。我相信未来的我也会对一些我现在认为很棒的东西摇头。

事情稳定下来后的第一个提交是添加 [Dev.to](https://dev.to/) 链接，作为侧边栏一般更新的一部分。有趣的是，在提交后不久 [@benhalpern](https://twitter.com/bendhalpern) 发布了这条提醒:

> ![Ben Halpern 🤗 profile image](img/3814cc9cbc453345725bf2b5e578e8a0.png)本·哈尔彭🤗@ bendhalpen![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)提醒将 DEV 徽章放在你的个人网站上，与 Twitter 和你的其他社交链接并排。你可以使用 SVG 或者 Awesome 字体。
> 
> [dev.to/p/badges](https://t.co/itCHG96NX8)
> 
> 快乐编码❤️2019 年 2 月 23 日 20 点 49 分[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1099410983695478784)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1099410983695478784)43[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1099410983695478784)261

做这样的项目很有趣——你会发现还有哪些库是过时的。😕

在问了一会儿“我做得对吗？”再次检查源代码、输出、引用等等，结果是我已经有一段时间没有更新[字体了。也证明了他们有一个更简单的方法来使用这个库，但是我接下来会讲到这个。对于 AnonJr.com，我继续使用](https://fontawesome.com/)[自托管方法](https://fontawesome.com/how-to-use/on-the-web/setup/hosting-font-awesome-yourself)。对于新项目，我不建议这样做，除非你有一个清晰的理由说明为什么你*需要*自己托管那些文件——例如，“项目在公司局域网上内部运行，员工无法访问外部资源”。这就是我在恐怖角谷工作时不得不做的事情，因为一些员工无法访问公司网络外部的资源。至于这里，我不准备撕掉一切，重新为两个图标设计样式。我会把它留到从椒盐卷饼变成饭桶的时候。

几天后，我开始更新侧边栏上的项目链接，并意识到我真的需要更新页面并添加一些其他内容。将所有的链接升级到 HTTPS，并检查一个你会在之前的几篇文章中看到提到的目标，这很不错。添加一些标志和加强文案也很好，反思一些被忽视的项目有点令人难过。如果这篇笔记有一个关键的收获，那就是花些时间回顾你过去的项目和工作，并进行反思。漫威为你所取得的进步而惊叹，为你发现自己仍需努力的地方而叹息。如果你没有看到增长，或者认为不需要增长，那么你可能需要反思一下。

还有几个其他的提交，但是唯一值得一提的是添加一个合适的 Favicon，并为 Twitter/脸书/etc 设置图片和元信息。出示一张合适的卡片。由于设备种类繁多，我们不得不添加如此多的图标链接，这让我感到惊讶和沮丧。我可能会看到一个“大”和一个“低带宽”的版本，但因为 iPhone 需要一个尺寸，而 iPad 需要另一个尺寸，Windows 将固定一个网站，但只有第三个尺寸，等等——我现在有大约 8 个不同尺寸的图标浮动。更不用说 Twitter 宁愿为一张卡片使用一套元标签，但脸书使用另一套…我会推荐一个通用标准，但我们可能会看到常见的 XKCD 漫画。

[![XKCD Standards](img/5f39a21c21785cf3c5daf89a88b1d761.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RJjtUme5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgs.xkcd.com/comics/standards.png)

## CoffeeCraft.us

这是[另一个回购](https://github.com/AnonJr/CoffeeCraft.us/),在过去的几个月里占据了我大部分的承诺。我想提出一些有趣的技术挑战，但主要是在内容、布局和设计方面。我从[开始尝试“如此简单”的主题](https://github.com/mmistakes/so-simple-theme)，并从那里开始建设。

在这个项目上，我不会为任何人说话——对我来说，这个项目(以及我正在做的其他项目)的一个难题是我既是设计师又是客户。出于某种原因，尽管我有能力做出令人惊讶的事情，但我似乎不能为自己做出令人惊讶的事情。看看历史。看看我瞎扯的那些零件。我甚至有“**添加搜索，更新文字...和感觉丢失**”作为提交消息。

## 包装完毕

好吧，这并不像我第一次坐下来写的时候看起来那么技术性……尽管我很喜欢使用[ghost writer](https://github.com/wereturtle/ghostwriter)来撰写帖子，但我发现即使在程序没有焦点时光标也会闪烁，这让我非常沮丧——所以我开始在地址栏中键入下一句话，并做了各种各样有趣的其他事情，因为实际上有焦点的东西突然开始得到随机的字母和返回。在我太沮丧之前，我会看看我是否错过了更新。

我熬夜到很晚，所以我就长话短说了。我必须在凌晨 4 点起床工作…还有美国东部时间晚上 7 点即将到来的直播。最后，我想提醒一下，因为这是这个频道的开始，所以我对出席人数并不抱太大期望——所以，如果你想加入聊天，问一些编程或职业问题，我会非常乐意回答。我在 [Twitch](https://www.twitch.tv/anonjr_live) 、 [Mixer](https://mixer.com/AnonJr_Live?vod=91997262) 和 [YouTube](https://www.youtube.com/channel/UCXafqhKHbkSUIrq0LAuu0tw) 上——用你觉得最舒服的那个。Restream.io 应该会同步所有频道之间的聊天，我会让他们的聊天应用程序启动并运行(它应该会显示所有聊天记录)。

希望在那里见到你。