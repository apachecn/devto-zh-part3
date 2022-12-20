# 上周前 5 名开发者评论

> 原文：<https://dev.to/thepracticaldev/top-5-dev-comments-from-the-past-week-2p1a>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

**[你的 2018 年数字](https://dev.to/ben/your-2018-in-numbers-1l2k)** 提供了一种非常有趣的方式来回顾人们每年的成就。基本上，你应该跳进去，用表情符号列出你过去一年的个人指标。在 [@helenanders26](https://dev.to/helenanders26) 回顾:
查看格式

[![helenanders26 profile image](img/de870391d330d398eb6b423c0d3e62be.png) ](/helenanders26) [ Helen Anderson ](/helenanders26) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/helenanders26) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/helenanders26) [<time datetime="2018-12-31T21:40:02Z">Dec 31 '18</time>](/helenanders26/comment/7ohm)

我的 2018:

📝25 篇博文(8 月以来)
📖100k 博客阅读量(8 月以来)
🛩 9 趟
💾2 数据库迁移
工作中使用的🛠 5 AWS 服务
👩‍🏫举办了 12 场分析师研讨会
👯‍♀️ 10 名初级分析师登上
📚30 小时投入 AWS 研究(自 8 月起)
👩‍🎓2 个团队“牛逼奖”和年终获奖者
☕️这么多咖啡...

讨论中 **[你最喜欢的 JS 面试问题是什么？](https://dev.to/emmawedekind/whats-your-favorite-js-interview-question-1n0d)** ， [@ufocoder](https://dev.to/ufocoder) 提出了一个满意的问题:

[![ufocoder profile image](img/88a571c7537be6753635ebc0cd0c019f.png) ](/ufocoder) [ Sergey Ivanov ](/ufocoder) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/ufocoder) [<time datetime="2018-12-26T22:59:02Z">Dec 26 '18</time>](/ufocoder/comment/7mlb)

这些例子会输出什么？为什么？

```
for (var i=0; i < 10; i++){
    setTimeout(function(){
        console.log(i);
    }, 1000);
} 
```

怎么固定输出 0 到 9 的数字？

与此同时， [@eljayadobe](https://dev.to/eljayadobe) *在 **[展示了](https://dev.to/itsjzt/dont-set-a-resolution-this-year-1h4n)***的幽默感，今年不要下决心。

[![eljayadobe profile image](img/b4f7d703ab580c7a686e1fbb5f1fc960.png) ](/eljayadobe) [ Eljay-Adobe ](/eljayadobe) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/Eljay-Adobe) [<time datetime="2018-12-31T04:00:08Z">Dec 31 '18</time>](/eljayadobe/comment/7oba)

我的新年决心:5120 宽 2880 高。

在 **[中，一个初级、中级和高级开发人员走进一家酒吧](https://dev.to/anabella/a-junior-a-mid-and-a-senior-dev-walk-into-a-bar-414f)** ，我们得到了一个关于数据库迁移的警示故事，而 [@scotthannen](https://dev.to/scotthannen) 提醒我们测试是多么重要:

[![scotthannen profile image](img/2749bf5eb13837d52df24bf58732c542.png) ](/scotthannen) [ Scott Hannen ](/scotthannen) [![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png)](https://twitter.com/scotthannen) [<time datetime="2018-12-30T03:05:07Z">Dec 30 '18</time>](/scotthannen/comment/7o2f)

希望你的文化不是责备。

为了使这种迁移工作，您必须非常小心地确保它完全正确。如果它爆炸了，很容易说你应该更加小心。当然，你说你下次会更小心。

这听起来不错，但在这样一个环境中，每个人都必须非常小心，否则一切都会变得一团糟，这样的环境往往会面临灾难性的失败。我去过那里。

开发人员会犯错误。这就是现实。如果我们理解它并与之合作，而不是反对它，它就不必破坏生产系统。

这是我们为代码编写单元测试的一个原因。我们抓住小错误。如果我们没有犯小错误，那为什么还要写单元测试呢？

我们还测试我们的应用程序的行为，包括 QA 测试。如果我们从未引入错误，为什么要测试我们的应用程序呢？

然后我们部署我们的代码。正如你所经历的，这可能是一个复杂的过程。我们测试那个部署过程吗？我们测试所有其他的东西，那么为什么我们不测试最有可能破坏所有东西的部分呢？

这意味着要有一个完全反映生产环境的试运行环境，在那里我们可以测试我们的部署。集装箱化也有帮助。

不为这样的测试提供一个登台环境是你个人的想法吗？我打赌不是。灾难性故障的风险是您的部署流程中固有的。这是时间问题。

“让我们都更加小心”不是一个答案。我怀疑你或其他任何人是粗心的。人类根本无法心算每一个变量，也无法想象复杂系统的行为方式。(我们有时做得不错，但依赖这一点是一个可怕的想法。)如果我们能做到这一点，我们就不需要电脑了。我们可以在脑子里做任何事情。

测试一切，包括拥有这样的测试环境，才是答案。

最后，说到搞砸，阅读 **[甚至大的搞砸](https://dev.to/dstarner/even-the-big-ones-mess-up-51dp)**out 会让你感觉好一点。我们知道[@本](https://dev.to/ben)的感受:

[![ben profile image](img/aafbcbcc2891483db855a7b6ec39b85c.png) ](/ben) [ Ben Halpern ](/ben) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/bendhalpern) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/benhalpern) [<time datetime="2018-12-27T20:31:49Z">Dec 27 '18</time>](/ben/comment/7n53)

每次我在野外遇到 500 个错误，我都会松一口气。

下周见，更多精彩评论，✌