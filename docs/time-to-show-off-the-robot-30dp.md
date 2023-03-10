# 展示机器人的时间到了

> 原文：<https://dev.to/dan_starner/time-to-show-off-the-robot-30dp>

现在，我通常不做这样的事情，但在过去的 4 个月里，我醒着的时候除了机器人什么也没做，所以我想我应该炫耀一下，为我的学生感到骄傲...这也解释了为什么我从 12 月底就没发了😮这篇文章几乎一字不差地来自我在 https://www.ghouse354.com/2019-robot 的团队网站上写的文章。

回到 12 月，我正在寻找一个新的 [*第一*机器人](https://www.firstinspires.org/robotics/frc)团队合作。如果你不熟悉第一部*，我强烈建议你深入了解它，并与你身边的团队合作。尽管它的要点是高中团队花六周时间建造一个 120 磅的机器人，最好地完成游戏的目标。今年的游戏**目的地:深空**，主要是围绕场地移动舱口面板(聚碳酸酯盘)和货物(橡胶球)，并将它们放置在各自的目标位置。可以看下面的游戏视频。*

 *[https://www.youtube.com/embed/Mew6G_og-PI](https://www.youtube.com/embed/Mew6G_og-PI)

我最终与位于纽约市布鲁克林区的 G-House 海盗团合作。学生和导师就像一个亲密的家庭，到赛季结束时，*我*也觉得我已经加入了这个家庭。那么，接下来是激动人心的部分...

## 机器人，Ms 计算出来的

我们在 2019 FRC 赛季的机器人， **Ms Calculated** ，是专门为第一次玩而建造的:目的地深空，由波音公司展示。这个机器人是该团队与当前学生和导师团队一起建造的最复杂的机器人之一。再说一次，**我们只有六周的时间来建造这个**，我对学生们的成就感到非常惊讶，因为导师只是提供建议。一切都是由学生设计和建造的。

[![Ms Calculated at the Loading Station](img/42399b61c268876981b0369daa53e97e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nusieUzd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vxahceirqb2gsgsjisdw.jpg)

使用两级级联电梯设计，以及用于收集和存放游戏物件的多功能机械手，Ms Calculated 可以在场上的任何地方得分，包括货船和带有舱口板和货物球的火箭的所有三级。

### 机器人子系统

我们的机器人今年非常复杂，涉及许多不同的移动部件和子系统，请在下面阅读更多信息！

#### 电梯机构

这是我们的学生和导师小组尝试电梯的第一年，我们只能说 Ms Calculated 可以将游戏碎片提升到外太空！🚀…或者至少达到菲尔德火箭队的顶峰！它的特点如下:

*   具有[运动魔法](https://phoenix-documentation.readthedocs.io/en/latest/ch16_ClosedLoop.html#motion-magic-position-velocity-current-closed-loop-closed-loop)定位的两级级联升降机，使用[塔龙 SRX 速度控制器](http://www.ctr-electronics.com/talon-srx.html)提供精确定位，以便在操作员极少干预的情况下将机械手提升到特定的预定高度
*   第二阶段包含用于操纵场部件的机构
*   双驱动绳，用于升降载物台，提高电梯速度
*   在每个阶段安装恒力弹簧，以减少驱动绳张力和提升阶段所需的扭矩

#### 货物入口

[![Our ground to Rocket Cargo cycles were fast](img/124190dfc31df42d50a0db4c9e1e6b56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SoLdwNB4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dla4xdf6vldrzthvw9fe.gif)

货物操纵器被放置在升降舵上，提供了在场上任何地方和任何火箭高度得分的能力。

*   覆盖有橡胶涂层的收集辊，用于收集、保存和存放货物
*   装载站和地面货物收集都允许旋转和锁定手腕

#### 舱口进气口

[![Our hatch intake allowed for driver error and automated collection](img/71fc4c8807a3bb32984eab62629f6205.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XZsWoIB0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qxguwst93w4j0jn1b62i.gif)

*   舱口机构使用气动驱动的抽屉滑轨在保险杠上延伸

*   一对带切口的聚碳酸酯片将在舱口面板内延伸一次，将其保持在适当的位置

*   后来的迭代，如使用限位开关的自动收集和铰链收集允许更多的驱动器错误和更快的评分周期

#### 沙尘暴/自治时期

每场比赛的前 15 秒都是在沙尘暴中进行的，黑色的床单覆盖着司机站，挡住了司机、操作员和教练对机器人的任何视线。为此，Ms Calculated 配备了以下硬件:

*   一个聚光灯摄像机，可以自动发现并跟踪货船和火箭上方的反光带。使用输出的数据，Ms Calculated 可以将自己驱动并定位到刻划位置
*   一个像素 2 的摄像机，可以跟踪和跟随得分目标前面的线
*   一个 170°的鱼眼镜头，可以让驾驶员和操作员看到机器人周围的情况，以便在场地上导航

[![Our drivers were always prepared to handle the sandstorms](img/4ec4a956d97763ed2add17fa962060cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TSN-5jMz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/frerh8xcbrz3l43agshv.gif)

#### 控制/驱动站

不想被前一年的驱动站超越，团队决定加工和建造一个真正独特和特殊的驱动站，它包含以下内容:

*   驾驶员工作站笔记本电脑和外部监视器，允许驾驶员和操作员组织他们的摄像机和遥测
*   适用于驱动控制器
*   把手便于携带
*   定制团队编号和姓名雕刻
*   定制的 LED 屏幕和按钮板，用于控制与驱动机器人无关的所有机制，例如使用电位计和执行按钮的位置选择电梯控制、舱口和货物收集和存放等

查看[https://imgur.com/gallery/Vii1UYP](https://imgur.com/gallery/Vii1UYP)获取更多我们驱动站的图片！

[![Pretty Snazzy Drive Station](img/b7de3de64320cc4f7411681e56027174.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QFprM03V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sqkkz2a4286732lntbtk.jpg)

## 在一天的最后

我们最终在每场比赛的决赛中被淘汰，但最终，我们仍然度过了美好的一年，有一个伟大的机器人和一群更好的学生...为 2020 年更加美好干杯！

**哦，你觉得这很酷，想参与进来？你很幸运，在你的地区可能有一个团队[，所以给我发消息，我会确保让你参与进来！...尤其是如果你在纽约地区，我们总是可以使用更多的导师！](https://www.google.com/maps/d/u/0/viewer?mid=1sNr_BG6fmMVJjLovnQsevLDOfuE&ll=15.011038120855%2C0&z=3)***