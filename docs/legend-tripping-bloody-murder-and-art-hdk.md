# 传奇-绊倒，血腥谋杀和艺术

> 原文：<https://dev.to/gaffen/legend-tripping-bloody-murder-and-art-hdk>

## 内容警告:一些令人不安的图像

神秘感天生具有诱惑力。尤其是当它与谋杀、恐怖主义和阴谋有关的时候。我不久前在一个名为“平等者”的新闻网站上发表了这篇文章。作为一个与发展相关的人，我想和你们分享我是如何卷入这样一个谜团的故事。作为我的第一篇文章，我希望它不要太偏离普通开发者社区的常规。

要做到这一点，你需要熟悉两个术语。

* * *

### 密码术:

试图使消息对除预定接收者之外的任何人都不可读的艺术

### 隐写术:

隐藏信息的艺术，不让不知道去哪里找的人知道。

* * *

那就不碍事了，让我们从头说起。

## 2015 年 10 月 12 日。

约翰-埃里克·克拉比克勒(John-Erik Krahbichler)在一个相对不知名的科技博客“Gadgetzz.com”上发表了一篇文章，标题为[“这个令人毛骨悚然的谜题在我们的邮件中出现了”](http://gadgetzz.com/2015/10/12/this-creepy-puzzle-arrived-in-our-mail/)。该拼图由一张 DVD 组成，上面只标有随机的字母和数字序列。视频内容如下:

由于视频的奇怪性质，这篇文章很快获得了其他科技博客和互联网用户的关注。不久之后，有人想出了通过摄谱仪分析仪来分析视频中的音频——这是一种将声音视为图像而非波形的方法。由此产生的图像包含短语“你已经死了”，“我们是反病毒”，以及许多受折磨或死亡妇女的尸体照片。一个令人不安的，血腥的拼贴在粒状单调出血一个恐怖到下一个。一张嘴似乎在喘气，被困在塑料布下面。

[![](img/28df483f7d92290df0264126bde2ee18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NV_rSIOO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://api.gaffen.co.uk/wp-content/uploads/2019/02/11B-X-1371-Spectrogram-Both-channels-v4-1024x335.jpg)

这是我进入被称为 11B-X-1371 的现象。

如果你的假设和我的一样，在这一点上，它看起来可能是一个杀人犯的作品——对 YouTube 一代来说，是某种黄道带黑仔。这正是我和其他人对它的理解。

<figure>[![](img/a417a319ff09c73e28e869ad0954b00b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aeDdVa32--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://api.gaffen.co.uk/wp-content/uploads/2019/02/11B-X-1371-Pigpen-Frame-1024x576.jpg) 

<figcaption>来自 11B-X-1371 的视频帧显示了第一个猪圈符号。</figcaption>

</figure>

Reddit 上的一个社区已经在忙于解决视频中发现的各种隐藏信息。我被一种想以某种方式提供帮助的紧迫感所征服——看到摄谱仪中的所有尸体，我浏览了视频，寻找任何我可以贡献的东西，这时我注意到在 [1:26](https://youtu.be/quyXS4a0JGQ?t=1m26s) 处有一个奇怪的方形表盘。在爱好者看来，这是一种猪圈密码。

为了让你们真正了解破译一条加密信息是什么感觉，我将不得不使用一点技术，但是请耐心听我说。

## 简短的密码学课

pigpen 密码是单表替换密码的一种形式，这意味着编码消息(密文)的每个字母总是映射到原始消息(明文)中的同一个字母。例如，用“U”替换“T”的所有实例，用“A”替换“F”的所有实例。这不同于多字母密码，多字母密码对同一个字母进行多次替换，使得密码更难破解。在猪圈的情况下，替代，而不是一个字母的字母表，一个几何形状被用来代替。

也被称为共济会或井字游戏密码，它的名字来自于用来创建字母和符号(或密钥)之间的关联的网格:

<figure>[![](img/8bc3c416b16c3c543aacd943b7fb51ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R9GMcsGw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://api.gaffen.co.uk/wp-content/uploads/2019/02/TradPigpenExample-1024x442.jpg) 

<figcaption>图解传统猪圈密码</figcaption>

</figure>

如果把表盘上所有可能的形状都画出来，就可以制作出与猪圈钥匙完全不同的东西:

<figure>[![](img/041a3b3c9849f4b4ff72c97f4e9e94df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--loUaath2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://api.gaffen.co.uk/wp-content/uploads/2019/02/BlankPigpenKey-1-1024x452.jpg) 

<figcaption>所有可能的符号基于视频中显示的图案，以上是按顺序显示在表盘上的符号。一些符号显示的时间是其他符号的两倍，因此是重复的。</figcaption>

</figure>

虽然生成的网格与猪圈密码有明显的相似之处，但这一个最大的问题是它包含 24 个字母的空间，而在英语中却有 26 个。

## 传说跳闸

此时，其他信息已经被解密，全部是英文的。一条用莫尔斯电码写的信息是“杀死总统”，另一条包含了白宫的 GPS 坐标。结合短语*“我们是反病毒者】*，社区的一些成员很容易被引上阴谋的道路。一篇网上文章甚至断言，该视频暗示了 2024 年前美国和法国有[、【基督教生物恐怖主义】的威胁。](http://beforeitsnews.com/conspiracy-theories/2016/06/christian-bio-terrorism-threatens-usafrance-before-2024-11bx-1371-2475986.html)

我自己也成了 reddit 主题的定期撰稿人，也是 [##11b-X-1371 IRC 频道](https://kiwiirc.com/client/irc.freenode.net/##11BX1371)(一种黑客的即时通讯服务)的常客。我们培育的社区正处于成为一种[传奇旅行](https://en.wikipedia.org/wiki/Legend_tripping)的数字空间的危险之中；青少年通常从事的一种实践，本质上是鼓励迷信。在伦敦的一个极端案例中，青少年在海格特墓地讲述的吸血鬼故事被媒体报道，这让年轻人陷入疯狂，并在 1970 年的[大规模吸血鬼狩猎中达到高潮，扰乱尸体并亵渎坟墓。](https://en.wikipedia.org/wiki/Highgate_Vampire#The_Mass_Vampire_Hunt_of_March_1970)

该视频的一个版本也被发现上传到一个名为“[帕克·赖特](https://www.youtube.com/channel/UCS94HPu1qU1kOv8-BN9RlSA)”的频道。[一个匹配的 Twitter 个人资料](https://twitter.com/parkerwwright)也被打开，声称是视频的创作者帕克·华纳·赖特。他发布了一个类似瘟疫医生的棕色皮革面具的图片，看起来和视频中的一模一样。这导致模仿者登录，试图声称视频作者的荣耀时刻，哗众取宠的机会。

很快就变得很明显，我们需要尽最大努力来平息我们社区中的非理性猜测，因为人们涌入该频道提出神秘和偏执的建议——将视频与无数充满厄运的想法联系起来。

## 闯入猪圈

<figure>[![](img/544be4df94ab8c39df2a80cce60f0fe2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_rWipoPQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://api.gaffen.co.uk/wp-content/uploads/2019/02/HKaZCdsr-1024x576.png) 

<figcaption>从鸟人的肩膀上可以看到一个“|/”形状的密码，左上角有一个“A”。这被认为是一个暗示。</figcaption>

</figure>

一个形状喷在隐身人后面的墙上，我们称他为“鸟人”，它预示着一个潜在解决方案的关键。利用这些信息，我开始工作。

<figure>[![](img/f00842bfdfe514f69660b7a13c20e8df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fR_b3hiH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://api.gaffen.co.uk/wp-content/uploads/2019/02/SsdCgHr-1024x1024.jpg)

<figcaption></figcaption>

</figure>

没有地方放两个字母，我在这里碰壁了。对于为什么会有字符缺失，人们提出了许多建议，比如都铎王朝或波兰的字母表中排除了某些字母。事实上，波兰语可能有一定的意义，因为视频的地点被确定为波兰的犹太人医院，该医院在二战期间纳粹占领该地区时悲惨地结束了。

然而，尽管我尽了最大努力；我只是无法说服自己符号到字母的逻辑映射会产生任何有意义的东西。加上我作为 web 开发人员的日常工作的工作量，我开始放弃，让社区接手我留下的工作。

要是我坚持下来就好了；答案就在我眼前。

[![](img/305d3ab1a2c001483687b267377376b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--05wHMYku--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://api.gaffen.co.uk/wp-content/uploads/2019/02/PigpenWeb-01-1024x536.jpg)

罗马字母表有 23 个字符，缺少现代字母表中的“J”、“U”和“W”。使用上面的网格会产生以下消息:

**来对抗男性**

考虑到在古典拉丁语中，符号“V”也用作“U ”,因此:

**【广告质疑人】**

*【以男人为目标】。*

这只是许多隐藏信息中的一个，所有这些信息都需要团队的努力来解密。

## 一个谜

[![](img/37cbbe5ea0ba9307f787e224ccd916ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O0SW8EFm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://api.gaffen.co.uk/wp-content/uploads/2019/02/6upRsk0-1024x611.gif)

到目前为止，大部分隐藏的信息都已经被破解了，但是有一个被证明是特别棘手的。[一份排列成四个字符列的密文](https://youtu.be/quyXS4a0JGQ?t=1m59s)被确认为来自臭名昭著的恩尼格玛机器的输出——这台机器保护纳粹的信息，直到波兰和英国的联合破译行动开始。这在[布莱奇利公园](https://en.wikipedia.org/wiki/Bletchley_Park)的 Ultra 项目中达到高潮，该项目被认为将二战缩短了两到四年。

一个“G”进一步证明了这一点，这个“G”起初看起来是画在鸟人身后墙上的一只眼睛的瞳孔，但实际上是对英格玛标志的引用。

<figure>[![](img/7ba13c6b48c9edb73c2534e86878b095.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vpltX3gH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://api.gaffen.co.uk/wp-content/uploads/2019/02/logocomparison-1024x355.jpg) 

<figcaption>喷绘到英格玛 logo</figcaption>

</figure>

的对比

我们开始猜测机器的设置，使用视频本身的提示。但是无论我们怎么尝试，包括使用自动化软件的强力猜测，我们都无法产生任何可读的东西。这个过程持续了几个星期，但我们仍然没有运气。

## 寻找 11B-X-1371 的创造者

到这个时候，我开始觉得我们只是在踩水。没有一个隐藏的信息似乎导致一个明显的下一步；除了隐约带有威胁性的反体制语气，没有任何信息传达出任何进一步的意图。我们没有发现下一个受害者的任何迹象，或者与某个二级谜题的联系。如果说我不清楚这个视频到底想表达什么，这是一种保守的说法。

但是我们所有人都将被扔进一个循环。

原来，摄谱仪中所有隐藏的图像都来自电影和戈尔的艺术作品，除了一张玛丽·沙利文尸体的历史性谋杀照片，[据称是波士顿扼杀犯](https://en.wikipedia.org/wiki/Boston_Strangler#Victims)的作品和[证明他是阿尔伯特·迪沙佛](https://en.wikipedia.org/wiki/A_Rose_for_Mary)。

这个视频会是某种虚拟现实游戏吗？这些多层次的互动故事通常被用来推销[电影](http://cloverfield.wikia.com/wiki/Cloverfield_Alternate_Reality_game)、[电视节目](http://lostpedia.wikia.com/wiki/The_Lost_Experience)、[游戏](http://uk.ign.com/wikis/destiny/Owl_Sector)，甚至令人费解的是，一个[滑雪场](http://metatalk.metafilter.com/21759/What-Is-This-Creepy-Site-Advertising-A-book-perhaps)。

[Reddit 社区的一名成员](https://www.reddit.com/r/creepy/comments/3ongx1/this_creepy_puzzle_arrived_in_our_mail/cw51dzi/?context=3)表示，该视频的发布与丹·布朗的*地狱*的屏幕改编同步，该电影原定于 2015 年 12 月 18 日上映，可能会对其进行病毒式营销。

> 一个名叫 Bertrand Zobrist 的男子制作了一个令人毛骨悚然的视频，他完全穿着传统的瘟疫医生面具和服装。他说他就像死亡，是治愈的良药
> 
> <cite>*——careersxmichael，Reddit*</cite>

虽然这是一个很有前途的理论，但大型营销公司似乎不太可能使用这种极端的图像进行宣传，这很容易对任何营销工作产生负面影响。

一个更可信的想法是——考虑到视频的发现者经营着一个相当不为人知的科技博客——张诗钟·克拉比奇勒可能自己制作了视频。一家营销机构可能会拒绝使用这种令人不安的图像，但一个希望获得关注的人可能会这样做。

张诗钟本人在某个时候登录了该频道，坚称自己没有以任何方式参与视频的制作。由于无法进一步证实或反驳这个想法，我们将注意力放在加密讯息上。

最终，一个名为“ParkerWWright”的用户登录了 IRC 频道。

## 帕克·华纳莱特

ParkerWWright 并不是 IRC 上第一个声称自己是鸟人的人，但他肯定比任何人都更努力地证明自己的身份。他发布了一个定制的皮革手套的照片，上面嵌有视频中那样的闪光灯，此外还有一些简单的 arduino 代码，用于产生它创建的莫尔斯码。虽然没有确凿的证据，但一个坚定的模仿者可以相当容易地做出类似的东西，这让我们停下来思考。他还在 Twitter 上发布了一张棕色皮革面具的照片，看起来与视频中的面具一模一样，但我们还没有准备好接受它作为他是视频创作者的不可否认的证据。

我们陷入了僵局，如果要取得任何进展，社区需要视频的创作者采取行动。

一天早上，我登录 IRC，发现一条新消息被解密了:

> *“自满的是弱者，与我们并肩战斗，打倒黑兽，杀死他的疾病，否则同归于尽”*

ParkerWWright 将关键的 Enigma 设置交给了社区的一名成员，揭示了最终的信息，并证明了他声称是视频创作者的真实性。

我们找到了鸟人。他自称是艺术家。他为自己建立了一个脸书页面和一个推特账户。过了一会儿，甚至一个[网站](https://pwwright.com/)也紧随其后。

没有人有生命危险。我为这个项目贡献的时间的意义开始蒸发，因为我试图以新的眼光来看待这个视频的意义。我失去了兴趣。我回到了我的日常工作中。

## 把 11B 视频当成艺术

那是一年前的事了，第二个视频——[11 b-3-1369](https://www.youtube.com/watch?v=SWB5npxJ1gY)——已经发布了。帕克·华纳·赖特(当然是化名)在 YouTube 上接受了一次[采访。我甚至把他加为脸书的“好友”。](https://www.youtube.com/watch?v=iR_0XQoJeDk)

赖特似乎视自己为某种“人类的老师”；他在采访中提到了这个词。他承认他的作品有其重要性，迫使观者透过表面去看。他热衷于强调不要从表面上接受流行的叙事，并放弃“故意的无知”。在他的第二段视频中，一个隐藏的信息相当直接地暗示:

> *“他们让一切如此可信，所以你像妓女一样吮吸它”*

到目前为止，我看过的所有作品都有一个重复的主题；复杂的制作技术，错综复杂的多层谜题，都是为了隐藏缺乏一定深度的信息。腐败、战争、贫困、不平等、说谎的政客——这些都不是新概念，我们也不是经历痛苦的第一代人。“醒醒！”莱特似乎在尖叫，但为了什么？

> 一个新的秩序正在兴起。你要么加入，要么倒下。病毒已经扩散得太远了；必须制止这种行为。我们将在它的根部筑堤。13 和 50，会燃烧。”

军国主义的语言，革命的模糊含义……这些词听起来有些耳熟:

> *“奋起夺回权力，是时候了
> 
> 那些肥猫心脏病发作，你知道
> 
> 他们的时代即将结束
> 
> 我们必须团结起来，看着我们的旗帜升起”*
> 
> <cite>*——缪斯乐队*起义</cite>

至少从表面上看，从 11B-X-1371 现象中提取出的东西实际上没有什么意义，这是一种真正的危险。在某种程度上，这件作品的艺术价值似乎非常肤浅。虚张声势和场面掩盖了很少的意义。如果挤在互联网黑暗角落的一群乌合之众黑客和阴谋论者是目标受众，他们几乎肯定已经“觉醒”了，这让赖特成为皈依者的自封传教士。

很长一段时间，我认为这就是一切。一堵砖墙，浪费几个小时的时间去帮助别人凝视自己的肚脐。最初是一种内在动力，想要为一些可能拯救生命并可能抓住连环杀手的事情做出贡献，但这种动力却被简化成了某人的行为艺术。我被骗了，看到有人可能会浪费对一大群被俘虏的观众说一些真正有意义的话的机会，我很恼火。

当然，一件艺术品不仅仅是一大堆文字。仅仅因为一件艺术品包含文字并不一定意味着这些文字包含并概括了它的意义。我觉得特别有意思的是。这篇文章是针对谁的？谁是预期的观察者？我之前暗示过我自己可能是目标受众，但是我想得越多，我就越不确定事实是否如此。

谎言的传播是整个故事中反复出现的主题。当视频第一次被披露时，我是众多支持凶手逍遥法外这一理论的人之一。即使是现在，你在网上阅读的几乎每一篇文章都会忽略文章中的任何意义，甚至忽略它是艺术的想法，而倾向于一个吸引人的标题和廉价恐吓的承诺。像*、【神秘】、*、*、*这样的词经常出现。甚至我在这篇文章的开头也详细讨论了尸体，因为这是一个陷阱。我认为这对帕克·赖特来说是一个聪明的举动，我怀疑如果没有它，他就不会为自己赢得观众。

当谈到艺术时，“意义”或“信息”的概念并不那么简单。一些艺术家并不声称给他们的作品赋予任何意义，或者说他们让观众赋予他们的作品以意义。

一件作品在公共空间的存在，以及艺术家把它放在那里的决定。

这两个因素是故意的。你做给别人看的东西是有意图的，因此可以说它传达了一个信息。

尽管我个人倾向于根据艺术传达某种东西的能力来评判艺术，但这是否是艺术家的职责仍有待商榷。尽管我之前嘲笑过“深入观察”是一种没有意义的、肤浅的想法，但这可能是有道理的。我认为，事实上，文章、回应视频、模仿者、阴谋论者的生态系统相当准确地说明了这一点。

我认为将帕克的艺术评价为“仅仅是他的视频”是忽视了 11B 已经变得大于其各部分之和的事实。这使得这篇文章成为他作品的一部分——如果你已经读到这里，那么也许你也已经成为他作品的一部分。

> “世界是一件艺术作品，它自己诞生”
> 
> <cite>*——尼采*</cite>

* * *

*特别感谢奇点、Cyphere 以及* [*##11BX1371 IRC 频道*](https://kiwiirc.com/client/irc.freenode.net/##11BX1371) *的所有人，感谢他们与我一起参与这一旅程，并为本文的研究提供帮助。没有你我不可能做到。*

帕克·华纳·赖特。保留所有权利。

恩尼格玛机器的照片