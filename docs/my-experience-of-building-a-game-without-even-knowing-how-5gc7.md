# 我甚至不知道如何构建一个游戏的经历

> 原文：<https://dev.to/patiencedaur/my-experience-of-building-a-game-without-even-knowing-how-5gc7>

上周，俄罗斯非政府组织 Komanda 29 在圣彼得堡举办了一场为期 3 天的黑客马拉松，旨在制作更多面向社会的所谓“严肃”游戏，帮助解决公民问题。他们邀请了公民活动家和非政府组织雇员、艺术家、说书人，当然还有美国编码员。

我响应了参与者转发到我们的*皮拉迪斯*电报聊天的号召。创作游戏不是我计划要做的事情。我目前正在转行，对 Python 有一定的了解，最近还参加了 DevOps 课程。但后来发现项目是我青梅竹马办的，我就毫不犹豫的去了。:)

我绞尽脑汁想了想如何用 Python 制作一个游戏(因为这是我最了解的)，看了几个`pygame`模块的视频，然后带着更大的疑惑去了活动现场。

[![Photo by Komanda 29.](img/668b61bf49a8b068d741f9ea435e8d0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j8C3PtUr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9f8rlvmm2yggqqnof47v.jpg)

我不会把这次活动描述成一次黑客马拉松，而是一次研讨会。两位专业的游戏设计师被邀请来做一个关于如何从零开始创造一个游戏的演讲，无论是视频游戏、桌面游戏还是 LARP 游戏。他们在这个过程中指导我们，监督我们原型的创建。还有免费披萨:)

有四个积极分子，每个人都提出了他们的游戏想法。其中一个想法是处理听力损失的增加，它最终转化为一个以邪恶耳朵为主要角色的战术 RPG 的原型。另一个项目是像素艺术城市行动主义模拟器，你试图从住房开发中拯救一个公园-其中一个结局是致命的。还有一个向活动家传授新技术的游戏。它涉及到以*统治*的方式做决策，并且有非常有趣的文本。

我参加了一个预防家庭暴力的项目，是由一位心理治疗师提议的。我们决定让这个游戏基于她在工作中开发和使用的聊天机器人中的问题。一位艺术家加入了我们，他的风格让我想起了苏联的海报。我们必须在三天内交付原型，所以我们开始工作！

[![Photo by Komanda 29.](img/439ff8a87ef56ae2a264a002968e02bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RA6FMCBe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ndfvnnpxbqtdvs09qgeb.jpg)

我们决定我们的游戏将是一部视觉小说。目的是帮助玩家将言语和情感虐待与即将到来的身体虐待联系起来。因为我们时间不多，我们的原型只展示了一段异性关系，你扮演女性角色。

首先，我们重新创造了三个关系场景，在这些场景中，角色可能明白她接下来将面临身体暴力。在这里，她可以选择回答选项(就像在你的常规巫师 3 对话中一样)，并看到她的行为的后果。我们还决定添加按钮“帮助”和“退出”，但功能不同寻常。按下“帮助”会触发主角最好的朋友的呼叫，“退出”意味着“退出关系”不是每个玩家都会猜到的！

第二，我们必须画出对话的框图，以免在所有选项中混淆。这是加快我们工作并帮助我们取得胜利的一个重大步骤。我们的游戏有三个结局。

[![Beware of the mighty diagram!](img/4f0ddc71eaedfb5bee119a6cf3049434.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aazt-BJa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8o3sq7916a93ro7eijlr.jpg)

最后，到最相关的部分——编码！事实上，我们的目标受众要求我们制作一个电话应用程序，因为家庭暴力受害者的电脑通常由虐待他们的伴侣监管(或者如果他们有密码就会被破坏)。但是我们只需要向陪审团展示一个可玩的原型。所以我决定用[麻线](https://twinery.org)。

Twine 便于创建思维导图。它似乎是 Javascript 的包装器。Twine 使用的一种降价语言叫做 Harlowe。Harlowe 还允许添加 HTML、CSS 和基本逻辑(我想我在这里发现了一些错误)。Twine 也可以和另外两种 markdown 语言一起使用，但是它们非常依赖 Javascript，而我对 Javascript 一无所知。

Twine/Harlowe 对于大型项目来说不是很方便。但是，再说一次，我从来没有真正对前端 web 编程感兴趣，我早年只知道一些关于 HTML 和 CSS 的技巧。因为我们必须尽快交货，所以我们有点被绳子缠住了。我曾考虑过使用一些非常好的视觉小说游戏所使用的`RenPy`，也看过库`pyjsdl`。然而，在与导师讨论并对我们的时间和我的知识进行清醒的估计后，我决定我们用 Twine 拼凑一个简单的原型。

最后，我们展示了我们的项目。陪审团播放了我们的原型并得出了他们的结论。我很高兴地说，我们的游戏赢得了组织者的资助！在接下来的半年里，我们将不得不改进一切，这样游戏就可以在手机上玩，并真正帮助人们。我们也将详述这个故事。我必须拓展我的前端技能。我认为这是学习新东西的一个很大的激励，我非常感谢有这个机会和善良体贴的人在一起！

这里有一张有趣的图片，说明某些东西是如何不起作用的。我想我们看到了一个错误:

[![oops](img/73fcd5c7a551922c84afd328eacfd437.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SdBDJjcY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/habuvwm27iyth5pseopa.jpg)