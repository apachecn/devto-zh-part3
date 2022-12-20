# 调和居伊·德波:语法第一人称编码

> 原文：<https://dev.to/samosborn/reconciling-guy-debord-coding-in-grammatical-first-person-26g5>

在现代生产条件盛行的社会里，生活呈现为一大堆壮观的景象。一切直接存在的东西都退居到了表象中。

* * *

这种奇观不能被理解为仅仅是大众传媒技术产生的视觉过剩。这是一种实际上已经具体化的世界观，已经成为一种客观现实。

* * *

经济统治社会生活的第一阶段带来了明显的退化，从*成为*到*拥有*——人类的满足不再等同于一个人是什么，而是等同于一个人拥有什么。现阶段，社会生活已经完全被经济的积累产品所占据，正在带来一种普遍的转变，即从有的*到出现*的*...*

居伊·德波*奇观协会*

* * *

# 软件在奇观中的寓意

尽管《奇观社会》写于 60 年代，但它可能是对社交媒体、流行文化、肤浅的破坏性漩涡以及令人沮丧地广泛存在的事实紧缩的最清晰的批评之一，这在一定程度上要归功于软件。德波花了整个社会为*奇观*下了一个更加精炼的定义:在商品经济中表象本身的实现和物化。批评是广泛而严厉的，但前提是在一个表象是经济目标和经济副产品的社会中，这个系统变得完全同义反复。社会的手段和目的是完全相同的，所以代理生活和物化生活消失在商品代表主义中。

德博特别不稳定的不是他的论文，这是一个非常优雅的关于社会媒体/大众媒体如何使人类生活经验问题化的批判——一个在我们的政治和文化伪反乌托邦中不需要努力推进的论文。局部和局部不稳定的是，德波批评的传统与更大的技术世界的精神和文化密切相关。如果德波的对手是奇观，那么软件就是它的现代催化剂，而我们，设计师，是“不生产自己”，而是“生产一种独立于自己的力量”的工人。这部电影的*成功*，以及由此产生的富足，被制片人视为*剥夺的富足*

也就是说:我们努力去表现代码，但是既没有被代码包含，也没有被代码定义。当我们将工作部署到生产中时，它就变成了“先进经济部门的一个方面，这个部门直接创造了越来越多的图像对象，这种景象是当今社会的主导产品”。毫不夸张地说，这种景象生长在我们的背上。

# 玩弄和解

作为一名软件开发人员，与 Debord 一起工作，他写的代码对他的批评很敏感，这个话题真的很重要。两者可能是不可调和的。但是，我喜欢写代码，我喜欢思考，所以这里有一个好玩的尝试。

我认为，关键是解开德波在他的第 17 篇论文中描绘的进展:我们已经从*成为*到*拥有*到*出现*。毫无疑问，表示是编码的关键部分。我们一直在创造面向对象的概念、商品和图像对象的表示。我的挑战是倒回到简单的*是*，这里是在禅宗和大规模功能障碍之间振荡的短路时刻:第一人称中的**代码。**

这是《长生不老药》中一个愚蠢的例子。我发现，如果一个人真的想写第一人称的代码，最好从功能上来写:

```
defmodule I do
  def am_a_list_with_a_max([head|tail]) do
    _and_I_sometimes_am(tail, head)
  end
  defp _and_I_sometimes_am([], my_maximimum), do: my_maximimum
  defp _and_I_sometimes_am([head|tail], maybe_my_maximum) when head > maybe_my_maximum do
    _and_I_sometimes_am(tail,head)
  end
  defp _and_I_sometimes_am([head|tail], maybe_my_maximum) when head <= maybe_my_maximum  do
    _and_I_sometimes_am(tail, maybe_my_maximum)
  end
end 
```

```
iex(1)> I.am_a_list_with_a_max([1,2,34,54,65,21,45,23,67,43,12])
67 
```

以第一人称编写代码在几个重要方面面临德波的批评，所有这些都足够有趣，我认为它值得有趣的考虑:

1.  **具体化代码**:这听起来可能很奇怪，但是这里的含义和邀请是代码作者就是代码。代码编写者对数据做的事情就是代码。这不再是一个表现的游戏，而是一个存在和体现的游戏。也就是说，你写好代码的能力不是基于你能多好地在你的系统中表现对象，而是基于你能多好地像你的系统中的对象。在上面的例子中，我就像一个列表，寻找我的最大值。这一点很重要，因为它消除了外在化景象的物质化。道德后果应该是立竿见影的:如果代码迫使程序员以第一人称生活在其中，谷歌和脸书的隐私政策会改变吗？

2.  重新安置被剥夺权利的无产阶级:工人们完全被剥夺了自己的劳动产品。作为软件开发人员，我们的工作是深入思考一件事情，然后将这种思想结构从我们身上剥离，并以一种自我维持的方式捕捉它。这听起来很关键，但这种练习确实是编程的吸引力，也是我喜欢它的原因。事实是，将思维分解成一个壮观的自主图像对象，正是德波所批判的概念。这也让我们所有人都容易受到各种方式的脱节和征服。智力劳动变成商品的过程(你每小时的工资是多少？)，让我们，工人，被剥夺了我们的智力资本，并被匿名交换。以第一人称写作并不能完全避免这一点，但是语法提供了对匿名表示的防御，这就是“我”这个词。

3.  视角的模糊性:当阅读和编写第一人称代码时，一些真正有趣的事情发生了:在不同的说话角色之间出现了模糊性。有“我”这个代码编写人，这绝对是本意。但是以一种略带诗意的方式，代码开始以第一人称向世界说话。自记录代码风靡一时。

# 结论和邀请

第一人称代码的重要后果是它围绕模块“我”运行。在很多方面，“我”既是代码编写者的声音，也是代码的结构核心。如果你对“我”有什么想法，我很想听听。“我性”当然是一个深刻的认识论焦点，也是代码语法迄今为止所缺少的东西。也许它需要代表比一个模更大的东西，不管这个模的阶数有多高？对此明显的结论是某种面向第一人称的语言，尽管现在我最感兴趣的是探索现有语言中的第一人称。试试看:在禅宗和与我混淆功能障碍之间摇摆。