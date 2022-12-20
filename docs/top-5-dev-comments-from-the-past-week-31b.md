# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-31b>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

回复**[JavaScript 社区里我最喜欢的老师](https://dev.to/emmawedekind/my-favorite-teachers-in-the-javascript-community-53h)** ， [@alemesa](https://dev.to/alemesa) 名单里又增加了一些很棒的名字:

[![alemesa profile image](img/ba408083d4b22b553f3ba3925e8ec033.png) ](/alemesa) [ Alejandro Mesa ](/alemesa) • [<time datetime="2019-04-04T02:01:44Z" class="date-short-year"> Apr 4 '19 </time> • Edited on <time datetime="2019-04-08T02:02:16Z" class="hidden m:inline-block date-no-year">Apr 8</time>](https://dev.to/alemesa/comment/9p54) 

这份名单很棒，但遗漏了一些独角兽:

*   [Wesbos](https://wesbos.com/courses/) - Syntax.fm 主机和许多疯狂的质量教程
*   泰勒·麦金尼斯 -泰勒是个奇才，他分解复杂概念的能力无人能及
*   Scott Tolinski - Syntax.fm 主持人和基于订阅的课程
*   丹·希夫曼——滑稽的家伙
*   布拉德·特拉弗斯(Brad Traversy)——Youtube 之王
*   网络忍者 -非常棒的 youtube 频道，他被大大低估了
*   弗拉维奥·科普斯(Flavio Copes)——我发现他的书面教程非常清晰简洁

Udemy 乐队也很棒

*   [马克西米利安黑黑字](https://www.udemy.com/user/maximilian-schwarzmuller/)
*   安德鲁·米德
*   斯蒂芬·格里德
*   安德烈·尼戈
*   [Jonas schmerdt Mann](https://www.udemy.com/user/jonasschmedtmann/)

[@denmch](https://dev.to/denmch) 回复 **[在没有 jQuery](https://dev.to/mrahmadawais/use-instead-of-document-queryselector-all-in-javascript-without-jquery-3ef1)T5】的 JavaScript 中使用$ & $$代替 document.querySelector/All，附带一些关于使用冗长标识符的额外提示:** 

[![denmch profile image](img/067268fe8c6da2c6f182def39135702b.png) ](/denmch) [ Den McHenry ](/denmch) • [<time datetime="2019-04-02T19:40:00Z" class="date-short-year"> Apr 2 '19 </time>](https://dev.to/denmch/comment/9o9n) 

如果你在一个分布式团队中工作，或者使用遗留代码，并且考虑到潜在的冲突(主要是与 jQuery，但也有一些其他的库，以及任何有人可能使用`$`代替`document.getElementById()`的代码，这在过去是很常见的)，我认为使用像这样稍微长一点的标识符是一个好主意:

```
const $q = document.querySelector.bind(document);
const $qa = document.querySelectorAll.bind(document); 
```

Enter fullscreen mode Exit fullscreen mode

这类似于在 noConflict 模式下使用 jQuery 的常用方法，例如:

```
const $j = jQuery.noConflict(); 
```

Enter fullscreen mode Exit fullscreen mode

如果您经常使用它们，它可以明智地扩展到类似的方法。

**[学习爱软件开发书籍💙](https://dev.to/lightalloy/learning-to-love-software-development-books-5e4)** 是一场关于通过书本学习，而不是在线视频和教程的大讨论。[@洛基德夫](https://dev.to/lokidev)附和了一些建议:

[![lokidev profile image](img/937d653109be318557da956811f8a423.png) ](/lokidev) [ LokiDev ](/lokidev) • [<time datetime="2019-04-04T18:20:14Z" class="date-short-year"> Apr 4 '19 </time>](https://dev.to/lokidev/comment/9pj6) 

我非常喜欢编程书籍。我甚至更喜欢通用书籍(比如《人月神话》——这本书还在我的书堆里)。

我读过的最好的多用途软件工程/编程书籍是:

*   务实的程序员(托马斯·亨特)
*   干净的编码者(罗伯特·c·马丁)
*   干净的编码者(又是马丁)
*   干净的建筑(还有:D)

特定语言:

*   Python:用 Python 进行测试驱动的开发(Harry J.W. Percival) -这本书可以购买，但也可以免费在线阅读-> obeythetestinggoat.com
*   尼姆:尼姆在行动
*   Go:在 Go 中构建微服务

仍然在我的堆里:

*   神话中的人月
*   产品领导地位
*   人件(软件学上新的概念)
*   代码完成
*   迅速发展
*   (还有很多虚构的书)

**[工作区周三:炫耀你的办公桌/电脑设置！](https://dev.to/ben/workspace-wednesday-show-off-your-desk-computer-setup-2di4)** 是展示我们办公桌和工作空间的好机会。很多人喜欢 [@steveblue](https://dev.to/steveblue) 的设置:

[![steveblue profile image](img/324f14b1391f0a0dfc72b3f658529b42.png) ](/steveblue) [ Stephen Belovarich ](/steveblue) • [<time datetime="2019-04-03T20:05:26Z" class="date-short-year"> Apr 3 '19 </time> • Edited on <time datetime="2019-04-03T20:07:00Z" class="hidden m:inline-block date-no-year">Apr 3</time>](https://dev.to/steveblue/comment/9p15) 

[![](img/6dd4bc962360344d762710e57707d69e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--swn0tzcj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/am084k321d60uyu39px8.jpg)

我的工作空间肯定是一个正在进行的工作，但我喜欢它！这是我用来 100%远程工作的桌子。

最后，在 **[CSS 很难——如何学习正确使用和编写 CSS？](https://dev.to/antjanus/css-is-hard-how-do-you-learn-to-use-and-write-css-properly-5eka)** ， [@horus_sky](https://dev.to/horus_sky) 提供了很多学习 CSS 的小技巧。这篇评论可能会被转化成一篇好文章😉:

[![horus_sky profile image](img/8010f6facce2c3959aeac3c4b79cf504.png) ](/horus_sky) [ Cedric W ](/horus_sky) • [<time datetime="2019-04-05T17:15:21Z" class="date-short-year"> Apr 5 '19 </time> • Edited on <time datetime="2019-04-05T22:01:20Z" class="hidden m:inline-block date-no-year">Apr 5</time>](https://dev.to/horus_sky/comment/a08e) 

TBH:我不认为 CSS 很难。像任何其他语言一样，如果你不坚持使用它，它就不会持久。我发现 JS 很难，因为它不是我经常使用的语言。说到这里，我想说:

### 先学基础

*   在尝试一个框架之前，要与基础知识保持同步。
*   始终保持资源开放，w3schools.com，css-tricks.com，开发或 Mozilla。

### 先学习布局技术，再学习框架

在很多情况下，如果你学会了如何利用 grid 和 flexbox，你并不真正需要一个框架。但是，我将它们与 Bootstrap 结合使用。不管怎样，这些技术本身就非常强大。

*   如果使用 **CSS 网格**使用克里斯科伊尔斯指南作为资源:【css-tricks.com/snippets/css/comple...】T2
*   如果使用 **Flexbox** 使用克里斯·科伊尔斯的指南作为资源:【css-tricks.com/snippets/css/a-guid...】T2

### 用特异性征服级联

如果你发现自己想知道为什么一个样式会被覆盖，这可能是因为一个类太笼统而不够具体。这就是 CSS 特异性的用武之地。

*   了解一下 **CSS 特异性**，Emma Wedekind 有一篇很棒的文章谈到了这个:[![emmawedekind image](img/36e4996c7a6d24ee780c07d6a827d560.png)](/emmawedekind) [## CSS 特异性

    T12】艾玛 韦德金德 ✨1 月 21 日四分钟阅读# CSS](/emmawedekind/css-specificity-1kca)

### 学会集中所有的东西

有些情况下，您可能需要将行内元素、块元素、flex、网格元素或纯文本居中。每种情况都有不同的最佳实践。

*   学习垂直、水平或两者居中元素。克里斯·科伊尔对此有一个指南。[css-tricks.com/centering-css-compl...](https://css-tricks.com/centering-css-complete-guide/)

### 下一步高级学习

一旦你对基础知识和以上所有内容更加熟悉和熟悉，尝试:

**嗯**:(块元素修改)

*   使用边界元法的 5 个理由:[blog.elpassion.com/reasons-to-use-...](https://blog.elpassion.com/reasons-to-use-bem-a88738317753)
*   [![adamaso image](img/32fff3c99866027b402e29a5930c1f94.png)](/adamaso) [## 有好的，好的的朋友

    ### 安吉拉 达马索 1 月 30 日 183 分钟阅读

    【CSS】【sass】【体系结构】【well】](/adamaso/friends-with-bem-efits-4ehp)

**SASS** :(语法上令人敬畏的样式表)

*   萨斯基础知识:[sass-lang.com/guide](https://sass-lang.com/guide)
*   [![akanksha_9560 image](img/98a7c20762c2a2f45d42400a8b1648b2.png)](/akanksha_9560) [## Sass Beginner's Guide

    ### Akansha Sharma April 21, 2018 Read

    for 3 minutes#sass #css](/akanksha_9560/beginners-guide-to-sass-8lh)

【Sass 工具:

*   Prepros: [prepros.io/](https://prepros.io/)
*   考拉:[koala-app.com/](http://koala-app.com/)
*   程式码片段:T0 程式码片段. com/index . html
*   VSCode 也有一些针对 SASS 的本地处理和扩展

希望这有所帮助。

下周见，更多精彩评论，✌