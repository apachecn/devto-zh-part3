# 高效开发人员的 14 个习惯(第二部分)

> 原文：<https://dev.to/pavlosisaris/the-14-habits-of-highly-effective-developers-part-2-2io0>

本文原载于[paulisaris.com](https://paulisaris.com/the-14-habits-of-highly-effective-developers-part-2/)

## 简介

这是“高效开发人员的 14 个习惯”系列的第二部分。你也可以在[我的博客](https://paulisaris.com/the-14-habits-of-highly-effective-developers-part-1/)中阅读第一部分，或者在 dev.to:

[![pavlosisaris image](img/645ccd0d16c2a405ceec6feb0688bc57.png)](/pavlosisaris) [## 高效开发人员的 14 个习惯(第 1 部分)

### 保罗伊萨里斯 2 月 10 日 1915 分钟阅读

#beginners #career #productivity #motivation](/pavlosisaris/how-to-transition-from-junior-to-mid-level-developer-part-1-4gig)

在“高效开发人员的 14 个习惯”系列的第[部分](https://dev.to/pavlosisaris/how-to-transition-from-junior-to-mid-level-developer-part-1-4gig)中，我们开始展示 ***习惯*** 在开发人员日常生活中的力量，以及我们如何从将简单而关键的改变融入我们的工作中获益，旨在获得最大收益并帮助提升我们作为软件工程师的职业水平。

习惯造就了我们。我们就是我们的习惯，尽可能地养成它们是我们的责任。当某个与工作或学习相关的习惯很强时，我们可以有效地对抗阻碍我们的事情和情况，比如**拖延**、**倦怠**、**厌倦**，以及偶尔在前后放松**的冲动。**

> 如果你想在大事上取得卓越，就要在小事上养成习惯。优秀不是例外，而是一种普遍的态度。
> **科林·鲍威尔**
> 
> 我们就是我们反复做的事情。因此，优秀不是一种行为，而是一种习惯。
> **威尔·杜兰特**
> 
> 美德是习惯的结果。我们通过做正义的行为变得正义，通过做温和的行为变得温和，通过做勇敢的行为变得勇敢。
> **亚里斯多德**

事不宜迟，让我们继续深入 14 个习惯，它们将永远改变一个人的职业生涯，并让你成为一名**高效的开发人员**:

## 8。习惯你的自尊心受到伤害(从好的方面来说)

我们开发人员是一群奇怪的人。我们谈论我们只理解的事情，以奇怪的方式使上下文更加不可理解。在某些时候，我们的工作会让客户或产品/项目经理不高兴，这是合乎逻辑的。某个功能可能没有正确实现，或者项目需求中可能存在歧义。这就是我们骄傲的时候；

*肯定是**不是我的错！我怎么会犯这种错误？他们是白痴吗？他们个人讨厌我吗？为什么他们不理解重新实现一个特性的困难？***

 **所有这些想法对所有软件工程师来说都是常见的，只是作为一个糟糕的顾问，烦扰我们不要接受问题，坚持我们的骄傲。重要的是要明白(可能)没有人对你或你的项目有任何私人的想法。如果有误解，就去把事情说清楚。如果有一个 bug，一定要记录下来，修复并测试它。这就是务实和专业的软件工程师所做的。不要坚持你的骄傲，甚至不要让你的[骗子综合症](https://en.wikipedia.org/wiki/Impostor_syndrome)发作。

> 你的工作不仅仅是成为一名优秀的分析师、程序员和技术人员，还要成为一名优秀的专业人士。
> 拥有出色的软技能可以在你出色的技术技能无法帮到你的时候帮到你。

## 9。让露营地比你发现时更干净

著名的童子军规则。这实际上适用于我们日常生活的方方面面，所以软件开发也不例外。
很多时候，我们需要扩展代码库的功能并添加新功能。我们建立了开发环境，从版本控制中取出代码并打开项目，只看到一堆`TODO`未使用的方法和变量，硬编码的数字和字符串值以及未使用的依赖项。

这是我们坐下来思考是否应该做一点清理工作的时候，因为我们已经在这个项目中工作了。但是，另一方面，我们不确定我们是否会打破比修复更多的东西。如果我们将重命名的方法(用一个更好、更具描述性的名称)用于代码的其他部分(甚至作为依赖项用于其他项目)，该怎么办？确定项目需要的重构级别并不容易，单元和集成测试总是有助于找到代码中的断裂部分，方法范围也是如此。

> 在我们确定相应地更改了任何客户端(调用该方法的代码)之前，不应该更改公共方法。受保护的方法应该更容易更改，我们只需要检查子类的显式实现。私有方法通常是最容易的，因为它们的范围是有限的。

除了方法重命名和重构之外，一个负责任的开发人员还可以做其他事情，例如，删除注释掉的或未使用的代码，或未使用的文件。大多数专业的 ide 都有让你知道一个方法是否被使用的工具。不要害怕删除注释过的或过时的代码。过时的代码只会增加项目的技术负担，并且总是可以通过从版本控制系统中签出到更早的时间点来检索。

## 10。不要害怕参与非编码的东西

典型的软件工程师的工作是写代码。无论是在分析用户故事、写下需求还是绘制数据库草图时，我们的最终目标都是写出好的、健壮的代码，高效地完成工作。然而，做非技术性的工作，钻研软技能的神奇世界是至关重要的。与经理、测试人员和客户沟通似乎令人畏惧和疲惫，但它也有很大的价值。

建立你的软技能，更好地与人沟通和管理，这肯定会让你成为一名更好的专业人士。你将能够更好地交流用户故事，甚至解释技术细节，而不用使用严格的技术语言(让人觉得我们是外星人的那种)。

此外，能够更好地与经理和客户沟通也将有助于您在分析新的用户故事或估计新功能完成所需的时间时变得更加高效。你将更好地理解客户想要什么，因为你将能够提出更好的问题。；-)

> 不幸的是，有些人认为软技能没那么重要。然而，几乎每一个和我谈过这个问题的雇主都不同意。在一个工作角色快速变化的世界里，软技能将是为数不多的不变因素之一。

## 11。做一个文档狂

添加/删除插件和第三方库、在代码中做出假设、在设置或构建过程中增加额外的步骤或者使用特定版本的命令行工具，如果没有正确地记录下来，将来可能会非常痛苦。这条规则与第一部分中描述的“总是想着下一个开发者”规则紧密相连。您应该确保您不是唯一能够为开发设置项目或运行生产构建的开发人员。

实际上有一个非常简单的规则。完成一个项目后，将存储库克隆到另一台机器上，并尝试按照自述文件中的说明进行设置。这肯定会指出缺失的说明，然后你就能构建健壮、专业的文档。

## 12。给自己时间放松和锻炼

当然，8 小时睡眠和(尤其是)日常锻炼对于普通的开发人员来说并不常见。我们倾向于下班后偷懒，看连续剧，玩电子游戏。甚至不要让我开始清洁饮食和避免垃圾食品。事实是，健康的饮食和坚持不懈的锻炼会对你的工作情绪和表现产生巨大的影响。

当你锻炼时，你也增加了大脑的血流量，这有助于提高你的意识，让你更好地准备应对下一个大项目。保持最佳的身体健康将有助于提高你的整体工作能力。锻炼不仅可以帮助减轻体重和某些疾病的风险，还可以改善心血管健康，这将为你的日常工作提供更多的耐力。

> 当你锻炼时，你的大脑会释放血清素，帮助你感觉更好，改善你的精神状态，使工作压力更容易处理。血清素是大脑中的一种神经递质，它向身体发送信息，以刺激情绪和情感。

除了锻炼，睡个好觉会让你第二天早上感觉更好。当生活忙于繁重的工作、不规律的工作时间表、学校和养育责任时，睡眠往往是首先要放弃的事情。所有这些正常但越来越耗时的现实往往会挤出健康睡眠所需的时间和内心平静，而健康睡眠肯定会带来更好的工作记忆和整体表现。

在会议上表现得精神抖擞，不要表现得像一个通宵玩电子游戏的青少年，这肯定会让你在工作中表现得更好，也会让你在同事和客户中的形象更好。

## 13。学会脱离个人感情

这与第八条规则“习惯于自尊心受到伤害(从好的方面来说)”紧密相关。然而，重要的不仅仅是学会如何容忍你的“开发者自我”受到伤害。这条规则是关于学习如何成为一名高效的专业人士，避免在工作中为个人问题烦恼。

很难与它达成良好的关系，但是不断抱怨产品缺陷的客户并不讨厌你。审阅您的代码并发现一些设计缺陷和需要改进的技术债务问题的同事并不认为您是一个坏人，他们也不讨厌您并想揍您一顿。学会接受他人的意见(即使你个人并不同意)，做对工作和项目有益的事情。

当然，这并不意味着你应该贬低自己的个性或者接受任何人抛给你的东西。你有不可否认的权利去争论，但是永远记住这个争论是如何尽可能富有成效的。你只是为了争论而争论，为了捍卫你的自尊，还是你真的有什么建议可以让情况变得更好，并导致双赢的平衡...？

## 14。给出富有成效的建议

初级和高级开发人员的一个巨大区别是他们给出好的建议和进行有益的代码评审的能力。当被要求提供建议或进行代码审查时，避免考虑你看待事物的方式，而是专注于更大的图景和“公共利益”。

就像一个优秀的超级英雄一样，一个优秀的软件工程师在给出建议时应该是直接的、诚实得可笑的(当然不能粗鲁)。你看到需要重构的东西了吗？不要勉强，说出来。有人问你的**专业意见**，你将发表你的专业意见！

这里最关键的部分是，每当你要给出一条建议或指出一个缺陷或错误时，问一个迷你的内在问题:
*“我必须提出更好的建议吗？”*
提建议而不提出建议的东西即使稍微好一点也比不上抱怨。确保你有一些有益的东西添加到一个富有成效的对话中，并始终牢记大局。

> 作为一名高级软件工程师，你不仅关心自己的发展，也关心他人的进步。不要把建议留给自己，开始给别人提供富有成效的指导。你将从中受益匪浅，因为你将负责整个专业团队的成长。

## 结论

养成良好的个人和职业习惯是成为有效职业者的高速公路。推进一个人的职业生涯不是一朝一夕的事情，它需要时间，最重要的是坚持不懈。你现在的工作是尽可能多地将这些习惯应用到你的日常生活中，并开始向真正的专业人士转变！

请在下面留下你对这些习惯的评论！:-)

这篇文章发表在我的个人博客上。**