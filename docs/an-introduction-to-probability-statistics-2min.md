# 概率统计导论

> 原文：<https://dev.to/brandonskerritt/an-introduction-to-probability-statistics-2min>

> “概率论应该被扔到公共汽车下面”——人工智能专家卡洛斯·e·佩雷斯。

我们从学习概率论开始，然后钻研统计学。

概率和统计在计算机科学中一直被使用。机器学习？是概率。数据科学？是统计学。

### 高等级概率

概率提供了一种总结不确定性的方法，这种不确定性来自我们的懒惰和无知。换句话说，概率找出了某事发生的可能性。

### 离散概率

离散概率是概率论的一种形式，它从离散数学中描述了在计算机中使用的概率。

在用离散概率解决问题时，我们从使用概率空间开始。概率空间是配对(S，P ),其中:

1.  S 是所有基本事件 X ∈ S 的样本空间。S 的成员称为实验结果。
2.  P 是概率分布，即给每个基本事件 X ∈ S 分配一个实数 P(x ),使得它的概率在 0 和 1 之间，并且∑P(x) = 1

对于点 2，P(x)读作“X 的概率”。概率必须始终介于 0 和 1 之间，或者通常表示为 0%和 100%。

### 举例

想象一下抛硬币。概率空间是(S，P)。结果 S 是**S = {H，T}**其中 S 可以是正面也可以是反面。因此，概率 isP(H)= P(T)= 1/2 正面的概率与反面的概率相同，都是一半。换句话说，如果你掷一枚硬币，它有一半的机会正面朝上或反面朝上。

如果每一个结果都同样可能，那么概率分布被认为是均匀的。

### 解决概率问题入门

很多很多人包括大学教授和博士生都不会解概率问题。正如本文后面所讨论的，Monty Hall 问题是一个著名的问题，也是一个很好的例子。

> 假设你在参加一个游戏节目，你有三扇门可供选择:一扇门后是一辆车；在其他人后面，山羊。你选了一扇门，比如说№1，知道门后是什么的主人打开了另一扇门，比如说№3，里面有一只山羊。然后他对你说，“你想选 2 号门吗？”改变你的选择对你有利吗？

这个问题被送到当时世界上智商最高的 Voe Savant 那里。Voe Savant 回答，换车有 2/3 的几率赢车，不换车有 1/3 的几率赢车。

成千上万的人争论蒙蒂霍尔问题，许多大学数学教授说数学盲在美国很普遍，因为蒙蒂霍尔问题的解决方案是错误的。

这个问题出现在接下来一周的每堂数学课中，成千上万的读者，其中许多是数学博士，写信来解释莎凡特是错的。甚至世界上最著名的数学家之一保罗·erdős 也说莎凡特是错的。

不幸的是，莎凡特是对的。这是一个简单的概率问题，如果正式定义就可以解释。许多数学家用他们的直觉来解决这个问题，而不是按照下面概述的解决概率问题的步骤。

在解决一个概率问题之前，你需要采取几个步骤来证明你完全理解了这个问题。

### 样本空间

样本空间是包含所有可能结果的集合。

所以给定一个硬币，样本空间是{正面，反面}，因为硬币只能正面或反面着地。

### 胜负

在实验完成后，结果由实验的所有信息组成。当你掷硬币，硬币正面朝上，结果是{正面}。

### 概率空间

概率空间是样本空间，但是每一个可能的结果都有一个应用于它的概率。抛硬币的概率空间是{(正面，0.5)，(反面，0.5)}。

概率空间中所有概率的总概率必须等于 1。没有一个概率可以小于 0 或大于 1。

[![An Introduction to Probability & Statistics](img/01cd842e5607ea6764f0ac650214be9f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JeFVHFOW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Aw98z_3H7Vm9Ruu-3rR_FrA.png)

许多成绩优异的学生告诉我，他们试图尽可能多地想象他们正在处理的事情。

### 举例

假设我们掷一个六面骰子，我们想计算出得到 4 的概率。

1.  计算可能事件的数量。骰子有六个面。所以有 6 个可能的事件
2.  决定你正在检查哪个事件的概率。这个问题让我们知道我们正试图掷出一个 4。
3.  计算在可能发生的事件中正面出现的几率。骰子只有一边有 4 个点，所以总共 6 次机会中只有 1 次机会掷出 4。
4.  写出可能发生的概率与可能发生的事件的分数之比。(1/6)

虽然这是一个简单的问题，但它说明了在解决较难的概率问题时要采取的重要步骤。

### 事件

事件在概率论中经常被忽略，也不怎么被讨论，所以我决定在这一节中阐述什么是事件以及为什么它们很重要。

事件是概率实验的一组结果。在贝叶斯概率中，事件被定义为使用来自当前状态的知识描述下一个可能的状态空间。

一个事件通常用字母“e”来表示。例如事件的概率是 P(e)。事件在概率上比大多数人认为的要重要得多。

一个事件可以是掷骰子(如“5”)的结果，也可以是掷硬币时得到一条尾巴的结果。

事件可以是:

1.  独立—每个事件不受之前或未来事件的影响。
2.  从属—一个事件受其他事件影响
3.  互斥-事件不能同时发生

### 事件为什么重要？

嗯，事件允许我们用概率做一些相当惊人的事情。以蒙蒂·霍尔问题为例。尝试回答以下问题:

[![An Introduction to Probability & Statistics](img/ce8617e82f7fef8f5ddc6593cb7a515f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RB4eZddz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AqHDZ_HkSDRAullLhe05VSQ.png)

上面的一扇门包含一辆豪华跑车，另外两扇门包含山羊。随便挑一扇门，去吧！

好吧，假设你选了 1 号，游戏节目主持人会打开一扇门，里面有一只山羊，所以假设我们打开 3 号门，里面有一只山羊。所以你知道 1 号门是你的选择，3 号门是山羊，2 号门没有被碰过。注意:你最初选的是什么门并不重要，重要的是你选了一扇门，gameshow 主机打开了一扇门，里面有一只山羊。

[![An Introduction to Probability & Statistics](img/31aeae4e35f68a1b4d71d3bb98e6aa3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7avqnUtX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AVa0XV0-W6rspB4xihWBmQA.png)

游戏节目接着问:“你确定 1 号门是对的吗？要不要换？”

你是做什么的？

嗯，概率表明我们应该选 2 号门，因为你会换。为什么？嗯，2 号门有 2/3 的机会或 77%的机会包含汽车，1 号门(你最初的选择)有 33%的机会包含汽车。

怎么了？？

这是一个著名的概率问题，称为蒙蒂霍尔问题，它展示了事件如何影响概率。为了解释这一点，请观看下面的数字爱好者视频:

### 一个事件发生的概率

一个事件的补充是一个事件的所有其他结果。

例如，如果事件是反面，补码是正面。如果事件是{星期一，星期二}，补码是{星期三，星期四，星期五，星期六，星期日}。

如果你知道 p(x)的概率，你可以通过做 1-P(x)来找到恭维语。因为所有的概率都等于 100%，我们可以把它表示为 1。

### 补语为什么有用？

有时在实际概率之前先算出补数更容易些。例如:

```
Work out the probability that when 2 die are thrown that the two scores are different 
```

不同的分数就像得到了 2 和 3，或者 1 和 6。所有可能的不同分数的集合相当大，但是所有可能的不同分数(分数相同)的补数相当低。事实上，它是:

{ (1, 1), (2, 2), (3,3), (4,4), (5,5), (6,6) }

不同组合的总数是 6*6，也就是 36，所以得到相同分数的概率是 6/36 或 1/6。现在我们可以从 1 中减去 1/6(把 1 想象成一个普遍的集合),这等于 5/6。

### 两个事件的并集(互斥原理)

这需要你了解一点集合论，所以点击[这里](https://medium.com/brandons-computer-science-notes/a-primer-on-set-theory-746cd0b13d13)了解更多。

如果两个事件是互斥的(它们不能同时发生)，那么它们同时发生的概率是 0。

如果两个事件不是互斥的，那么两个事件并的概率就是两个事件相加的概率。

[![An Introduction to Probability & Statistics](img/463a224db6dd2a1ef0da4649da47c15a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kOyT5MFs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A6oJV0e4u70nsViAjjVkEOA.png)

我们去掉 A 和 B 的交集的原因是因为 P(A) + P(B)包含了 A 或 B 中的所有内容，但是由于 union 的工作方式，将会有一个交集，这将产生 2 个 A 和 2 个 B，所以我们需要去掉这个交集来得到每个事件的概率。

换句话说，包含 B and B 中的元素的包含中的元素。

[![An Introduction to Probability & Statistics](img/3e219ad8a43a8c904693e768c07aabae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ni_4Q_aK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AkjnsvpWVqtuMe9zSRLMyAA.png)

[![An Introduction to Probability & Statistics](img/407e74b9c8d5a99d90080c33f93dfdd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PgwHkcDY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AnL3wFe5taxhNJZhZwaFljw.png)

### 三个不相交事件的并集

假设我掷出三次公平的骰子。s 是长度为三的事件序列的集合，使得{ 1..6) }P(x) = 1/6*6*6 = 1/216 对于所有 x∈s 我们掷出至少一个 6 的概率是多少？因为我们掷骰子 3 次，设 E1 是掷骰子 6 的概率，E2 = P(6)，E3 = P(6)我们想算出 P(E1∪E2∪E3)

请记住，概率的并集是 P(A)+P(B)—A 和 B 的交集。我们希望 A、B 和 C 的并集也包括中间的交集。我们去掉 A B，A C，B C 的交集，加上所有 3 的交集，得到中间部分。

[![An Introduction to Probability & Statistics](img/aea662c2470f2d4b0e7303228366a3b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VElbHF6K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AZiv4odRalsjjVFsn.png)

所以这只是:

[![An Introduction to Probability & Statistics](img/87332447efa6f13295030d07131b62db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---lwcKMYL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AJNvJ5ezGe_mtLE0wKXx4Sg.png)

你可能已经注意到了这个交叉点是 6/216。这可能看起来令人困惑，因为我们没有为此手动定义一个集合。不用担心:交集的公式是:

[![An Introduction to Probability & Statistics](img/856c1f1440695fd56c8ccf7c91ce895f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XqLNoAoQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AyqWpNDejt5Khtiuw-zN1Iw.png)

### 例题

```
Given 4 coins, what is the probability that at least 3 of them come up tails? 
```

至少 3 枚硬币出现反面的事件是 5 个不相交事件的并集，即所有硬币出现反面(1 个不相交事件)并且 4 枚指定硬币(4 个不相交事件)出现正面。这听起来可能有些混乱，我就形象地解释一下。如果你不困惑的话，可以跳过下一段。

分离事件意味着事件不能同时发生。第一个不相交的事件是“如果所有硬币都是反面呢？”那就是这 5 个硬币{T，T，T，T，T}。另外 4 个事件是如果一个指定的硬币正面朝上会怎样？所以第一个不相交事件是{H，T，T，T}，第二个是{T，H，T，T}等等。因为我们需要至少 3 个硬币作为反面，{H，H，T，T}无效。

5 个不相交事件的并集是每个事件发生的概率相加。

首先，让我们找出在这个空间内任何概率都是可能的概率。问题空间是 4 个不同硬币上的{H，T}。每个硬币有 1/2 的机会是正面或反面，有 4 个硬币，所以 1/2 * 1/2 * 1/2 * 1/2 是状态空间中任何可能结果的 1/16 的机会。

因此一个事件的概率是 P(1/16)

我们知道在 4 个硬币上得到{H，T}的任何组合的可能性有多大，我们可以用这个来计算得到 5 个不相交事件的可能性有多大。由于每个事件都是不相交的，一个事件不会影响另一个事件，所以这只是 1/16 * 5(对于 5 个不相交的事件)的情况，结果是 5/16。

因此，至少 3 枚硬币出现反面的概率是 5/16。

### 条件概率

条件概率是指一个事件只有在另一个事件已经发生的情况下才会发生。让我们从一个简单的问题开始:

John 最喜欢的编程语言是 Haskell 和 x86 汇编。设 A 代表他强迫一个类学习 Haskell 的事件，B 代表他强迫一个类学习 x86 汇编的事件。在随机选择的一天，约翰自己被撒旦接管，所以 P(A)的概率是 0.6，P(B)的概率是 0.4，他教 Haskell 的条件概率是 P(A|B) = 0.7，根据信息，约翰教 x86 汇编的条件 P(B|A)是多少，四舍五入到最接近的百分位？

概率 P(A 和 B) = P(A|B) * P(B)读作“|”如给定，如在，“A|B”读作“A 给定 B”。也可以写成 P(B|A) * P(A)。

之所以是 P(A|B) * P(B)是因为给定“给定 B 发生的概率，A 发生”的概率，B 的概率就是 P(B)。(A|B)是与 P(B)不同的概率，P(A 和 B)只有在 P(B)发生时才能发生，而 P(B|A)又允许 P(B | A)发生。

所以我们可以把它转换成一个数学公式:

P(A 和 B)= P(A | B)* P(B)= 0.7 * 0.5 = 0.35 求解 ITP(B|A)* P(A)P(A)= 0.5 so 0.6 * P(B | A)现在我们不知道 P(B | A)是什么，但是我们想找出答案。我们知道 P(B|A)一定是 P(A 和 B)的一部分，因为 P(A 和 B)是这两个事件发生的概率...P(A 和 B)= 0 . 350 . 35 = P(B | A)* 0 . 5 通过简单的代数运算 0.35/0.5 = P(B|A)P(B|A) = 0.7

想看条件概率的直观解释，请观看可汗学院的视频

### 贝叶斯定理

贝叶斯定理允许我们在已知事件的先验知识的情况下计算出事件的概率。与其说它是一个定理，不如说它是一个观察结果，因为它总是正确地工作。贝叶斯定理是由托马斯·贝叶斯创立的，他在一个笔记本上记录了这个观察结果。他从未发表过它，所以他在有生之年没有因为他著名的理论而遭到反对。

<figure>[![An Introduction to Probability & Statistics](img/d20852acb743e291fd48411d0b0cfbac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YM97zniV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A4NP-Lj4PxOP98zmv6IfilA.png) 

<figcaption>贝叶斯定理来自[https://better explained . com/articles/colorized-math-equations/](https://betterexplained.com/articles/colorized-math-equations/)</figcaption>

</figure>

给定 B 的概率是 B 给定 A 的概率(注意:这里倒过来了)乘以 A 的概率除以 B 的概率。

当然，这听起来令人困惑，所以它可能有助于看到一个例子。

假设一股新的墨西哥黑焦油海洛因在街上被发现，警察想要识别某人是否是使用者。该药物具有 99%的敏感性，即被正确识别为服用该药物的人的比例。该药物具有 99%的特异性，即被正确识别为未服用该药物的人的比例。注意:用户和非用户都有 1%的误报率。假设约翰穆雷斯公司有 0.5%的人服用这种药物。随机选择的测试结果为阳性的约翰·穆雷斯学生是用户的概率有多大？

[![An Introduction to Probability & Statistics](img/1a26853d1f0adabd8e87ab8a38fac909.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t-AlfNwI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AHmzHFZOkqEjKx3ulkPFuBQ.png)

一旦你有了所有的信息，这只是一个简单的代入值并计算出来的问题。

下面的视频直观地解释了贝叶斯定理，并附有真实世界的例子、其背后的历史以及贝叶斯定理的哲学:

如果你想知道贝叶斯定理是如何用于机器学习的——看看这个！

[**机器学习的绝对基础**](https://hackernoon.com/absolute-fundamentals-of-machine-learning-dca5deee78df)

[_ 机器学习，真是个流行语。我相信你们都想了解机器学习，这就是我要去……_ hacker noon . com](https://hackernoon.com/absolute-fundamentals-of-machine-learning-dca5deee78df)

### 随机变量

随机变量是一个函数，它不是随机的，也不是变量。

随机变量不需要直接指定样本空间 S，而是分配一个变量(X)取某个值的概率。与之前需要定义样本空间的概率不同，我们只关心概率本身。

随机变量通常写成 P(f=r)其中 f 是事件名称，r 是概率。

像所有概率值一样，概率必须在 0 和 1 之间。

对于 F 是除 r 之外的每一个变量的情况，我们写 NOT(用你希望的任何符号)(F=r)。

这方面的一个例子

```
P(Die=1) = 1/6The probability that this die takes the value 1 is 1/6NOT P(Die=1) is the event that the die is(Die=2) OR (Die=3) OR (Die=4) OR (Die=5) Or (Die=6) 
```

P(f=r)的补码；用于表示随机变量的符号是 1 — P(f=r)，其中 1 是 100%或仅仅是 1。

我们有时用符号(单词)代替数字来表示随机变量。这真的很有用。假设天气可以是 4 种状态中的一种，晴天、下雨、多云、下雪。因此，我们可以写天气=晴朗，而不是指定天气= 1。

有时写下所有的概率很冗长，例如 P(天气=晴天)= 0.7 或 P(天气=下雨)= 0.3。如果值按顺序固定，那么我们可以写 P(天气)= (0.7，0.3)

我们使用粗体字 **P** 来表示结果是一个代表天气个体值的数字向量。例如: **P** (天气)= (0.7，0.3)。

### 联合概率分布

联合概率分布允许你有多个随机变量，通常是 50 或 100 个，但我们的例子将包括更少。

下表给出了随机变量 Weather 和 Cavity 的可能联合概率分布 **P** (Weather，Cavity ):

[![An Introduction to Probability & Statistics](img/0aa69243eb1448e8ff7a1219fde0e0c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DBIPMG2I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A0hoJlRjzSXrTqCyPDre1Lg.png)

这是蛀牙和天气的联合概率分布。Cavity 是一个布尔值，它是 0 或 1，有 4 个天气选项。如果我们想创建一个 P(天气，洞穴)的联合概率分布，我们可以制作上表。

天气=晴朗，空腔= 1 的概率为 0.144。联合分布的概率总和为 1。

### 全联合概率分布

如果域中所有相关的东西都包括在内，我们称之为全联合概率分布。与上面的例子不同，蛀牙和天气不在同一个域中。

假设随机变量牙痛、蛀牙、感冒完全描述了去看牙医

那么下表给出了完整的联合概率分布:

[![An Introduction to Probability & Statistics](img/3785be0939f77ba8b13c881cb1449ac9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ueKt8Gtu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2Agea23EgYphQi3foF.png)

从[到这里](https://math.stackexchange.com/questions/1976663/need-help-calculating-full-joint-probability-distribution)

### 边缘化

人们可以通过对变量求和来计算随机变量的边际概率。例如，在上面的例子中，如果想要对 P(空腔=1)的概率求和，那么你将对空腔等于 1 的所有概率求和。

[![An Introduction to Probability & Statistics](img/4c6dcb5fac724cd41b2d800a2aeca99a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VAauJiWM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AObUU8ZDp9CWfTyUulP5nnw.png)

### 条件/后验概率

我们可以像通常那样计算完全联合分布的条件/后验概率。

[![An Introduction to Probability & Statistics](img/77ecd01543571f949f05815d4099c803.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rlAbN5pg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AvIWe-Tmj75QHvqlbAR9ZTw.png)

注意(F，G)表示 F (and，交集)G。

### 预期值

期望值就是它听起来的样子，你期望的值是什么？你可以用它来计算 6 次掷骰子的平均分，或者任何与概率有关的有价值的东西。

给定结果=(1，2)和概率=(1/8，1/4)期望值，E[x]是 E[x] = 1(1/8) + 2(1/4) = 0.625。

[![An Introduction to Probability & Statistics](img/f20f9dae34b9f2bffeae013598692b3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mE3I7zjr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AAeV39_mjhpyjZtaSRLC7LA.png)

假设我们正在计算自行车的种类，我们有 4 辆自行车。我们给每辆自行车分配一个代码，如下所示:

[![An Introduction to Probability & Statistics](img/361bcd788325ebc2792b3eaf097da380.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--am1DDBkQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AabbFixm6lr_ENDq5OuVFsw.png)

对于每辆自行车，我们都给它一个号码。对于每个编码，我们可以看到我们使用 2 位。不是 0 就是 1。对于期望值，我们不仅需要变量的值，还需要概率。每辆自行车都有相等的概率。所以每辆自行车都有 25%的几率出现。

计算期望值时，我们将概率乘以 2 位，得到:

[![An Introduction to Probability & Statistics](img/f15785f14a230fc6c57cc17a02470384.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PIg_OcrI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ACOX5CcPoUAawmFXqd_27WQ.png)

如果概率不相等呢？

[![An Introduction to Probability & Statistics](img/9fea1dd3a495ea9f9a4a0eab79ee54c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0wiOvNnW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A2BN_KfFV2guDUfs89gBSLA.png)

我们需要做的是用概率乘以位数

[![An Introduction to Probability & Statistics](img/bb00a37171b5c0c2040d6c7fcfc521f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cE7q_23N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AwHvOPbsGOQYbRuMh-A_Ybg.png)

### 熵

熵是与随机变量相关的不确定性的度量。它被定义为传递变量值所需的预期位数。

[![An Introduction to Probability & Statistics](img/3b7fe6b59803eb54d37a8f1ab8569165.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---n7pmsgK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ac_3RiTHigg36ry1XOTiQwg.png)

熵试图给事物的不确定性一个数字。

### 统计数据

统计学不是概率论。统计学是概率论思想在现实世界中的应用。这些可能包括:

1.  选举学
2.  数据分析—数据科学
3.  质量管理

### 样本空间

样本空间是作为单个有限集的数据集合，类似于:

[![An Introduction to Probability & Statistics](img/257a955bdfb1a6e066454b6c3f375501.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EB79cNDO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ApGfv8JlWce49qMdVyVb6-w.png)

其中 S 是样本空间。

### 概率分布

假设我们想从一组阅读《太阳报》的人中随机挑选一个人。一个人被选中的概率是:

[![An Introduction to Probability & Statistics](img/2e9e5972d8a0c45b16363edcbe9b7f19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HP6XcLnf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AIIuQJTJK_AzbgK6OrK5gqw.png)

概率分布是一个样本空间，其中每个项目都有一个分配给它们的介于 0 和 1 之间的概率值，表示它们被挑选的可能性有多大。

总的来说，如果 S 是 S 的元素，也就是说，如果元素 S 是样本空间 S 的集合(组)的一部分，那么:

[![An Introduction to Probability & Statistics](img/810cedb01b4f61f5683da8962c260799.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8pY9ZgoN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Aq8frCWvgjDzsYy0znPPxoQ.png)

如果将样本空间中每个元素的概率相加，其总和必须为 1。

当我们想要对这个数据集进行采样时，我们只需仔细检查数据集中的每个人，就能很好地感受到这个样本的普遍性。然而，如果这个数据集中有 70 亿人，这可能需要非常非常长的时间。

现在有两种方法可以对数据进行采样。

我们可以从数据集中随机选择一些人作为我们的样本，或者我们可以手工选择一个特定的数据子集来使用。

一个**统一的**数据集是一个每个人都有同等可能被选中的数据集。一辆**偏向**样不整齐划一，人被手摘了。

无偏的数据集似乎“公平”，而无偏的数据集似乎“不公平”。有了无偏的样本，我们就无法确定结果。我们不能改变数据使之对我们有利。

有时候我们不在乎“公平”，有时候无偏的样本会导致意想不到的结果。

### 随机变量

还记得之前我们说过随机变量是函数吗？嗯，如果你把一个随机变量应用到一个样本空间，一个像这样的总体:

[![An Introduction to Probability & Statistics](img/a8c128ce935719f163b66e214fed5f15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VZHdOyUl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A1pd0CT0KmFOdbOZXTQoPYQ.png)

你从这个样本空间得到一个**偏向的**数据集。这是有偏见的，因为我们不是随机选择人群。我们正在应用一个过滤器——一个对集合进行过滤的规则，以获得人口的一个子集。

保罗·邓恩教授这样描述随机变量:

> 概率分布的概念。这是对群体(即集合)成员被选中的概率的描述。例如，如果我们考虑一个骰子，群体有 6 个成员:{1，2，3，4，5，6}我们可能有一个对应于公平骰子的概率分布，这样每个人都有 1/6 的概率被选中。例如，如果是一个有偏的骰子，那么概率分布可能是 P[6]=5/6 P[1]=0，P[2]= P[3]= P[4]= P[5]= 1/24
> 
> ，这样各个结果的总和就是 1。
> 
> 一个随机变量最好首先通过忘记概率和考虑从总体到例如实数的任意函数来考虑。在这个例子中，我们可以选择 f(x)=x。现在，与概率分布函数不同，所选择的函数没有约束:群体成员的值不必在 0 和 1 之间，函数值的总和不必等于 1。当一个函数与一个概率分布相结合时，就出现了“随机变量”的概念。现在，分布不再被视为简单地选择群体成员，而是以随机方式选择函数值，也就是说，不是返回所选择的成员(例如投掷骰子的结果)，而是报告该成员的函数值(例如投掷数字的平方)。

### 用随机变量表示平均值

给定一个总体 S，其成员是根据分布 D 抽样的。随机变量 r(s)在 D 下的平均值(期望值)表示为

简单来说，期望值是以下各项的“加权”和(对总人口 S 的所有成员 S 进行计算):

[![An Introduction to Probability & Statistics](img/05ea799ae024a30e3963210dd65454b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TTwklriu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ak9W5oonAUUfXsZ9AyIE8gg.png)

> _ D 选择 s 的几率乘以 r 为 s 返回的函数值，即 r(s)。_ 在无偏分布中

#### 无偏分布

在无偏分布中，期望值就是所有随机变量的总和除以总体规模:

[![An Introduction to Probability & Statistics](img/8c9514f9426b4ce39717f4510124dccd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MVaoKCYC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ADRqshziDzXJMMIoc7NFjfw.png)

这只是你典型的均值，你在学校学的那个。我的老师教了我一首很酷的歌来记住平均值、范围、中间值等之间的区别。

> 中位数是我们相加并除以平均值的中间值。众数是你看到最多的，音域是两者之间的区别！

假设 S 是掷骰子 6000 次可能出现的结果的集合。

那么对于一个“公平”的死亡，你会期望看到每种结果 1000 次。

假设我们有一个游戏，玩家下注 1，如果骰子落在{1，2，3}中的一个上，玩家将得到 2，否则他们将失去他们的赌注。在一场公平的游戏中，玩家有一半的机会会赢 3/6 = 1/2。

### 信心测试

假设一个实验结果的假设是 X，实际结果是 y。

结果 Y 与预测相差甚远，假设是错误的。这叫做**显著性**。

一个**无效假设**声明结果**将**是 x

**显著性**表示**观察结果**与**预测结果**一致的可能性。

一个假设可以被“拒绝”,观察到的结果有三个增加的置信水平:

1.  给定 Y，X 成立的概率最多为 0.05(显著)
2.  给定 Y 的结果，X 成立的概率最多为 0.01(非常显著)
3.  给定 Y 的结果，X 成立的概率是 0.001(非常显著)

这里可能发生两种类型的错误:

**第一类错误** —拒绝真假设**第二类错误** —接受假假设

### 测量显著性

事件的结果会越来越接近期望值，可以用一个叫做偏差的公式来表示。回想一下，样本空间中随机变量的事件是:

[![An Introduction to Probability & Statistics](img/9731e9e41693c5e545608723ab820c88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NFnbv0QT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AYI56gC-6p7BMUib9OoXTwg.png)

方差只是:

> *“所选成员离期望变量有多远”*

[![An Introduction to Probability & Statistics](img/28277dba5b978b1fdb207706ea34d5aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6cCfHxut--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AI2SfKcgHI2gpgfs0rE6Dug.png)

那看起来不可怕吗？如果我们把第一个公式放进去，它会是这样的:

[![An Introduction to Probability & Statistics](img/4949a92c30115165bc92ee7a2bb3ce96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i4cDgsy---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AYKibcjT2qOEtHYpR1trzmg.png)

这看起来是不是有史以来最可怕的公式？

r(s)部分是随机变量，是总体的子集。该部分是随机成员的期望值。

方差总是产生一个非负值。

标准差就是这个公式，平方根。

[![An Introduction to Probability & Statistics](img/843dc54426fabaddc30285cacf93ef9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d91YccZh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Au74zNcbCgBj2bF6mfj8mow.png)

实际上更常见的写法是:

我只是想看看这个公式能变得多有说服力。

标准差只是:

> *“最大(或最小)数据点距离均值的距离”。*

[![An Introduction to Probability & Statistics](img/030f90055bdaa95adb8468df60be68e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q6EgLTpZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AvegPq019hD09KdtSAmCylw.png)

### Q-测试

给定一个实验的预测结果 X 和实际结果 y。如果我们知道实验环境的**标准偏差**，那么我们可以计算该值:

[![An Introduction to Probability & Statistics](img/ed8e909befa2f8e74767a68b32ed0c29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zUMTbpUX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A8PZoP7uQHpBIfXVXj70hOg.png)

如果 q > 0.01，则 X 以最佳概率 0.05 成立；如果 q > 2.33，则 X 以最佳概率 0.01 成立；如果 q > 3.09，则 X 以最佳概率 0.001 成立