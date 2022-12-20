# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-20o2>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

我喜欢一个很好的宣泄式的咆哮，还有那个 **[Sh*tpost:我们能不能不说“句法糖”？](https://dev.to/jenninat0r/shtpost-can-we-stop-saying-syntactic-sugar-3i4j)** 也不例外。 [@marek](https://dev.to/marek) 插话提供了一些关于这个主题的有用想法:

[![marek profile image](img/b91d35aed548547a676f86cd5553f7d9.png) ](/marek) [ Marek Zaluski ](/marek) • [<time datetime="2019-01-18T20:33:14Z" class="date-short-year"> Jan 18 '19 </time> • Edited on <time datetime="2019-01-18T20:34:26Z" class="hidden m:inline-block date-no-year">Jan 18</time>](https://dev.to/marek/comment/8871) 

之所以称之为句法糖，是因为它没有给语言引入任何实质性的新特性，它只是一种更甜的句法。

所以是的:析构和`+=`是语法糖的例子，因为你可以用更长、不太简洁的形式来写它，而且它是等价的。

作为旁注，有时动词“去糖”用来表示“变成不加糖的等价句法”，如这个例句:

> `a += b`脱糖至`a = a + b`

Async/await 不是一个真正的语法糖的例子，它是语言中的一个全新特性。您可以重写 async/code 以使用`then` / `catch`处理程序，但这不仅仅是简单的语法转换，而是重写。

总之，它之所以被称为“糖”，可能是因为它意味着你可以在语言语法上添加一些东西，使它变得更容易，但并不是一套全新的特性。在引擎盖下，语法糖可以作为语法树的转换来实现，所以运行时引擎甚至不需要知道它。

我同意“糖尿病”的说法:太多的语法糖不是好事，当它使代码更难阅读而不是更容易阅读时，它肯定会走得太远。

有一个很棒的对话，人们回答: **[在等待测试运行结束的时候，你在做什么？](https://dev.to/jess/what-do-you-do-while-waiting-for-tests-to-finish-running-5898)** 。帖子顶部是来自 [@sergio](https://dev.to/sergio) 的回复，他描述了 Rails/Elixir 的测试速度差异:

[![sergio profile image](img/faa19b25655a930432e55bb71b7a3029.png) ](/sergio) [ deleteme deleteme ](/sergio) • [<time datetime="2019-01-16T14:51:34Z" class="date-short-year"> Jan 16 '19 </time> • Edited on <time datetime="2019-01-16T14:59:38Z" class="hidden m:inline-block date-no-year">Jan 16</time>](https://dev.to/sergio/comment/86ga) 

当我在 Rails 工作的时候:读一些新闻或者在 youtube 上看一些搞笑的片段。

当我在《长生不老药:没时间做任何事》中工作时，测试运行得非常快。我的最长时间是 7 秒，那是在大约 800 次测试中。

[![https://i.imgur.com/IOnmN84.png](img/3373be8ad1326627b908b0fbc9970efd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DhX6Mplv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/IOnmN84.png)

在《真正伟大的文章》中， **[25 年的编码，而我才刚刚开始](https://dev.to/dechamp/25-years-of-coding-and-im-just-beginning-442n)** ，[，@tylerlwsmith](https://dev.to/tylerlwsmith) 感同身受，描述了他们与作者的关系:

[![tylerlwsmith profile image](img/ac14d33a1a1f518792adf751f736db34.png) ](/tylerlwsmith) [ Tyler Smith ](/tylerlwsmith) • [<time datetime="2019-01-17T08:08:30Z" class="date-short-year"> Jan 17 '19 </time>](https://dev.to/tylerlwsmith/comment/8751) 

我绝对有这种感觉。两年前，我辞掉了工作，成为了一名网站开发人员，因为我有几个个人项目想要建立。事实上，我可能是一个足够称职的开发人员，能够在成为开发人员后的 10 个月内构建出一个可接受的版本，但是有两件事总是让我止步不前:

1.  发光物体综合症。我想用最好的平台来做这个项目，所以我从 WordPress，到 Laravel，到 Django，然后开始在无服务器上阅读。这是一次很棒的学习经历，但我什么也没做。

2.  我想要更多的功能，而不是我一个人能够合理地生产出来的。

我被困了一年没有推出任何东西。

我认为对我有帮助的是阅读一堆关于最小可行产品(也称为 MVP)的文章，并对什么都不做感到厌倦。我把目标从大约 10 个功能降低到 1 个功能，并在 14 天内在 WordPress 中建立了一个可行的网站。它在 WordPress 中并不重要，因为还没有人访问这个网站，所以可扩展性完全不是问题🤷‍♂️

过去两周，我一直在试图找出如何推广我认为非常酷的东西，尽管代码库有点乱，但我真的很高兴，没有人关心代码。

当你投入到你的项目中时，祝你好运，如果你专注于把代码弄出来，你会做得很好！

在一场关于 Slack 新 logo——**[的讨论中，Slack 有了新面孔](https://dev.to/forstmeier/slack-has-a-new-face-e1f)**——[@ objque](https://dev.to/objque)基本上赢了跟帖🦆🦆🦆🦆:

未找到注释

本周，@ Derek hopper 给出了一个关于如何考虑为开源做贡献的精彩评论。整个 **[对初学者的开源贡献？！](https://dev.to/coreyrodgers95/open-source-contribution-for-beginners-pf7)** 文章和讨论值得一读:

[![derekjhopper profile image](img/4d978c9ff19a270aeadbe9f755ddd52b.png) ](/derekjhopper) [ Derek Hopper ](/derekjhopper) • [<time datetime="2019-01-17T16:32:26Z" class="date-short-year"> Jan 17 '19 </time>](https://dev.to/derekjhopper/comment/87d1) 

在你的工作中有没有使用开源项目？这是一个很好的开始。偶尔，我会看看我正在做的项目中的依赖项，看看它们是否有我可以帮助解决的问题。这是回报的好方法。

此外，贡献不需要很大或复杂。我所做的贡献中，我唯一帮助的就是修复文档中的一些拼写错误或打字错误。

当你想做出贡献时，给你一些一般性的建议:

*   首先感谢项目的作者。友善总是有帮助的。
*   阅读他们的投稿指南。大多数项目都有一个。例如，一些项目不接受新特性。
*   如果你不确定他们是否会接受改变，打开一个问题，提出你的改变。问他们是否会感兴趣。
*   如果你发现了一个 bug，提交一个失败的测试用例会有所帮助。然后如果他们问你是否有时间修理，你已经在路上了。
*   试着以一种比较及时的方式进行交流。有时候，一个拉取请求可能会经历几轮变更。及时的沟通有助于使每个人的过程更容易。
*   遵循项目制定的惯例。如果他们以某种方式编写测试，试着遵循这种方式，而不是你自己的风格。
*   当你打开一个拉取请求时，尽量解释清楚。如果项目有一个拉请求模板，请遵循它。
*   总的来说，要友好，容易相处。没有一个开源维护者会因为你的友善而生气。

祝你好运！为开源做贡献是一项值得的努力。如果你把它变成你经常做的事情，总有一天你会有一些贡献。

下周见，更多精彩评论，✌