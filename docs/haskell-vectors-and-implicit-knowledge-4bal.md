# 哈斯克尔、向量和隐性知识

> 原文：<https://dev.to/cohere/haskell-vectors-and-implicit-knowledge-4bal>

> 嘿伙计们！
> 
> 这篇文章已经有几年的历史了，但是当我把更多的东西搬到这里的时候，我会把它重新发布在 dev.to 上。有点私人的。与我的许多其他工作不同，它不是关于教人们如何更好地使用技术，而是关于为什么以一种可访问的方式这样做对我来说如此重要。

今年冬天我在自学哈斯克尔。

我以前试过，和每个人的宝贝*学你一个 Haskell 为了伟大的利益*。我以前的老板曾把它的“另类”方法比作《T2》_ why ' s 辛酸的 Ruby 指南，我很喜欢。两者都有非正式的语气，但相似之处仅限于此:辛酸的指南有可爱的卡通狐狸，*学你一个哈斯克尔*有令人不安的兄弟幽默。我想我被其中一个(许多)无聊的笑话逗乐了。

这一次，我尝试了真实世界的 Haskell，起初事情进展得更好。材料组织得更有效，例子和练习更适合我的学习风格。
然后我就来了这个问题。

## 问题

真实世界 Haskell 第三章末尾的问题集包含以下序列:

> 1.  Consider three two-dimensional points A, B and C. If we look at the angle formed by the line segment from A to B and the line segment from B to C, it either turns left or right, or forms a straight line. Define a direction data type to represent these possibilities.
> 2.  Write a function to calculate the rotation angles of three 2D points and return a direction.
> 3.  Define a function that accepts a list of 2D points and calculates the direction of each successive triple. Given a list of points [a, b, c, d, e], it should start by calculating the turn of [a, b, c], then [b, c, d] and then [c, d, e]. Your function should return a list of directions.
> 4.  Using the codes in the previous three exercises, the Graham scanning algorithm is implemented for a set of convex hull of 2D points. You can find a good description on Wikipedia about what convex hull is and how Graham scanning algorithm should work.

问题 9 非常简单:

```
data Direction = Left | Right | Straight 
```

问题 10 花了我一个月的时间。

我想这一定很简单。如果这是一个困难的几何问题，它就不会出现在初学者的 Haskell 书中，而是出现在“Haskell 应用于复杂的数学”书中。但是一次又一次的尝试都让我失望。我比较了坡度。我尝试了复杂的条件句，这些条件句基于当每条线的基线被设置在原点时，它位于哪个象限。

你知道 Haskell 有两个`Data.Vector`模块吗？标准库中有一个处理向量的方法，比如类似数组的 CS 概念。Hackage 上有一个是关于向量的，比如几何和二年级代数概念。它们被称为同一个东西。我现在知道了这一点，因为我找到了后者的文档，像一个好女孩一样导入了`Data.Vector`,并且在放弃基于点积的解决方案之前，花了数十亿年的主观时间来研究 arity 不匹配错误。

最终我终于解决了所有的特例。

我一直在督促自己完成，而不是减少损失，继续写这本书，因为我觉得当我最终完成时感觉会很好。它没有。我只是觉得自己很愚蠢，因为我花了一个月的时间才弄明白这本书的作者们显然期望的简单。

我接着讨论第 12 个问题。按要求在维基百科上查了格雷厄姆扫描算法。

> 再次，确定三个点构成“左转”还是“右转”不需要计算两个线段之间的实际角度，并且实际上可以仅通过简单的算术来实现。对于三个点 [![p1=(x1,y1)](img/864eb72918ca5365843fbe17e0f7ef1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qy0-KPqL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/math/e/3/8/e38e464b2ad39be72af38cacc8fc17de.png) 、[![p1=(x2,y2)](img/6f5d933f1491a1cd063821376a2a9f00.png)T5、](https://res.cloudinary.com/practicaldev/image/fetch/s--Xz6tybA0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/math/a/4/5/a45267c35535663e45d3a0bcc4db16e6.png) [![p1=(x3,y3)](img/34b54ae024cee515b956bbab9403ac34.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NU4RLRw4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/math/b/d/b/bdb6d1f831105c159498d203a95c41fb.png) ，只需计算两个向量 [![p1p2](img/36b6b827e86525392cc533acb74f113c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1WjOoppD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/math/a/c/7/ac7a0fe21575792e379eb1caa37d5224.png) 和 [![p1p3](img/55f11daf1529b6a2fe2081a470dc89d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5406PX2O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/math/2/b/5/2b5ba9e273a2f3c0de10b0a07125bd86.png) 的叉积的 z 坐标，由表达式 [![(x2 - x1)(y3 - y1) - (y2 - y1)(x3 - x1)](img/9b50729e5133c50731efc8f30b199eac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d0uL_Hwx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/math/4/4/d/44dac4ebbcdd18ed0593418004b5e4c8.png) 给出。如果结果为 0，则点共线；如果为正，则这三个点构成“左转”或逆时针方向，否则构成“右转”或顺时针方向(对于逆时针编号的点)。

读者，我看见火了。

起初，我生自己的气，因为已经有了一个直截了当的答案，而“我太笨了”没有得到。然后，我对作者没有把“简单算术”的答案放在问题 10 的正文中感到愤怒。然后我对作者大发雷霆——因为我意识到他们为什么没有这么做。

他们认为这太简单了，无法解释。他们认为任何学习 Haskell 的人都会记住高中预备微积分中包含的所有随机主题。他们认为任何学习 Haskell 的人都会是那种“自然而然”记住这类东西的人。

现在，因为这是一个女人写的，你可能会认为这是一个关于你不需要了解计算机数学的咆哮。

再想想。我就是那种人。我十二岁或八岁开始编程，这取决于你是否把 HyperCard 算在内。我九年级的时候学了 Scheme。我就是那种人，问题还是让我觉得自己是个他妈的傻逼。像个骗子。好像我应该放弃一样。

我是否记得向量叉积是不可交换的，以及这对转向问题有什么影响。

这可能是对函数式编程象牙塔的傲慢的一种咆哮。但是很多人已经大声疾呼了，我更感兴趣的是自我反省，而不是成为另一个对非 Ruby 爱好者嗤之以鼻的 Ruby 程序员。

## 离家更近

我在空闲时间教人们编程。我在 DC 共同组织了一个名为 Learn Ruby 的加入小组，所以每隔一周，我都会指导不同技能水平的人如何编码。这包括真正的初学者，以及学徒和初级人员。

他们中的每一个人在某个时候都会遇到一个理解上的差距。他们混淆了局部变量和实例变量，或者变量和字符串，或者变量和符号——确切的混淆并不重要。他们的反应是随机加减`@` s、冒号和引号，直到它起作用，然后他们松了一口气。发生这种情况是因为他们没有心理支架来区分变量和符号之间的差异，本地人和 ivars 之间的差异——对他们来说，他们只是一些单词，在他们面前可能有也可能没有魔法，所以你摆弄魔法，直到它运行。

我总是要花几分钟的时间去观察，才能明白他们脑子里在想什么。我从 2008 年开始写 Ruby 原始变量、访问器和局部变量之间的范围差异就像现在的呼吸一样。我的直觉大脑假设每个人都知道这一点，我需要支持并提醒它那不是真的。

我需要这样做，即使在 2008 年，作为一个用各种传统语言工作了 8 年的女人，我拼命地想把这种差异内在化。

我让我的学生感到愚蠢吗？我希望不是，但是…有时候我可能是。

## 我们去哪里？

我们创造的教育材料编码了我们的价值观——通常是偶然的。 *_why 的辛酸指南*反映了一种风气，即编程是一种快乐的、创造性的活动。*学你一个哈斯克尔*传达了一种世界观，即肥胖的笑话非常有趣。*现实世界中的哈斯克尔*假设所有真正受过教育的人都能不假思索地记住向量数学的复杂性。

我们教育材料的价值创造了我们的社区价值。假设与他们的相吻合的人继续进行社区参与；那些假设和价值观不会泄露的人。我认为 Haskell 是一门非常漂亮的语言，但是我不知道 Haskell 社区是否需要我。

忘记自己内化的知识不是常识是人之常情。当不好老师是人之常情。迷惑学生是人之常情；让他们感觉很糟糕。还是很残忍。当我们把这种残酷编码到我们的教育材料中时——不管多么偶然——我们把从这种残酷中幸存下来变成了我们最珍视的东西。

你选择的语言需要哪些隐性知识？在大三的时候你是怎么处理的？

* * *

如果你觉得这篇文章有帮助，我希望你订阅我公司的每月简讯！我们每个月都会分享一些我们发现的有价值的东西，以及成为更有效的程序员和工程领导者的独家见解。