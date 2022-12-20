# 关于商业和资源伦理人工智能的思考

> 原文：<https://dev.to/documentednerd/musings-on-ethical-ai-for-business-and-resources-to-help-47g1>

当我还是个孩子的时候，我最喜欢的电影之一是侏罗纪公园，因为嗯…恐龙。我记得那年夏天这部电影也是一个现象，到处都是衬衫和玩具。我甚至记得去社区游泳池，看到到处都是成年人拿着银色封面的书，上面有霸王龙的头骨。

这确实是一部超越时代的电影，不仅仅是在特效方面，也不仅仅是它如何涵盖了克隆的主题，而是因为它描述了我们都在朝着许多人还没有完全看到的社会关系前进。电影中我最喜欢的一个瞬间是杰夫·高布伦饰演的刚从霸王龙的袭击中幸存下来的角色说出的这句台词:

[![See the source image](img/87c02a24858089bec3e73da385f96fc9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xRc3aips--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.bing.com/th%3Fid%3DOIP.9YYYEIe-qRc3FIUMcm1eGQHaEC%26pid%3DApi%26rs%3D1%26p%3D0)

技术发展突飞猛进，以至于现在许多人认为摩尔定律已经过时。我们在生活的各个主要领域都取得了进步，以至于我们的孩子完全认不出我们成长的世界。此外，就人工智能而言，这个问题在今天变得更加重要。

要明确的是，这些是一个开发者/架构师(我)对这个问题的想法，我建议你认真研究一下，然后得出你自己的结论，但这些是我的观点，而且仅仅是我的观点。

我们已经到了一个时期，越来越多的企业和社会普遍将人工智能视为解决许多问题的潜在解决方案，越来越多的人工智能伦理问题变得普遍。但这实际上意味着什么，以及一个组织如何才能建立起有利于全人类的人工智能解决方案，而不是造成意想不到的问题并潜在地伤害社会成员。

这一点的第一部分归结为一个认识，即人工智能解决方案需要充分烘焙，需要非常小心地支持减轻训练数据和服务最终结果中的内置偏差的想法。现在的问题是我所说的偏见是什么意思。我的意思是积极寻找潜在的不良假设，这些假设可能会进入基于训练数据集的模型。让我们举一个对我来说切中要害的假设案例。

如果你想建立一个系统来识别肺炎高危患者。这是几个月前我和一位同事谈论的一个假设。如果你对他们的状况进行训练，并以此作为他们最终是否会得肺炎的指标，这似乎是解决问题的一种合理方式。

但是，基于许多像我一样的哮喘患者倾向于寻求积极治疗的事实，可能会出现潜在的偏见，因为我们处于高风险中，并且许多医生非常积极地治疗感冒。主要是因为当我们得肺炎时，它会危及生命。因此，如果你不考虑这种偏见，它可能会扭曲任何人工智能系统的结果。因为你可能不会在你的训练数据中看到很多哮喘患者实际上得了肺炎。

或者，另一个潜在的考虑因素可能是位置，如果我只从亚利桑那州这样的西南部获取数据样本，干燥的气候往往更适合患有呼吸系统疾病的人，他们患肺炎的风险可能更低。

我的观点是，如何收集数据和创建训练数据集是需要大量思考和关注才能确保成功的事情。

另一个主要问题是，每个人工智能系统在一个坏结果的影响方面都是独特的。在上述情况下，就网飞的推荐引擎而言，这意味着我错过了一部我可能喜欢的电影。非常不同的结果和对生活的影响。这一点不容忽视，因为它确实是整个等式的一部分。

因此，问题变成了我们如何确保我们在用人工智能解决方案做正确的事情？答案是花时间决定作为一个组织，我们将在这些解决方案中接受什么样的核心价值观。我们需要就我们所关注的影响类型做出价值驱动的决策，并让这些价值来指导我们的技术决策。

长期以来，价值观一直是成功组织和不成功组织之间的决定性因素之一。我想到的一个例子是泰诺事件，一批泰诺被篡改了。董事会有一个选择，出于公共安全考虑，撤掉市场上所有的泰诺，伤害他们的股东，或者保护股东，否认。该公司的价值观表明，客户必须永远是第一位的，这使他们的决定很清楚。这绝对是正确的决定。我给出了一个非常简略的版本，但是这里有一个[链接](https://www.pbs.org/newshour/health/tylenol-murders-1982)到一篇关于恐慌的文章。

微软实际上发布了一个商业人工智能学校，以帮助客户获得一个解决这个问题的良好起点。他们还为各种行业制作了几个轨道，以帮助了解每个行业应该考虑的事项。微软也在公司总裁 Brad Smith 和[的博客文章](https://www.microsoft.com/en-us/AI/our-approach-to-ai)中非常清楚地表明了他们在伦理人工智能上的立场:微软人工智能

以下是一些相关培训课程的链接:

*   [人工智能技术介绍](https://docs.microsoft.com/en-us/learn/modules/introduction-to-ai-technology/)
*   [面向商业领袖的人工智能技术介绍](https://docs.microsoft.com/en-us/learn/paths/ai-technology-for-business-leaders/)
*   [定义人工智能战略以创造商业价值](https://docs.microsoft.com/en-us/learn/modules/ai-strategy-to-create-business-value/)
*   [确定企业中负责任人工智能的指导原则](https://docs.microsoft.com/en-us/learn/modules/responsible-ai-principles/)
*   [理解建立人工智能就绪文化的重要性](https://docs.microsoft.com/en-us/learn/modules/build-an-ai-ready-culture/)
*   [了解如何在销售中培养人工智能文化](https://docs.microsoft.com/en-us/learn/modules/foster-ai-ready-culture-sales/)
*   [考察微软的人工智能方法](https://docs.microsoft.com/en-us/learn/modules/microsoft-approach-to-ai/)

除此之外，人工智能领域的一些最大的高管也对此进行了大量讨论，包括塞特亚·纳德拉:

但是关于人工智能的伦理和未来，我听到的最有趣的声音之一是卡卢姆·刘升，我会告诉你看这个，因为它真正深入到挑战和方式的深度，如果人工智能没有得到负责任的处理，我们将看到人类进化中的另一个主要奇点:

这是一个复杂而多面的话题，也是周五值得深思的话题。移情是任何技术解决方案中最重要的元素，因为这些解决方案对社会的影响越来越大。