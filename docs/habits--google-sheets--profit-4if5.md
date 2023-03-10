# 习惯+ Google Sheets =利润！

> 原文：<https://dev.to/hector6872/habits--google-sheets--profit-4if5>

## 因为好习惯值得

[![](img/3dc73f69b8cc5188cbffd845c5ee58da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--shvCuVU4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/hrules6872/Writings/master/9-habits-google-sheets/art/1.jpeg)

* * *

更新:在使用下面的解决方案两年多之后，我终于决定创建自己的原生 Android 应用。试试看！

[**安卓牵引 App**](https://play.google.com/store/apps/details?id=com.hector6872.habits)

* * *

### 什么是新年决心？

一个**新年决心**是一个[传统](https://www.history.com/news/the-history-of-new-years-resolutions)，在这一年的最后几天左右，我们对自己做出一系列承诺，为了在未来的一年里以一种好的方式改善我们的行为或生活方式(即戒烟、少吃垃圾食品、减肥、做某种形式的运动和/或停止说 JS 是一种真正的编程语言)。

尽管有些承诺可能只是个人目标或挑战(如独自旅行)，但几乎我们做出的每一个这类承诺都或多或少地成为了一种习惯(要么成功，要么失败)。

### 什么是习惯？

习惯是一种行为模式，**通过频繁的重复**获得，我们经常无意识地——无意地和不受控制地——对一个已知的 cue⁴.做出反应

### 谷歌和这一切有什么关系？

我们中的大多数人都曾许诺打破一些坏习惯作为新年决心，然后在暑假后意识到-充其量-我们甚至没有开始。然后，又一年过去了…

> # Tracking our habits⁵—— every day—or at least once a week—is the best way to keep us motivated and show us how well (or badly) we are doing.

我尝试了很多应用程序和网站来追踪习惯，但不管出于什么原因(价格、缺乏出口选择、不确定的未来等等)，我对它们都不满意。然后，我和两位大型组织专家[Jesus cervio](https://dev.toundefined)&[David 进行了交谈。s](https://dev.toundefined)——我很幸运能有他这样的同事——有人告诉我，有了广泛使用的强大的 tool⁶，我可以做任何我想做的事情(甚至更多): **[Google Sheets](https://www.google.com/sheets/about/)** 。

## 首先我们养成习惯，然后习惯造就我们

在做了一些研究后，我基于[哈罗德·金](https://dev.toundefined)的出色工作，创建了一个改进的 **[习惯跟踪器模板](https://docs.google.com/spreadsheets/d/1pEvbxS_O7kb7jnMxRvLJhh2v7g4yvFesUV5_YmSVmrw/edit?usp=sharing)** 。

> # Log in to your Google account, copy my **[habit tracker](https://docs.google.com/spreadsheets/d/1pEvbxS_O7kb7jnMxRvLJhh2v7g4yvFesUV5_YmSVmrw/edit?usp=sharing)** template (copy the file > …), and fill in your [smart goals and habits](https://en.wikipedia.org/wiki/SMART_criteria#Current_definitions) in the activity column.

然后，清除样本数据，你将能够跟踪你自己的习惯，把一个 **X** (或者😄/🙁取决于你的心情)在你想要标记为**完成**的每个单元格上。

**[不要断链！](https://lifehacker.com/jerry-seinfelds-productivity-secret-281626)T3】**

### 特性

*   **条纹**和**最大条纹**

*   **进展**基于您在**预期**栏中输入的值(必须在 days⁷)

*   突出显示当天

*   每月更换颜色

*   基于日期自动隐藏列

提示:你甚至可以通过[内置的笔记功能](https://www.prolificoaktree.com/google-sheets-comment-vs-note/)将习惯跟踪器用作与习惯相关的日志。

> # Don't forget to set a regular reminder (you can also use [Google Calendar](https://www.google.com/calendar) by the way) so that you won't forget to record your habits. Then get into this habit!

## 结论

最重要的是，**不要成为你(坏)习惯的奴隶……**和[要像比尔](https://imgflip.com/i/2sovqj)！

本文最初发表于[媒体](https://medium.com/@hector6872/habits-google-sheets-profit-aef90faba4f0)

* * *

【1】最常见的新年决心在[英国](https://www.comresglobal.com/polls/bupa-new-year-resolution-survey/)(2015 年 12 月)和[美国](http://d25d2506sfb94s.cloudfront.net/cumulus_uploads/document/366cvmcg44/New%20Year%20Survey,%20December%208%2011,%202017.pdf)(2017 年 12 月)
【2】只是吊儿郎当，别喂我🐟
[3]养成一个新习惯需要 18 到 254 天(平均 66 天)。不幸的是，[没有神奇的数字](https://jamesclear.com/new-habit)。
【4】关于习惯和意志力的一个很好的提示:*我们不应该关注行为，而应该[关注线索](https://markmanson.net/downloads/habits)(即地点、一天中的时间、情绪状态、信仰、其他人等等)来培养新的习惯(或打破它们)*
【5】
【6】它是免费的、多平台的，允许我们导出数据并离线工作💪
[7]记住，普通的一年有 52 周(也就是说，如果你想每周锻炼 3 次，你只需在那个单元格中填入`=3*52`)👨‍🎓

* * *

[外部链接👀](https://gist.github.com/hrules6872/c285131c025569487e88df2e19d353fa)