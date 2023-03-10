# PHP 塞尔维亚是一个爆炸

> 原文：<https://dev.to/jsguru_io/php-serbia-was-a-blast-3fn7>

*原文发表于[sasablagojevic.com](https://sasablagojevic.com/php-serbia-was-a-blast)T3】*

我们该谈谈房间里的植物了。*Ba Dum Tss！明白了吗？这是双重文字游戏，房间里的大象和 php +大象== phelephant。好了，我要停下来了...

* * *

我想在 [PHP 塞尔维亚 2019 大会](https://2019.phpsrbija.rs/ "PHP Serbia 2019 conference")后总结一下我的印象，因为这是我第一次参加。我想这可能对那些犹豫是否值得去的人有所帮助。

**TL；博士**

> 确实是。

### **PHP 塞尔维亚 2019 阵容**

阵容和涵盖的主题都非常好。 [Zeev Suraski](https://twitter.com/zeevs "Zeev Suraski") 是今年的主题演讲人，他向我们概述了自他加入被称为 *php internals devs* 的兄弟团队以来的 PHP，更重要的是，所有很酷的东西都将包含在即将到来的 php7.4 和 php8 中。是的，伙计们，php8 有望在 2020 年底问世。哦，时间过得真快，我的意思是我玩这个游戏的时间不长，但我见证了从 php5 到 php8 的惊人演变。

[![](img/2858df5c329208adce094b5276ade7fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ow_GEkXo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sasablagojevic.com/img/content/139/zeev.jpg)

正如我所说的，所有的演讲都很好，但我认为组织者分享视频时，我建议您观看以下内容(排名不分先后):

*   *你好，我的名字是“if”*作者[塞巴斯蒂安·费尔德曼](https://twitter.com/movetodevnull)，他不仅像疯子一样跳舞，让迈克尔·杰克逊感到羞愧，而且他还让我们对 php 中不同类型的 if 语句如何工作有了更深入的了解，是的，你可能认为三元 if 语句只是编写常规 if 语句的一种速记方式，但当它被转换为[操作码](https://x-team.com/blog/learn-about-php-opcodes/ "Zend Opcodes")时，实际上不是这样，使用空合并语句也是如此。

*   *Git legit* 作者[宝琳·沃斯](https://twitter.com/vanamerongen)以一种容易理解的方式解释了 **git rebase** 和 **git merge** 策略之间的区别，即使像我这样的傻瓜也能理解。现在我们终于可以放下这种不和，选择一个与我们哲学上一致的，因为这和 OOP vs FP 是一样的，一个不比另一个好，他们都有他们的权衡。在软件开发中没有银弹，无论我们如何努力寻找它！

*   您不是{framework}开发人员！- 如果你是 Symfony 或 Laravel 的粉丝，你可能会强烈反对这个:D，但 [Thomas Dutrion](https://twitter.com/tdutrion) 就如何从框架中抽象并保护我们自己给出了一个很好的起点。关于如何在*模型*和*控制器*级别进行抽象，Symfony 和 Laravel 框架都有一些很好的例子。我们的领域逻辑不应该知道我们的基础设施(框架)，这是我从这次演讲中得到的关键信息，至少我是这样理解的，现在对我来说非常有意义。对于那些说*但是我们多久改变一次框架*的人来说？我们不一定要改变框架本身，但是我们也需要保护自己免受未来对我们框架的更新。每个主要版本都会有突破性的变化，选择不更新会损害我们代码库的性能和安全性——这就是我们如何得到每个人都讨厌使用的遗留代码库。

*   *使架构清晰*作者[赫本托·格拉卡](https://twitter.com/hgraca)，我们都知道组织代码和文件夹是一件痛苦的事情，如果做得不好，会成为一个巨大的负担。嗯，赫伯托有一些关于用 [DDD](https://medium.com/the-coding-matrix/ddd-101-the-5-minute-tour-7a3037cf53b8 "Domain Driven Development") 方式组织代码的很棒的图表和分析。

我高度怀疑[马塞尔·波西奥特的](https://twitter.com/@marcelpociot)演讲【WebSockets 入门和[达米恩·塞吉的](https://twitter.com/@faguo) *T* *op 10 PHP 编码陷阱*也很棒，但不幸的是，我没有参加。

[![](img/fdea676dcd642d14ee1267b601d4395d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1qZCercY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sasablagojevic.com/img/content/139/IMG_0263.jpg)

*这是我赢得 2020 年 PHPSerbia 的免费门票和:conf 问答比赛的冠军*

### 地点和组织

场地是泽蒙的一个大剧场，有 A 和 B 两条轨道，B 显然是较小的一条。与 A 相比，它要小得多，所以发生的事情是，所有对 B 轨道的演讲感兴趣的人都无法融入，或者选择不站着。不过这种情况只发生过两次。衡量观众的兴趣以及哪个演讲者应该得到短棒是一件棘手的事情，所以正如达米恩·塞吉所建议的那样，当阵容已知时，与会者可以以某种方式投票，他们对哪些演讲感兴趣，这样组织者至少可以有一些估计。

有丰富的食物和饮料。我像植物一样被喂得饱饱的。我必须强调，食物真的很好吃，会议的精酿啤酒也很好喝。与我参加的其他会议相比，我真正喜欢的是(那是萨格勒布网络夏令营的两倍 lol)他们有一台真正的浓缩咖啡机，所以我可以每天喝 37 杯玛奇朵，是的，我是个瘾君子，我不感到羞耻。在整个会议期间，我基本上没有花一分钱，真的没有必要。

总的来说，组织者做得很好，他们非常热情，从第一天开始，你就有宾至如归的感觉。

我唯一不高兴的是，会谈可能会短一点，所以我们在它们之间有一个小的停顿来重置我们的大脑，闲聊，喝咖啡或什么的，或者可能我只是被网络营销宠坏了。

### 定论

我给它一个实值 **9/10** 只是因为我担心如果我给他们 10，他们会变懒。现在是你停止思考，开始准备下一个的时候了！

附:感谢我公司 [JSGuru](https://jsguru.io "JSGuru - Your reliable software development partner") 付的票，我是说谁不喜欢免费的东西！

*原文发表于[sasablagojevic.com](https://sasablagojevic.com/php-serbia-was-a-blast)T3】*