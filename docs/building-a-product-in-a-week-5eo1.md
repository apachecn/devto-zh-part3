# 在一周内构建一个产品

> 原文：<https://dev.to/m1guelpf/building-a-product-in-a-week-5eo1>

[https://app.blogcast.host/embed/26](https://app.blogcast.host/embed/26)

周一醒来，我有了一个奇怪的想法。在开发了微型产品(我在不到一个小时的时间里开发了两个 chrome 扩展)和小型产品(开发时间不到一天的产品)之后，我想开发并发布一些新的东西。但我不想像上次一样花一年时间。就在那时，我决定要在一周内建造一些东西。我唯一需要的是一个想法。

## 第一天——读还是听？

我开始思考创客社区，以及人们如何说创客市场(创客为其他创客建造)是拥挤的。我正在浏览 Makers Kitchen 社区，这时有人发布了一篇他们刚刚写的文章。

我花了五分钟读它。这是一篇好文章。但是后来，有人又发了一个。我确信这也是一篇好文章，但是我没有时间去读它。

我意识到了一些事情。每天都有新的精彩内容出现，但是我们不能一心多用阅读某些东西。但是还有另一种内容消费方式:音频。我们可以边听音频边做其他事情。

这是导致播客越来越受欢迎的因素之一。你不需要盯着屏幕看内容。相反，你可以遛狗或者洗碗，或者做任何其他不需要听东西的活动。

我问自己，为外面的每篇文章都提供一个播客是不是很棒？或者更好一点，**如果有一个工具可以自动生成文章的音频版本岂不是很棒？**那里也是[博客](https://blogcast.host)诞生的地方。买了域名 [blogcast.host](https://blogcast.host) 就睡觉了。

# 第 2 天-验证

我从手动抄写文章开始，想弄清楚我的产品消除了多少障碍。我不得不首先从文章中获取文本，然后调用文本到语音的 API(按字符计费)，重试几次以检查进度，然后下载音频并上传到公共服务器。显然还有改进的余地。

我还查看了其他人是否已经解决了这个问题。已经有一些应用程序提供相同的功能，但它们都专注于读者，所以我决定采取不同的立场:**我将专注于作者和出版商**，通过**允许他们在文字旁边提供文章的音频版本**，帮助他们增加读者人数(或听众人数)。

终于到了编码的时候了。我创建了一个新的 Laravel 应用程序，并开始在我的编辑器中键入内容。几个小时后，我设法制作了一个 artisan 命令，生成音频版本或文章。

我用我的命令转录了一些朋友的文章，并向他们征求反馈，他们很喜欢。

但是为了让其他人使用它，我需要的不仅仅是一个命令。我需要一个平台。

# 第三天——博客所有的事情！

这可能是我最有成效的一天，它完全专注于编码。我首先将命令功能抽象为一个类，我可以通过应用程序使用它。然后，我做了一些数据库工作，将转录的文章保存到数据库中，并使该过程异步(在 PHP 世界中，这可以通过使用队列工作器在请求-响应生命周期之外执行操作来实现)。

是时候关注应用程序面向用户的部分了。我使用 Tailwind CSS 设计了认证视图和一个非常简单的仪表板。我还为着陆定制了一个模板(我通常会从头开始设计着陆，但这次我的时间限制不允许)，并为它写了一些副本。

最后，我做了账单。我想有两个计划:定期订阅和现收现付订阅，但希望用户用信用卡注册。这比预期的要难一点，但是经过一个小时的努力，我终于成功了。

# 第 4 天——勉强后端

除了为仪表板设置一些路线，我想我没有接触后端。这一天专门致力于使仪表板工作，并在所有屏幕上看起来很好。我设计了一个漂亮的桌子，上面有下载或嵌入音频的选项，还添加了将音频赠送给另一个人的选项(稍后我会详细介绍这个功能)。制作所有这些 UI 元素比你想象的要花更多的时间，所以我刚完成就没时间了。

# 第 5 天——礼品驱动型营销

在开发产品时，你必须引起人们的兴趣，让他们对产品感到兴奋。当构建一个成熟的产品时，这已经很困难了，但是我设定的短时间框架使它变得更加困难。

还记得礼物功能吗？它允许你分享一篇嵌入文章的登陆修改版本。我用它来让其他人通过赠送他们文章的音频版本来了解 Blogcast。

> ![Miguel Piedrafita🐘 profile image](img/7b627ca48efb49891e6e8badf73259d8.png)米盖尔🐘[@ m1 guelpf](https://dev.to/m1guelpf)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)👋🏻 [@makermag](https://twitter.com/makermag) 战队！我给你烤了点东西🎁:
> 2019 年 1 月 17 日上午 00:54[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1085701935292919808)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1085701935292919808)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1085701935292919808)

这引起了很大的反响，让人们对博客感兴趣，最重要的是，让人们开心😁

> ![David Llop profile image](img/8ca96b1264c0f497b8cd6028236b351d.png)大卫 Llop[@ lloop](https://dev.to/lloople)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ them said](https://twitter.com/themsaid)[@ m1 guelpf](https://twitter.com/m1guelpf)让我说一句，这种宣传你的 SaaS 的方式实在是太棒了，太好了👏👏👏2019 年 1 月 17 日上午 06:36[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1085787997520490496)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1085787997520490496)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1085787997520490496)

我还花了一些时间制作了一个设置页面，允许用户更改凭据、更新信用卡详细信息、更改计划和查看发票。下面是完整用户界面的演示:

> ![unknown tweet media content](img/e935705dce93ce173ab1a0b53ac9033e.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/ext_tw_video/1086321199637057538/pu/vid/384x180/2OHmrEo7R5H7y5FZ.mp4?tag=8" type="video/mp4"></video>![Miguel Piedrafita🐘 profile image](img/7b627ca48efb49891e6e8badf73259d8.png)🐘[@ m1 guelpf](https://dev.to/m1guelpf)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)终于完成了我新产品的 UI 工作！还是觉得要拧一下，不过这里提前预告一下:
> 
> [blog cast . host](https://t.co/5JI2Ku8Eh5)2019 年 1 月 18 日下午 17:55[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1086321289923624961)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1086321289923624961)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1086321289923624961)

# 第 6 天——功能完成！

只剩下一个功能:语音选择。直到那一刻，选择的声音是硬编码在后端，但我想让它可定制。这也允许用不同的语言和口音抄写文章。

在后端实现这一点并没有花费太多时间。我只需要使硬编码的声音动态化，并列出所有可用的声音。但是，你可能已经猜到，前端是一个不同的故事。

一个语音选择选项不适合当前的用户界面，所以我把它变成了一个模态，并让创建文章按钮打开这个模态。这样，我没有为声音设置任何默认值，完全由用户来决定。使所有这些响应也是一个可怕的大量工作，但我希望仪表板在移动和桌面上同样可用，这是许多企业甚至没有考虑的事情。

毕竟，我是超级累，但博客是功能完整！

> ![Miguel Piedrafita🐘 profile image](img/7b627ca48efb49891e6e8badf73259d8.png)米盖尔🐘[@ m1 guelpf](https://dev.to/m1guelpf)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)🎉Blogcast ia 功能完成！！！！！！！
> 
> 我列表中的下一项是“先付费渲染”，有人想帮忙吗？😁
> 
> [blog cast . host](https://t.co/5JI2Ku8Eh5)2019 年 1 月 19 日 21 点 44 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1086741265696387073)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1086741265696387073)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1086741265696387073)

# 第 7 天-产品搜寻准备

> 到了第七天，上帝已经完成了他一直在做的工作；到了第七天，他停止了一切工作，休息了。

老实说，第七天我没做多少事。我那天的目标是准备产品发布会，但是我很累，还没有做作业。所以我休息了。

我也写这篇文章，希望即使没有我想做的标志或动画缩略图，我也能在明天的 PH 上发布。

# 结论

创建博客非常有趣。我学到了很多新东西，并在很短的时间内成功打造了一款令我自豪的产品。

我感谢所有让一个 16 岁的孩子做出令人敬畏的东西的人，从我曾经学习编码的教程的作者到激励我并让我坚持下去的创客社区。让我们一起让世界变得更美好！

如果你有一个博客，试一下 [Blogcast](https://blogcast.host) 怎么样？这可能有助于你与你的观众建立联系，或者获得更多的关注。几乎还没有人这样做，这是你大放异彩的机会！