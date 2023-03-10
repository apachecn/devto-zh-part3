# NLP APIs 令人捧腹的脆弱性

> 原文：<https://dev.to/ajsharp/the-hilarious-fragility-of-nlp-apis-2api>

最近我一直在研究 [Follow Reset](https://www.producthunt.com/upcoming/follow-reset) 中需要机器学习和自然语言处理的功能，所以我一直在试验两个著名的 NLP API:[AWS 领悟](https://aws.amazon.com/comprehend/)和[谷歌自然语言](https://cloud.google.com/natural-language/)。虽然我对这些 API 的主要兴趣是它们的定制建模能力，但我很想看看我能从它们的基本实体识别和分类功能中得到什么样的快速结果。

高层次的产品目标很简单:使用 Twitter bios 提取和检测关于人的高层次分类信息。

我的主要测试案例是对乔·罗根的简介描述，他是一位非常著名的喜剧演员和播客，拥有 468 万 Twitter 粉丝(截至发稿时)。

我(天真地)希望这些 API 能够从这个描述中提取出这个人是喜剧演员乔·罗根。

结果是...哦...至少可以说，令人惊讶。

## 😕初始(奇数)结果

我使用 Joe 的 Twitter 个人资料作为两个 API 的输入:

> 站立漫画/混合武术狂热分子/迷幻冒险家乔·罗根体验#狂欢派对的主持人[http://www.facebook.com/JOEROGAN](http://www.facebook.com/JOEROGAN)

AWS 理解承认乔罗根作为一个人。好的开始。AWS 有一个称为关键短语提取的特性，不幸的是，在这种情况下，它没有添加太多的上下文，通常在这里是没有用的。

[![](img/4b97c38299f641a15b358335308a4ab4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FaCeAOVj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AF3kTMW0Hq9R6oSM3K31H1Q.png)

另一方面，谷歌实际上并不承认“乔·罗根”是一个人，尽管它将“主持人”和“综合武术爱好者”都识别为一个人实体。奇怪。

[![](img/fd0fcb06ef4baf397bb86b70eaa8a546.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7HELWxmT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2At67Mzs5qckvC0uZLhONpxQ.png)

虽然理解的实体结果往往更符合事实，(乔·罗根是一个人)，但谷歌的结果*试图*提供背景，在他的简历中识别直接的背景实体，如“冒险家”，以及一篇关于乔·罗根经历的维基百科文章——他非常受欢迎的播客——背景*不包含在简历中*。

这里突出的一点是，虽然谷歌识别分类上下文——这可能是漫画的描述——但它无法正确识别漫画类别所指的人实体。考虑到 Google 检测和提示输入字符串之外的上下文的能力，它不能正确识别 Person 实体有点令人头疼。

## 🔑改变输入，内容是关键？

如果我们把名字从乔·罗根改成亚历克斯·夏普会怎么样？

以下是更新后的输入字符串:

> 站立漫画/混合武术狂热者/迷幻冒险家亚历克斯·夏普体验#狂欢派对的主持人[http://www.facebook.com/AlexSharp](http://www.facebook.com/AlexSharp)

[![](img/658ea694bb76232bedb1fc630eaddbac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DtOozTEj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AShfIAZeHyepI0EUk4KJNWw.png)

亚马逊仍然将亚历克斯·夏普视为一个人。酷毙了。我就是那个。🙋‍♂️

谷歌的结果……出乎意料。不知何故，谷歌比乔·罗根更确信亚历克斯·夏普是个喜剧演员。呃，当然😂

[![](img/a0e4974430706132c43d46d5b7c2f606.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PVSwNE8q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A_NDLh55IHVbCn-Wn67MY9Q.png)

## 🙃乔·罗根不是乔·罗根？

如果你把乔·罗根的资本从减少到*乔·罗根的资本*，会发生什么？当我们这样做时，Amazon 不再识别 person 实体。谷歌仍然认为我们*可能*在谈论一部漫画，但不会太多。

[![](img/aa4a96c038a46cdb8898c24a595bc8b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TltQmwxS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AzDyfKp0glaKF30HMfmF-Fw.png)

如果我们用一个虚构的角色的名字来代替，或者如果我们改变名字周围的一些单词的大小写，会发生什么？这里我把名字从*亚历克斯·夏普*改成了*罗纳德·麦当劳*。

如果一个人实体的*没有被其他大写单词包围*，那么这两个 API 似乎都有一个更容易识别的时间**。**

[![](img/a08006b5a95bf536adc7a8840a4415d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N1F4Z-5M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2APTAl0_xleI70BU4rxHlAow.png)

亚马逊承认这两种形式，但当*罗纳德麦当劳*没有被大写单词包围时，它的可信度要高得多。

谷歌的结果更加明显，如果一个词被其他大写名词包围，它根本无法识别一个人实体:它将整个短语*麦当劳经历*识别为“其他”。它以为是什么东西，但不知道是什么。

虽然这些结果对于门外汉来说有逻辑意义——大写的单词通常是专有名词——但这些产品依赖于如此基本而脆弱的语法规则有点令人失望。

[![](img/96340db0b6d2ac107485bc9bfe5fa3ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--irZn7mma--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AK8JppWlNptSComUDr_XipA.png)

[![](img/ce30042124722fe78f2e9ce6bba53d52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fbL5GJVO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A31cOyb_Woa0IOWLWSHB5xQ.png)

## ☝️Massively 对亚马逊的不合格建议&谷歌

在我看来(*巨大的警告*:我是一个完全的人工智能业余爱好者)，人实体识别问题可以通过根据名字是否匹配(或不匹配)字典单词来识别名字来改善。单词 *joe* 和 *rogan* 都不是字典中的单词，所以我们可以合理地假设，带着适度的信心，配对在一起， *joe rogan* 可能是某人的名字？我不知道，我已经远远超出了在我几乎不了解的游泳池的深水区游泳的能力🤷🏻‍♂️.

## 🚫🦄这里没有魔法

正如我们所见，这些 API 可能**非常脆弱**，经常以奇怪和意想不到的方式对输入的微小变化做出反应。

它们似乎围绕着相当基本的语法“规则”运作——专有名词必须大写，专有名词必须独立存在——这些规则在许多情况下非常脆弱，尤其是考虑到我们许多人并没有在互联网和社交媒体上用学术认可的语法写作。把一个大写字母换成小写字母，整个事情就完了，可怜的乔·罗根就被剥夺了人格。悲哀。😟

[![](img/b01b9163b579d3848266dc470de10d11.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GVMXiii6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2AKruDl3ONh8_h0c6CPpD29w.jpeg)

除了这种基本上令人乏味但令人挠头的好奇心练习之外，在[深度学习驱动的](https://medium.com/syncedreview/best-nlp-model-ever-google-bert-sets-new-standards-in-11-language-tasks-4a2a189bc155)机器学习和 [NLP](https://pytext-pytext.readthedocs-hosted.com/en/latest/index.html) 中还有一些真正令人兴奋的工作。不幸的是，AWS understand 和 Google AutoML 感觉更像是一种技术的训练轮版本，可以在我们想到单词之前准确地键入单词，在复杂的策略游戏中击败人类，驾驶汽车等等。不幸的是，这些 API 除了基本的语法分类和情感分析(这里没有涉及)之外，对其他任何东西都没有什么吸引力。

* * *

*🙏感谢阅读。如果你有兴趣了解更多启发了这篇文章的产品，请查看并订阅* **关注重置**、*，这将很快使清理你的 Twitter 订阅变得非常容易。*