# 我对新程序员问题的回答

> 原文：<https://dev.to/aspittel/my-answers-to-questions-from-new-coders-gf>

作为我的[博客问答帖子](https://dev.to/aspittel/developer-blogging-qa-2c9b)的后续，我询问了新程序员的问题，我将在这里回答他们中的一些！

> ![Ali Spittel 💁 profile image](img/b53b95ff44d646255c2b871501c17b6b.png)阿里·斯皮特💁[@ aspittel](https://dev.to/aspittel)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)新程序员！关于学习如何编码，你最大的问题是什么？
> 
> 下周初我会在一篇 Q &的博文里尽可能多的回答！
> 
> [【code newbie】](https://twitter.com/hashtag/CodeNewbie)[【100 daysofcode】](https://twitter.com/hashtag/100daysofcode)2019 年 2 月 15 日下午 13:28[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1096400956202409985)![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)47[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1096400956202409985)143

## 什么时候开始做项目？

> ![Racheal Id profile image](img/2e8247805915d42bba0e85d1bde847c0.png)

越快越好！当你知道了一门语言的基础，你就可以开始把这些东西放在一起，构建一些小东西。而且，如果你心中有一个想法，它可以帮助引导你学习更多的东西。在了解了一门语言的基础知识之后，你可以构建许多小的命令行游戏。利用 web 开发的基础知识，您可以构建更酷的东西！

## 一个 dev 应该具备的最重要的技能是什么？

> ![Ricardo D profile image](img/3edd0ed35b1fc3405fe82e6b30f4cdff.png)李嘉图 D@ hdandhf![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ ASpittel](https://twitter.com/ASpittel)一个开发人员要想进入一个真正的开发人员岗位，最重要的技能是什么？2019 年 2 月 15 日下午 14:13[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1096412197939568640)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1096412197939568640)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1096412197939568640)4

如果让我挑选一件事，那就是高效地自学新事物的能力。作为一名开发人员，你将会不断成长和学习新技术。做到这一点的能力比任何单独的工具或技术都更重要。

## JavaScript 参数是如何工作的？

> ![Odee Onakii  🧦✖️🎶 profile image](img/4991b8fad1760dbba1c4b6fd527b1f83.png)奥迪·大木🧦✖️🎶@ choirsandpianos![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ ASpittel](https://twitter.com/ASpittel)[@ venikunche](https://twitter.com/venikunche)我想了解一些关于 JavaScript 参数的说明，如何正确地使用它们，以及两个或更多的参数应该以什么顺序传递给一个函数以获得想要的结果。谢谢你。2019 年 2 月 15 日下午 15:38[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1096433554282762240)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1096433554282762240)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1096433554282762240)3

在我开始之前，先快速分析一下行话:

*Arguments*——每次调用时传递给函数。

*参数* -函数定义中的变量。

在`function myFunction(x, y)`中，x 和 y 是参数。当我们通过运行`myFunction(1, 3)`调用该函数时，1 和 3 是参数。

当我教授函数时，我试图用两种方式来教授它们，以便对两种不同类型的思考者有意义。第一个是可重用的代码块，您可以将值插入其中，以使您的代码更加通用，并允许更少的重复代码。在这种情况下，参数是插入到代码块中的“动态”信息。所以，当你调用这个函数时，这个值可能会改变。几乎是一个变量，每次运行函数时都不一样。

我也喜欢把函数解释为一系列的输入和输出——有点像一个小机器。你把东西放进机器里，然后就有东西出来了。参数是你输入到机器中的东西，返回值是输出的内容。这更符合函数的代数定义——如果你还记得学校数学中的`f(x) = 2x + 1`,这些函数只是写在纸上，而不是以编程方式编写的。

在 JavaScript 中，传递给函数的参数顺序对应于函数声明中参数的顺序。所以，如果我的函数声明看起来像`function add(x, y)`，然后我用`add(1, 2)`调用函数，在函数中 1 将是`x`，2 将是`y`。如果我改为跑`add(100, 50)`，`x`会是 100，`y`会是 50。由于`x`是我的第一个参数，我传递给函数的第一个参数将是`x`，由于`y`是第二个，我传递的第二个值将是`y`。有时候把这个用图表表示出来会有帮助。

```
function subtract(x, y) {
  return x - y
}

add(5, 2) // 3, 5 is x, 2 is y
add(200, 50) // 150, 200 is x, 50 is y
add(20, 70) // -50, 20 is x, 70 is y 
```

| x | y | 减去(x，y) |
| --- | --- | --- |
| five | Two | three |
| Two hundred | Fifty | One hundred and fifty |
| Twenty | Seventy | -50 |

## 你认为具备良好的工作知识对哪种语言很重要？

> ![Mark Shaffer profile image](img/f1863a1910ef0f3957b40f13c96f27e0.png)Mark Shaffer[@ codemelted](https://dev.to/codemelted)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ ASpittel](https://twitter.com/ASpittel)具备良好的工作知识，你认为哪些语言是重要的？2019 年 2 月 15 日下午 14:51[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1096421851826438145)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1096421851826438145)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1096421851826438145)3

我认为这是超级技能集特有的。我对数据科学和 web 开发了解最多，因为这是我职业生涯的起点。对于数据科学，我会说 Python 和 R 是现在的支柱，我至少知道其中之一。对于 web 开发，HTML/CSS/JavaScript 是你唯一需要的。在这一点上，您可以用 JavaScript 构建任何东西——它是迄今为止最通用的语言。

## 当技术人员告诉你不要继续时，你该如何继续？

> ![Jewel Barnett,Ph.D. profile image](img/c37478ad02153c194c8e79eeae733d08.png)Jewel Barnett 博士@ jewelbarnettphd![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ ASpittel](https://twitter.com/ASpittel)当科技界的人告诉你不要坚持下去的时候。2019 年 2 月 18 日下午 16:39[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1097536189194735619)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1097536189194735619)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1097536189194735619)3

> 也希望对询问冒名顶替综合症的人有所帮助！

这是我仍在处理的事情，我没有一个完美的答案。事实上，我在大学时就放弃了编程，因为我认为自己不够好，处理人们对我说的粗鲁的话是一场斗争，这常常让我想到职业转型。但是，我试着继续思考让我留在科技行业的事情。它所允许的向上流动，我遇到的令人敬畏的人，我建造的很酷的东西。尽管困难重重，但这些都是值得的。我想到那些人如此伤害他人会受到多大的伤害，以及他们一定会感受到来自他人的威胁——这种威胁不是来自一个安全的地方。双方都缺乏信心，这只是在他们这边表现得更加有害。

我还记录了我的成功:我做的很酷的东西，人们的积极反馈，我取得的成就。然后，在艰难的日子里，我可以回到那个文档，提醒自己做过的很酷的事情。

## 编程逻辑和解题如何变得更好？

> 相当多的推文在这里！

这是一个我可以永远继续下去的话题，实际上我正在就这个话题开一门课。敬请关注那里的更多内容。抱歉，我没有一个很好的简明答案，但它主要是关于抽象思维和将问题分解成更小的问题。

[![aspittel image](img/45a8d86fe0ff981bf7cdb45b471a3c5c.png)](/aspittel) [## 超越教程:程序员解决问题的课程

### 阿里·斯皮特尔 2 月 18 日 192 分钟阅读

#beginners #problemsolving #programming #movingpasttutorials](/aspittel/moving-past-tutorials-a-course-on-problem-solving-for-programmers-3oa4)

## 如何免费学习编码？

> ![Jeskai Affiliate profile image](img/22b7ad4faea9bae784229c548eed1f0e.png)Jeskai 附属@ bsquaredmtg![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ ASpittel](https://twitter.com/ASpittel)去哪里免费学代码，如果你很穷就去哪里入门。2019 年 2 月 15 日下午 14:22[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1096414340499030017)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1096414340499030017)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1096414340499030017)5

我有一堆我喜欢的资源给你！

[![aspittel image](img/45a8d86fe0ff981bf7cdb45b471a3c5c.png)](/aspittel) [## 我最喜欢的新程序员免费资源

### 阿里·斯皮特尔 13 年 8 月 18 日 3 分钟阅读

#beginners #learntocode #javascript #python](/aspittel/my-favorite-free-resources-for-new-programmers-bia)

## 我可以向哪里/谁询问问题？要学多久？

> ![Brykka profile image](img/efda38035c5abbbef58ae0e4f212ab08.png)布里卡@ eye balm![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ ASpittel](https://twitter.com/ASpittel)资源。我可以向哪里/谁询问问题？如果我每天花 3-4 个小时学习一门语言，那么我应该为精通这门语言设定什么样的时间表？我什么时候会停止哭泣？2019 年 2 月 15 日下午 14:16[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1096412997151449089)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1096412997151449089)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1096412997151449089)9

你也可以在 Dev.to、Twitter 或其他问答网站上提问！我也会加入你所在地区的 Slack 小组，或者如果你想私下问得更多一点的话，我会加入 CodeNewbie one。或者，参加学习编码的论坛。

就时间线而言，每个人都超级不一样。请记住，许多人花了四年的全职工作，甚至更长的时间来学习获得计算机科学学位。你可以加快这个时间表，但我认为“我在一周内学会了编码并赚了一百万美元”的成功故事是例外而不是规则。可能需要一些时间，没关系！

## 学习 Git 和 GitHub 之前需要多少经验？

> ![José Hernández profile image](img/a84314c165f9579996224a12b758b159.png)何塞·埃尔南德斯@ jlhcode![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ ASpittel](https://twitter.com/ASpittel)[@ _ 100 daysofcode](https://twitter.com/_100DaysOfCode)入门 GitHub 需要对代码了解多少？还是我根本不需要任何经验？2019 年 2 月 15 日下午 15:08[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1096426056742027265)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1096426056742027265)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1096426056742027265)4

你不需要任何！我教过完全非技术人员 Git 和 GitHub 用于商业和设计。[这个](https://lab.github.com/)是了解 GitHub 更多信息的绝佳资源！

## 先学哪种编程语言？

> ![Ali profile image](img/c84131f71961d2a8ca2028f220d884f9.png)阿里@ El _ alisami![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ ASpittel](https://twitter.com/ASpittel)我很难决定应该从哪种语言开始。我知道我必须知道我想做什么。但是我想学习基本的编码语言，然后逐渐变得更专业。那么你会推荐从哪种语言开始呢？2019 年 2 月 15 日下午 13:32[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1096401836762902528)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1096401836762902528)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1096401836762902528)10

在我不可否认的偏见看来，JavaScript 还是 Python。Python 的语法非常适合新程序员，是一种超级通用的语言。JavaScript 是构建网站的基础，你可以用它比用其他语言更快地构建有形的东西。

## 如何记东西？

> ![Kristen Truempy profile image](img/11aa7c84f44c1f210b99e63fa15e1db1.png)Kristen true mpy@ kristentrumpy![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ ASpittel](https://twitter.com/ASpittel)[@ _ 100 daysofcode](https://twitter.com/_100DaysOfCode)怎么记东西？2019 年 2 月 15 日下午 17:45[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1096465463016636417)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1096465463016636417)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1096465463016636417)2

我不会试图去记忆句法或语言方面的东西。那些可以查，或者我可以用我的文字编辑器帮我。重要的概念是概念性的，在这种情况下，我会更注重理解而不是记忆！

## 要建什么项目？

> ![Rebecca Lewis profile image](img/0e703f96115468460fbf46633a6e7990.png)Rebecca Lewis@ beckibuzz![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ ASpittel](https://twitter.com/ASpittel)人们总说要练，有什么项目是你推荐练得好的？感谢☺️2019 年 2 月 15 日下午 13:56[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1096407892863922176)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1096407892863922176)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1096407892863922176)7

我觉得游戏总是很好玩的！我也认为[这篇](https://medium.freecodecamp.org/every-time-you-build-a-to-do-list-app-a-puppy-dies-505b54637a5d)是一篇关于构建事物的很棒的博文！或者尝试重现你在网上看到的东西。

* * *

我希望这篇文章是有帮助的，如果你喜欢它，让我知道或者在下面留下问题，我可以做另一篇这样的文章！