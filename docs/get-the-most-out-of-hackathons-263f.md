# 充分利用黑客马拉松

> 原文：<https://dev.to/anoff/get-the-most-out-of-hackathons-263f>

从[https://anoff.io/blog/2018-12-09-stuttgart-hackathon/](https://anoff.io/blog/2018-12-09-stuttgart-hackathon/)上传的十字

> TL；DR；**网络**和玩昂贵的玩具

# [T1】简介](#intro)

10 月底，我第二次参加了[斯图加特🇩🇪黑客马拉松](https://www.hackathon-stuttgart.de/)。这是我的第四次公共黑客马拉松。在这篇博文中，我想告诉你为什么我喜欢参加黑客马拉松，为什么如果有机会你也应该参加！

[![Stuttgart Hackathon 2018](img/a176ced06a6ab263ea41d7a54756e32a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YZmAFzhl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://anoff.io/blog/img/assets/hackathons/stuttgart2018.jpg)

# 我过去是如何对待黑客马拉松的

我参加的第一次黑客马拉松是由蔡司在 2017 年 4 月举办的。我和 Tim Groß mann 一起组建了一个团队，在慕尼黑参加数字化创意和项目的竞赛。我们加入不是为了赢，而是为了开心和写点代码——所以在去慕尼黑的路上，我们在讨论做什么会很有趣。不知道黑客马拉松的确切范围，我们认为在网络应用程序上使用增强现实会很有趣。我们也没有设法得到我们想要包含在我们的解决方案中的任何小工具，因此我们不得不采用纯代码解决方案。

所以我们建造了[微型实验室](https://github.com/anoff/microllaborators)——这是显微镜和合作者之间可怕的文字游戏。我们和教练们讨论了这个想法，也想出了一个相当可靠的方案，包括观众在内的现场演示。我仍然喜欢我们构建的解决方案，但遗憾的是我们只获得了第五名😢

去年斯图加特黑客马拉松开始时完全不同。我没有加入一个团队，但参加了现场团队建设活动，以寻找新的编码伙伴。在发现了一个四人小组之后，发生了一件与地震相似的事情；

> 我们没有得到实现我们提出的想法所需的小工具

[![Gadgets](img/0e5fc01c165199e6f250cc4cc1dccce4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XXDeNWlZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://anoff.io/blog/img/assets/hackathons/Tokyo_Akihabara_gadgets.jpg)

所以我们摆弄了 3 个小时我们得到的东西+我们从我们的私人仓库带来的东西，然后决定我们根本不会试图竞争。相反，我们又花了 10 个小时互相教授我们每个人使用的语言、工具和程序，并在 2/3 之后离开了黑客马拉松。

# 什么变了

过去，我加入黑客马拉松主要是为了积累使用新语言、框架或小工具的经验，比如 Alexa、Hue 等。

现在我去那里有两个主要原因:

1.  建立工作关系网
2.  玩一些我永远也不会得到的东西

因为我在家也做很多兼职项目，所以我希望黑客马拉松能给我一些我的个人项目所没有的东西。因此，我不想建立一个花哨的基于区块链、AWS 托管的物联网解决方案。我可以在家里用很少的钱做这些💸。我在家里做不到的是，在这样的活动中遇到很棒的人，或者得到一些价值几千欧元的原型或工业设备💰。

# 斯图加特黑客马拉松 2018

在今年的斯图加特黑客马拉松上，我的团队就是这样做的。我们都怀着明确的目的加入，不为赢得 T1 而竞争。如果我们最终找到一个值得推介的解决方案，我们同意应该推介它。但是我们并没有把它作为我们的首要目标。我们想玩得开心——于是我们得到了其中一个坏蛋:Festo [仿生 Cobot](https://www.festo.com/group/en/cms/12746.htm) 一个由气压而不是电机驱动的人形机器人手臂。

[![Festo Bionic Cobot, copyright by Festo](img/72e8a88fe437ecbd1386171a2fcfb1b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ysay5oEA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://anoff.io/blog/img/assets/hackathons/bioniccobot.jpg)

在和机器人玩了几个小时后，我们最终想到了一个实际的项目，可以给机器人一个目标。于是机器人交互伴侣 R . I . c .诞生了。使用这个原型机器人真的不同于我迄今为止做过的任何兼职项目，因为这不仅意味着使用一个小工具，还意味着理解复杂的系统。下图显示了系统设置，其中灰色的`Client Code`是我们自己编码来控制机器人的部分。最棒的是，几位专家在现场帮助我们理解和定制 Cobot 的各个组件。

[![Cobot system](img/cfdb3d2140e89f2ad41262ca6501aecf.png)](https://camo.githubusercontent.com/d894c6fa7acc16193425edf1b75cf1bc400fa265/687474703a2f2f7777772e706c616e74756d6c2e636f6d2f706c616e74756d6c2f70726f78793f63616368653d6e6f267372633d68747470733a2f2f7261772e6769746875622e636f6d2f616e6f66662f7269632f6d61737465722f6173736574732f73797374656d2e69756d6c)

我们甚至设法将激光雷达扫描仪和回声点等一系列小工具与 Alexa 集成在一起。我们想出了一个有趣的故事来推销，我们的演示者在最后的演示中做得非常出色。

# 我未来的黑客马拉松策略

未来，我将继续参加黑客马拉松，目标是获得非消费级硬件和**网络**。如果你是一名学生，你可能会被黑客马拉松提供的奖品所诱惑，但我认真鼓励你利用这段时间结识新朋友，并与其他人交流想法和经验——这是你能从这类活动中获得的最有价值的东西。

在 Twitter 上给我留言或留下评论。很想听听你对黑客马拉松的想法！💬

## 图片来源

*   斯图加特黑客马拉松-我自己的
*   小工具- [维基百科](https://de.wikipedia.org/wiki/Datei:Tokyo_Akihabara_gadgets.jpg)
*   Cobot - [Festo](https://www.festo.com/group/en/repo/assets/00393-bioniccobot-1532x900px.jpg) 在他们的[网站](https://www.festo.com/group/en/cms/12746.htm)上找到的