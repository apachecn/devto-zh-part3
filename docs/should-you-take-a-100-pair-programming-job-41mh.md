# 你应该接受 100%结对编程的工作吗？

> 原文：<https://dev.to/daedtechllc/should-you-take-a-100-pair-programming-job-41mh>

结对编程。读者对这个话题的理解可能会有所不同。

你们中的一些人可能会模糊地认为这意味着两个程序员一起工作…或者其他什么。你们中的其他人可能对细节有更扎实的理解，并掌握了包括“驾驶员-导航员”和“专家-新手”等术语的词汇有些人甚至可能理解结对编程作为极限编程(XP)方法的核心部分的全部起源。

## 嘿，看，一对编程读者提问

我很快就会回到最初故事。但是首先，让我们看看我为什么要谈论这个。实际上，我正准备回答读者的一个问题:

> 我被一家做结对编程的公司面试过。我讨厌整天和看着我写代码的人在一起。你怎么想？

所以在这里澄清一下，我们正在谈论一家公司，完全遵守 XP 规则“所有的产品代码都是成对编程的”对于所有的意图和目的，这意味着开发团队通常 100%的时间都是结对编程。

[![](img/9e921b962dce913aaf0b86651014d730.png)](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://daedtech.com/wp-content/uploads/2019/03/people-chained-together-in-front-of-a-computer.jpg)

你应该在这样的公司工作吗？

老实说，我很难对任何一个人说，因为我的建议会非常适合你的个性和偏好。因此，与其立即表示赞成或反对，我想也许我应该更广泛地探索结对编程这个话题。

自从我发表了[开发者霸权](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~daedtech.com/book/)和随后[离开旅行咨询/培训生活](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~daedtech.com/slice-life-money-works/)以来，我已经使这个博客越来越成为一个关于*软件开发者授权*的博客。今天，我想通过这个相对不常见的镜头来看看这个结对编程的主题。

我想检验的不是结对编程是否是一件好事(TM)，而是作为一名软件开发人员，结对编程对你来说是否是一件好事。

## 理解我的配对偏见，比如它们

在此之前，我应该承认过去在这方面的工作和写作。坦率地说，这一切都是非常积极的配对。

*   我为 plural sight 上过一门课，在这门课中，我帮助软件开发人员向持怀疑态度的管理层提出结对编程的商业案例。
*   我已经专业地写了一些如何/解释的帖子，比如关于结对编程的[这篇文章。](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://stackify.com/pair-programming-styles/)
*   这些年来，我已经参与了很多配对活动，从临时的 T2 到正式的 T3。

但是，另一方面，我也是一个典型的内向者。这让我以同样的方式看待结对编程[我做开放式办公室计划](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~daedtech.com/three-martini-open-office-plans/):有趣、累人，而且不是我感觉最有效率的方式。

[![](img/1cef42a058d38b6df4fe353f2e961027.png)](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://daedtech.com/wp-content/uploads/2016/06/Three-Martini-Lunchers-e1511929473984.png)

这让我想到了一个相对困惑的观点:能够证明结对编程对团队/公司的好处可能大于对个人的好处，至少对某些个人来说是这样。这是这篇文章后面要考虑的重要问题。

我这么说是因为我要说的是，尽管这种工作方式很有效，但实际上有很多理由对其持怀疑态度。

## XP 和结对作为流行做法的起源

但首先，让我们回到配对和 [XP 起源的故事](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://en.wikipedia.org/wiki/Extreme_programming#History)。当然，我不在那里，所以我不会提供叙述细节或社论，*本身*。但是*确实看起来像*一些杰出的开发顾问类型的人(他们后来签署了[所谓的敏捷宣言](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://agilemanifesto.org/))聚在一起说，或多或少，强调我的: [![](img/135af3abdd485a0a294c886c144a922e.png)](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://daedtech.com/wp-content/uploads/2014/11/DevSkills.jpg)

> 嘿，如果代码审查和测试优先的方法在**编程**中是好东西，让我们把它们带到* *极限** 看看会发生什么。

事实证明发生的事情是好的。它进行得非常顺利，以至于产生了早期的维基页面、文章、书籍等等。其余的，正如他们所说，都是历史了。

当然，这激发了一个行业的模仿。作为一个工作社会，我们最喜欢的就是试图复制对他人有益的东西。为什么不呢？这是最基本的学习和成长方式之一。

但是也要考虑到 XP *规则*是开发顾问选择加入的——“嘿，如果我们做了 X，让我们试试看会发生什么。”但是 20 年后，当你为是否要去这家公司工作而争论时，这些选择加入的规则对你这个普通的开发人员来说将是**强制性的**。

## 作为一名开发者，反对 100%结对编程的理由

背景安全就位后，让我们进入正题。我将解释一下为什么你会想放弃接受这样的提议。

请注意，这只适用于对 100%配对持怀疑态度的人。我说的不是开发者有时会结对的公司，坦率地说，90%的公司都有不止一个开发者。如果你真的喜欢结对，无论如何，接受这份工作。这种方法有很多好处，你喜欢它，那就尽情享受吧。

我在这里提出的案例与实践的效率是正交的，并且理解我不是在权衡团队是否应该采用 100%配对策略。

### 1。对实体存在的回归式强调

让我们从容易找到的东西开始。20 世纪 90 年代走向“敏捷”的许多实践和哲学都是以肉为中心的，这对于传真机和 Citrix 或其他什么的时代是有意义的。你坐在开放式办公室的牛棚里，在贴在墙上的实体便条卡上写东西，每天站着说话，整天坐在某人旁边。

[![](img/fe8ccdcc30de16ef4f88d0b3ffa375d6.png)](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://daedtech.com/wp-content/uploads/2014/11/QuillStandards.jpg)1999 年，确定。但是 2019 年……真的吗？

如果你去参加任何企业敏捷转型，你会看到对变化的重视。在某些情况下，这是向现代的转变:抛弃着装规范，安装詹金斯，做 DevOps，等等。但是，在某些情况下，它走回头路:摆脱项目管理工具，使用干擦板，然后[让所有人进入同一个房间](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://www.nbcnews.com/business/business-news/why-are-big-companies-calling-their-remote-workers-back-office-n787101)。

因此，当谈到开发方法时，我们看到“倒退即进步”。现在，我是一个完全远程办公的员工，也是一个完全远程办公的企业主，多年来一直如此。所以我显然是有偏见的，我不会在这个会场上为远程而非物理的存在游说。(我只想说，很难想象曾经回去过。)

但是我*将*指出，100%配对的团队/公司极有可能在座位上有严格的政策。如果你 100%的时间都在配对，很难证明用一个不可靠的远程协作工具来冒险是合理的。而且也很难证明任何形式的弹性工作时间是合理的。

所以你可能正在寻找一份工作，你需要从 8:30 到 5:00 坐在你的椅子上，准备好社交互动，否则你会让你的团队失望。

### 2。100%配对授权是事实上的微观管理

稍微转换一下话题，让我们谈谈代理(或缺乏代理)。我们所有的开发人员 100%的时间都是一个正式的微观管理政策。

这些年来，在我的指导/咨询之旅中，我一直在处理经理和开发人员之间关于单元测试的争论。我听说管理层不让开发人员编写自动化测试。我听说管理层希望看到单元测试覆盖率。

我建议管理层[停止](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://blog.ndepend.com/code-coverage-not-management-concern/)。他们不应该干涉开发人员的单元测试，就像他们不应该干涉他们编译代码或检查堆栈溢出的频率一样。*开发人员如何工作应该由那些开发人员决定。*

那么，配对与否的决定有什么不同呢？

[![](img/1deb4cd55471f93651d977914bbdba49.png)](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://daedtech.com/wp-content/uploads/2014/11/Clipboard.jpg)

这里有一个特别讽刺的地方，敏捷方法吹捧扁平结构和自组织团队。当然，当肯特·贝克等人。艾尔。创造这种方法是因为*他们在制定这些规则的时候是自我组织的*。但是，当你加入一个“官方政策强制配对”的公司时，你就不是自组织的了。

所以要明白，你正在加入一家对你如何开发软件进行微观管理的公司。

### 3。你正在加入一家外向型是“正确”性格类型的公司

让我们把事情解决掉。不管我看了多少关于“内向者的配对”或其他什么的演讲，对内向者来说，配对绝对不是*而不是*。老实说，这些谈话会“稍微减轻你不可避免的疲惫和疲惫。”

但是你看不到这种诚实。你没有的原因是因为苏珊·凯恩在她的关于内向的优秀著作中定义的外向理想。随着推销员角色的兴起，外向成了“正确的”性格。

想想学校长大。如果你不喜欢小组工作的想法，或者如果你想在休息时读一本书，老师觉得有必要“纠正”你的行为，这样你就不会被贴上“反社会”的标签。

[![](img/8d2d3f390fd6b8c1d93cf807c68e499f.png)](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://daedtech.com/wp-content/uploads/2014/01/Interrupted.jpg)

这种情况会延续到工作场所。如果公开演讲、团队工作、闲聊和闲聊让你感到不舒服，这个世界告诉你，你需要调整自己以获得成功。"这里有 5 个有用的建议，可以治愈你喜欢独处的冲动."

并不是所有的公司在拥抱外向理想方面都是一样的。你可以放心地打赌，一家要求 100%配对的公司在试图修复你破碎的内向性格方面超越了竞争对手。

### 4。该公司正在玩不输游戏

在下一节中，我将更多地讨论结对给公司带来的好处，但是这种好处的很大一部分涉及到保持开发人员的专注和相对无错。另一方面，你没有由死胡同和“非生产性”题外话所提供的独特的个人创造力和灵感的爆发。

以谷歌的 20%时间(T1)为例，这是一个标志性的例子(尽管显然存在争议)。基本前提是“我们对员工有足够的信心，我们会每周给他们一天时间，让他们做他们想做的事情，无论他们想做什么，并相信他们的努力会以某种方式获得巨大的回报。”

那是为了赢而比赛。这就相当于一个足球队在第四节领先 14 分，然后向前推进。另一方面，100%的强制配对是跪在球上，然后在那个比喻中撑船——玩而不输。

[![](img/51a6a9ac7abf840f23ce31e75d607fec.png)](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://daedtech.com/wp-content/uploads/2013/07/FootballGame.jpg)

像这样的政策表明，公司主要对开发人员的可预测速度感兴趣，而不是出色的想法或改变游戏规则的开发。这一点也没有错——这是完全合理的。可预测的、错误最小化的工作的预期价值将会高于死胡同的预期价值，并希望改变游戏规则。

我提到这一点只是为了让你明白你可能会让自己陷入什么样的境地:一家希望你一天 8 小时不引人注目地埋头苦干的公司。

### 5。从属状态信号

我将用一个论点来结束我的演讲，这个论点很难用几百个词来概括。我在《开发者霸权》中详细阐述了这一点，但我将在这里记下它。

与那些可以自由工作的同行相比，强制结对的开发人员给人一种低级/从属的感觉。为了理解我的意思，就业务而言，考虑结对的三个不可否认的好处。

1.  集体代码所有权在团队中传播知识，[降低了“总线因子”](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://en.wikipedia.org/wiki/Bus_factor)
2.  配对意味着持续的代码审查，这意味着更少的遗漏缺陷和设计问题。
3.  结对情况下的开发人员不会面临太多的干扰和中断。

都是好东西，对吧？但现在，让我们从一个冷静的、企业估值的角度来看待它。首席财务官正看着你。

1.  配对使得单个开发人员的资源更具可替代性。
2.  它有助于克服软件开发人员固有的草率。
3.  它阻止了开发人员使用他们天生的称王倾向。

而且，一旦首席财务官在开放式办公室的牛棚里漫步完毕，你认为她会回到自己的桌子前，和她的首席财务官搭档吗？

当然不是——这不是领导者会做的事情。她回到她的办公室和*独自工作*。事实上，这甚至不是 scrum 大师、项目经理和分析师做的事情。这是只有开发人员才会做的事情，因为只有开发人员才需要会计毕丽伙伴。

[![](img/ec856c634809dd61569ae06ae94cd01c.png)](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://daedtech.com/wp-content/uploads/2015/06/RobotProgramming.jpg)

当然，没有人会说这种话，甚至可能没有人有意识地这么想。这种微妙的氛围将会渗透到整个组织中。这将使领导层不太倾向于听取开发人员的意见，并使技术职位对任何对横向转换或进入领导层感兴趣的人来说“更棘手”。至少它会，直到你能找到一种方法把自己从发出从属信号中抽离出来。

## 作为一名开发者，100%结对编程对你来说是可取的

这一部分将会滑稽地简短而甜蜜。对我来说，反对 100%配对需要大量的解释。证明这一点很容易。

问问你自己:

1.  你喜欢结对编程和一直这样做的想法吗？

如果答案是肯定的，那就去做吧。高度协作的开发团队是有效的，这种方法对公司有好处。如果这对你也有好处，那么你就是在签一个“大家都赢”的合同。

强制配对对你的个人职业生涯有一些微妙的负面影响(如果你是一个内向的人，有些就没那么微妙了)。但对你来说也有一些巨大的好处，包括:

1.  如果你是新来的，和更有经验的人一起工作，真的，真的学得很快。
2.  尽早体验，并经常作为导师/教练，可能会带来很好的咨询职业机会。
3.  办公室政治和引导他人个性的速成班。
4.  顺利的时候，和你的团队有一种难以复制的同志情谊。
5.  结交朋友的可能性更高，这将持续你的职业生涯。

当然，你的意识形态和你的公司及其利益是一致的。我想不出在任何情况下这是一件坏事(前提是你不要变得理想主义而忽视自己的利益)。

## 那么，到底该不该接受这份工作呢？

通常，任何权衡利弊的文章都会在结论中用“所以，如你所见，这要看情况，由你决定。”我不会在这里这么做，因为这有什么意思呢？

鉴于这个读者问题的框架，我建议*这位读者***你可能不应该接受这份工作**。

[![](img/681381fd9adba5d522cb2d16f5cbd4a2.png)](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://daedtech.com/wp-content/uploads/2017/07/Expensive-Hobo-Gear-e1509315266880.jpg)

我是一个内向的人，我不太喜欢团队工作。但我确实发现，每次我接了一份比以前更需要协作的工作，我都会比自己想象的更享受这种协作的局面。这不仅不是一场噩梦，而且一个更开放的办公室计划或更多的工作配对实际上证明是令人愉快的。

但事实也往往证明这是不可持续的。我一整天都在协作，然后在所有人都离开后加班，因为所有的协作都不像是真正的工作。最终，我会厌倦，不是因为长时间的工作，而是因为持续的合作。我会继续前进，或者转向一个不同的项目或角色。

我的观点是。你可能会比你想象的更喜欢这种配对。但鉴于你的直觉，它也可能会穿在你身上。

有很多公司有很多不同的哲学。我会找到一个更接近你自己的。

你应该接受一份 100%结对编程的工作吗？最早出现在 [DaedTech](http://feeds.feedblitz.com/~/t/0/0/daedtech/www/~https://daedtech.com) 上。

[![](img/03a390cc8942a284e11731d2ae17ab87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NmBb0EB8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://feeds.feedblitz.com/%257E/i/599819814/0/daedtech/www)
[![](img/24ec12dc49a4f166881c93dd32c0626c.png)](https://feeds.feedblitz.com/_/19/599819814/daedtech/www)[![](img/ce9a1f832dec4c2ede5f61841b9a69bf.png)](https://feeds.feedblitz.com/_/20/599819814/daedtech/www)