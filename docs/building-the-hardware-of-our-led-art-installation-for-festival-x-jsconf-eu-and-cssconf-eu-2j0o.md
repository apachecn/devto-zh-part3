# 为第十届艺术节搭建 LED 艺术装置的硬件。欧盟和 CSSConf 欧盟)

> 原文：<https://dev.to/azure/building-the-hardware-of-our-led-art-installation-for-festival-x-jsconf-eu-and-cssconf-eu-2j0o>

# 有点背景

我是德国[云倡导/DevRel @ Microsoft](https://developer.microsoft.com/advocates/index.html?WT.mc_id=devto-blog-jansche) 的项目经理。我完全爱上了技术社区，并一直在寻找我们可以支持的社区组织的会议。难怪我的道路不止一次穿过 JavaScript 社区，我一直想让微软成为 [JSConf 的相关部分。欧盟](http://jsconf.eu)，在我看来这是最重要的 JavaScript 会议，也是我见过的最具包容性的活动之一。我知道微软过去赞助过这个会议。但是，赞助有很多种不同的方式。我想要一个能打动人的。一个感觉真实的。它赋予了会议和与会者价值，而不是一个品牌。所以我们(云倡议)很早就开始向 CfP 提交，并与 JSConf 的发起团队取得了联系。欧盟和内部连接，将合适的人聚集在同一个(虚拟)桌子上。不知何故，它似乎工作了。

# 火花

在 1 月初微软参加柏林会议的策划电话中，我的同事布赖恩·休斯( [@nebrius](http://twitter.com/nebrius) ，微软开发者关系的云倡导者)提出了为展台制作一件数字艺术品的想法。一些带有 API 的东西，人们可以对着它编程，设计和展示他们自己的动画。我爱上了这个想法，不仅因为这意味着我们不会有一个没有人会关心的可悲的营销展示。相反，我们想要一个定制的技术作品，与开发人员的游戏性和发现精神相匹配。Bryan 谈到了木板上的 led，这是一个大约有平板电视大小的东西，以及他如何提供这两者，实际的硬件以及运行这个东西的逻辑。作为一名项目经理，我的工作主要是组织、制定策略、计划和执行。我仍然是一个动手型的人，有技术背景。这解释了为什么我在不知道任何细节的情况下，提议 Bryan 在欧洲复制他的 LED 装置，这样当他的东西在去柏林的途中坏了，在海关被卡住或任何其他不太可能发生但根据墨菲定律仍然会发生的不幸时，我们就有了备份。我记得他有点犹豫。我们还没有见过面，以前也没有合作过，所以当一个陌生人让我克隆我的作品时，我可能会有同样的反应。但是布莱恩给了我一个预付款(谢谢，布莱恩！)事实证明，这是一次非常富有成效的合作，双方都有很多相互学习的机会。

当我在 3 月份收到 Bryan 的设计文档时，我非常惊讶地发现没有找到屏幕的基本形状和大小，也没有安装到墙上。我准备在木板上钻一千个洞，然后从背面把 led 粘在上面。但事实证明，它是一个更令我开心的巧妙作品:一个由木板制成的底座，以圆形排列容纳 5 根不同长度(24-50 厘米可见)的管子。

[![](img/88846cb9b9d21d4b04eaad81566ac5ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cfj6l04M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lr39ph9lxf2gybiz250e.png)

这里需要提到的是，我非常喜欢和木头打交道。这是一种奇妙的材料，但最近被完全低估了。给我一块大木头和一些工具，你就能让我接下来的几天忙个不停。我非常高兴地发现，比起塑料和金属，布莱恩更喜欢木头。

# 一份初步的(有点奇怪的)购物清单

该项目的不完整购物清单:
(注:我努力寻找具体的链接，而没有建议某个市场，并提供无偏见的信息。我失败了。至少这些是非附属链接。)

这是我从布莱恩那里得到的消息:

| 项目 | 数量 | 描述 |
| --- | --- | --- |
| [LED 灯带 2x5m](https://www.amazon.com/gp/product/B01CDTEJBG/) | 2x5m | 可单独寻址的 LED 灯条 |
| [LED 扩散器，每包 5.1 米](https://www.amazon.com/gp/product/B01NBC0NI9/) | 5x1m |  |
| [5V 30A 电源](https://www.amazon.com/gp/product/B078RXZVDH) | one |  |
| 电力电缆 | one | 剪掉连接电器的那一端，保持插入墙上插座的插头完好无损 |
| 树莓 Pi 3 型号 B+ (1) | one | 确保获得一个快速的 SD 卡 |
| 2x30x30 厘米胶合板(2 块) | Two | 在当地的五金或工艺品商店找到 |
| 2x6 厘米暗杆/木块(4) | four | 在当地的五金或工艺品商店找到 |

这些是我在构建阶段添加的东西:

| 项目 | 数量 | 描述 |
| --- | --- | --- |
| 铝型材 | 2x1m | 选择任何适合你的最终基础高度 |
| 90 度角截面轮廓 | 1m | 每侧 8 毫米，覆盖上述型材的连接角 |
| 古董蜡 | 任何的 | 给木头表面最后的润色 |
| 矿物油/橄榄油/核桃油 | 任何的 | 为了强调木材表面的结构 |
| 装配胶粘剂 | 1 盒 | 这东西就像加法尔磁带 |
| 不同直径的电线 | 任何的 | 选择任何适合你的最终基础高度 |
| 废水管道 | 5x1m | 40 毫米直径，也购买配件(5)和端盖(10) |

当我拿到设计文件的那一天，我就迫不及待地开始建造了。我有不同项目的胶合板，但我没有圆锯。这已经是项目的一个关键部分——基础。我不能冒险让两边不平，因为我用了一个拼图玩具(顺便说一下，是一个便宜又糟糕的玩具。好的那个，我爸的传家宝，几个月前放弃了)。所以出发被推迟了几天，直到我能从我的岳父那里借一把圆锯(顺便说一下，他曾经在慕尼黑开了一家工具租赁店。每当我需要工具时，我知道去哪里找。)

# 时间构建

对于基地，我用了两张好胶合板(1.8 厘米厚)，每张 30 x 30 厘米。

[![](img/0d556c94def45b7e6a0f52ba44e263e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jte1d4rY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bftn5oglrh47odge9ars.png)

我在较好的一张纸上钻了支撑孔。这将是整个装置中最上面也是最显眼的一块木头。还要确保使用合适的钻头和钻床来钻 5 个 35mm 的孔。我从 30 毫米开始，因为我没有铝通道系统，使管在这一点上。因此我没有确切的措施。重要的是要知道哪一面应该是最后的顶面，因为所有的钻孔和锯切总是会轻微磨损底面的木材。如果不确定，你最好先在一块不重要的木头上试试你的工具。

这是两张纸，大小完全一样，上面一张有孔。通道系统，铝型材(每个 50 厘米)，松散地粘在里面。床单之间的临时隔板。那时，我还不知道要将四个型材组合成一个圆管，里面的 LED 灯面向四个方向。

[![](img/475c5df7b25b37c33ab16359455897ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RzT9a_v0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rhc1hbnolqnvq1lt854g.png)

[![](img/882829ef333d950d656de76c2378c416.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_RtBjExZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/epdqkywg9d8ajk1b0gyi.png)

目前看起来不错。

[![](img/903041bef2cd754bc566548b5e411d57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NRuUgpQO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2umskc91vsj3286vbvpu.png)

铝型材由非常软的铝制成。最简单的方法是用手锯锯，然后锉平，使边缘光滑。有时，在这篇博文的后面，当使用铝时，盘式研磨机会很方便，但是对于铝型材来说，你会对软材料施加太多的能量。

[![](img/6f2739a7a3289e9773f0a2e3c40ed3d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_w1I2TGp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q6vpnus7oknkd0fhwcxs.png)

将所有型材切割成合适的长度需要一些时间。我发现最简单的方法是用双面胶把它们粘在一起，然后用老虎钳夹住。由于老虎钳仅从两侧施力，确保用压缩钳将另外两侧(垂直方向，因为老虎钳通常水平夹紧)固定在一起，这样四个型材不会在老虎钳中张开。

[![](img/7c204b90b15bec4e2a38a0f993c11119.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4JGblmcE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6u6xtbpwxtp7exoroa9f.png)

在型材长度合适的情况下(最终长度+板材间距+顶部板材厚度)，我想看看应用扩散片后的效果:

[![](img/73b5941f938921532db4a405ede336ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RVAH0BpR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9bkbkoao4gfyxe10jvsq.png)

如果你打算和某人一起建造你自己的，记住沟通是很重要的！只是在我订购了一条 5 米长的灯带(我仍然不确定 LED 的总长度)后，我们才就此进行了同步，Bryan 告诉我订购背面带有粘合剂的 LED 灯带。我还是第二次点了同样的带子，以避免制造上的差异。如果一些发光二极管是不同的色调或亮度，这将是一个令人讨厌的光学元件。但这意味着我不得不自己在将近 10 米的 LED 灯带背面贴上双面胶。没我想的那么麻烦。把它们粘在卷好的带子上，用美工刀沿着带子的边缘切割。我已经适当地加长了它们，以便在尽可能短的胶带上贴尽可能多的条。

[![](img/86f7e4263dd0628a763164588e570eb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zcZJjHLu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oevm8sk6gp5ps6tfvreh.png)

400 多个发光二极管。看起来已经很不错了，对吧？将它们应用到概要文件是很容易的。一条好的双面胶，什么都值。有如此多的廉价选择会导致最终产品的悲伤。避开他们。不过，你不需要最昂贵的零件。只是一个你信任的好品牌。

[![](img/4e52771963bd6312ccab5ca5f5fe118d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MQgGy3hm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5mrm5iy4yt55wrsxewuk.png)

我在型材上钻了孔，用来连接一个灯管的 led 灯条的电源。我最初以为这个计划是让每一个带单独得到它们的信号，因此我一开始没有为第三根电缆钻孔。回想起来，我应该还在计划第三个钻孔。这是后来钻了所有的电力电缆焊接已经完成。对于我们的设置和场景，可单独寻址的条会变得太复杂。我想它可能会产生更疯狂的动画，每根管子和每条带子都可以单独寻址。

[![](img/0d04a90bb264ead895100256464c2a77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mzzxc7zC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c6j9kuwdc3ht8qfix9nu.png)

再次测试它的外观。如果它以预期的方式工作。

[![](img/472c9fe036e06ebe0d4b60ee6d059c42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r6Ciyorl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dixhka08nq9k93l65cxp.png)

最长的管子(可见长度 50 厘米)首次安装在底座中，并连接到控制板。

[![](img/462e6c0d731945967d65900adba0a8d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IF1_HygR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zy0xqgi6le14ilwgtilu.png)

我做了一个临时的布线解决方案，因为我不确定最后的布线。这是焊接过程中的最终布线设置，所有三根电缆都有穿过型材的通道。

[![](img/02fbc48571f39fd1931b560d0bd81c8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p4JA0cYm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lrxc3fw6bh14zw8cvj7m.png)

我还从 Bryan 那里了解到，由他定制设计并负责解释和传输动画到 LED 灯带的控制器板将无法提供足够的电力来安全地操作灯带。我们的购物清单上有一个巨大的 5V 电源，USB 交流适配器可以处理的 2A 与 LED 灯条可能消耗的相比微不足道，在极端情况下可能超过 20A。考虑到这一点，我还选择了直径更大的电缆(1 mm2)作为电源。

[![](img/a68f13b93feec39cc7cfd30f73af0b77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pKhl_biY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qcxvslsat0ah500bzoo0.png)

[![](img/c1c584496e073ca016af068c95c18cf0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fvnbNOdK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ff8q8weadufcnxeruj3z.png)

走了这么远，我问布莱恩他有什么计划来覆盖基地的两侧。考虑到我们需要确保设计中的空气流通，我们不能用更多的木材覆盖侧面。但我也想看到胶合板风景如画的一面。在这一点上，我已经砂光(最终颗粒:320-400)，油(核桃油；矿物油可以从公认有点无聊的轻质木材中获得更多)并给木材上蜡(古董蜡，用于点睛之笔和抵抗触摸和磨损的坚固性)，现在覆盖它会很尴尬。

[![](img/38ea43f760844ab8c777c0451c3fb905.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7q9ibwOP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zdh0ujo7yssmrfwwsfuo.png)

[![](img/e3c23124353b40cb77cf9e635f2292b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--knbNFw1q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jfeyavnd0kjtapyl6v1a.png)

但首先，布线必须达到生产水平。这是接线最终的样子:

[![](img/8fd53dd61274089f1a803bf77ab1b88f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kUR9gIKU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wez0sakgin31f50rs7pf.png)

[![](img/172b2f2bfcaa07b03154dae6f5c5446a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yVgVr-KW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/10z9v7akxql4kezrx6he.png)

焊接有点尴尬。我没有最高的焊接技能，也从未在专业水平上学习过这些。我对最终的结果很满意，但是我建议每个人也投资一些钱在一个好的焊接站上。当你有一些备用电源时，焊接的过程会顺利得多。我还建议在 LED 灯条的焊接点背面和铝型材之间放些东西。两者之间的物理接触会疯狂地吸走焊点的热量。用力过猛只会增加能量损失。CPU 冷却器也由铝制成是有原因的。

[![](img/5f7732fcd6b3ce09a9132f1005ac79e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QD4pLDMy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7h1moy0h7xteu40s9op9.png)

然后，我还扯掉了焊接点的一个管的供应时，从基地删除它。我诅咒自己，但我知道我必须采取预防措施，不让这种事情再次发生。

[![](img/d6c519d1e1b8885bfe4f1d028b955ad2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4848al4S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1kqp8pwfes1jrgdz6sys.png)

我不是唯一一个操作这个装置的人。而且我知道我不能指望别人有和自己一样的勤奋。总是用你的预算建造尽可能坚固的建筑。这不仅适用于手工艺。

[![](img/539028116bef1a80b94a23478362ea88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bI-vw2gY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9l3hm8hxt6842sl8der1.png)

完成布线和安装基地的一切都发生在一个伟大的心情。大部分东西都做好了。至少我是这么认为的。

[![](img/634db989a56f4e9672d8ecb989016824.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4ku67M6x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b06o8jlt982vqhn75o2c.png)

我建议我们再用一次铝，这将符合轮廓。已经有通气孔的东西。你当地的五金店可能有一些现货。我的也是，但当我在商店的盟友中闲逛时，我看到了更有趣的东西:更多的个人资料。一个看起来非常合适的。具有合适的高度以覆盖床单之间 6 厘米的空间。但这意味着我必须自己钻通风孔。很多人。

[![](img/0de261018413032b18fac116a6a989e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7PNlVBwF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kubmwceue2ww217psaye.png)

新的侧面轮廓看起来很棒，但我仍然不知道如何让它们粘在底座上。

[![](img/2f25b4d7f0ed9d497577a8b8b94986de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RimJERAg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j06h7icy6jbgvfnq4joc.png)

[![](img/791784fac44e4deab94ecf23b0f6044b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FUdafOJL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f31ulwh48nm1q44fxs1e.png)

我用圆盘研磨机把两边切成 45 度角。不是很精确的工具，但对工作来说足够了。我计划在每个角上有 90 度角剖面。

[![](img/fe0b09b3284c1f83fd13a4fb3d8b9212.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CehGfYbC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bii1cstv8qcptqe9t51u.png)

我自己钻的通风孔给创造力留下了一些空间。为一个特定的用例 JSConf 构建这个。欧盟，意味着我们可以借此机会传播一些开发商的爱。

[![](img/f8b91fcdc02905a0d2435a16a2da6431.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yG9noXCg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j2ga7ghtvvjyset1eiex.png)

有了 5 行(5cm)25 列(25cm)的矩阵，为什么不做点比特艺术书法呢？

[![](img/91d43db4d7da001fdcc665ffa0698a5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DESL8R0L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tv903exg1xls5r54kh89.png)

钻头< 3 JS

[![](img/daa206e813cb4f583c9bbcdb9a3bea04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gsuJixQb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yv5kdh3cd552elmdj2i0.png)

为了把侧盖粘在一起，我决定用组装胶。我不想用螺丝制造更多的分心点。这将是最干净、最灵活的解决方案，尽管不是最可靠的。时间会证明它是否有足够的粘性。铝不是粘合剂的最佳基础。

[![](img/cad9eea2759136a3fbc8712493654147.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DTZuqY-C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xzre9cbzix9gjah1xx9n.png)

您仍然可以看到一些多余的装配粘合剂。我需要用切卡刀把它取下来。

我现在对最终的结果非常满意。非常值得我投入的 20 多个小时(保守估计)的工作。这是定制的，你可以看到这一点，这很好。然而，神奇之处不仅仅在于硬件。请务必跟随 Bryan 的博客文章系列，看看这个可视化艺术作品背后真正有趣的技术是什么。

[https://www.youtube.com/embed/Y4pN53zziyA](https://www.youtube.com/embed/Y4pN53zziyA)

PS:我面临的另一个挑战是如何将这个装置带到柏林。当然，我计划部分解构它(移除管子)，但最长的管子是 58 厘米。这远远超出了我所拥有的飞行案例。你猜怎么着，我在五金店找到了一个完美的保护罩。安装所有 5 根管子只需 12 欧元。

[![](img/651794b8e954320eb5c7356e4dc6478c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n59Co4Pi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sqgwbtd0abiljg9ddr3q.png)

这些管道被称为 HT (Hochtemperatur，翻译过来就是高温， [Link](https://de.wikipedia.org/wiki/Hochtemperaturrohr) ，只有德语才有)尺寸 DN 40，内径约为。36mm，非常合身。端盖每个只有 65 美分，顶盖是端盖和配件的组合。我在十个端盖上都装了一点缓冲垫。然后，它们被牢固地粘合(再次使用装配粘合剂)到管道的末端。配件放在短管的顶部(最长可达 5 米),无需更多的粘合剂或固定就能很好地固定。我会把这 5 根管子绑在我行李的外面(这样也能安全地携带底座)。

希望能在 JSConf 上见到你。2019 年 6 月 1+2 日欧盟！