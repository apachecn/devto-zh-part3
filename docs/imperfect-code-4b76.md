# 不完善的代码

> 原文：<https://dev.to/codingmatty/imperfect-code-4b76>

我必须承认，我很难提供代码审查。我倾向于吹毛求疵。很多。

我注意到每个人似乎都有不同的代码编写方式。有时候看到新技术真的很有趣。但通常它只会给代码库增加麻烦。随着时间的推移，当不同的开发人员进来，用他们特定的风格标记代码库，并出于任何原因离开代码库时，你会开始感觉像一个地质学家在地壳层中挖掘。

我主要审查 Javascript 代码库的代码。这是一门伟大的语言，我喜欢它。但是它可以允许一些有趣的语法，并且仍然可以像预期的那样工作。当你加上 JSX 时，一切都乱套了。

我有追求完美的倾向。但是代码并不意味着完美。代码是描述计算机应该做什么的媒介。你可以拥有世界上最好的代码，没有人会使用它，因为也许它没有做任何有用的事情。

代码的半衰期似乎越来越短。我的意思是，一旦你写了一些代码，它就开始腐烂。随着代码变得越来越少，它的用处也越来越小，尽管它的功能可能会继续存在。因为*代码*应该由**人类**来阅读，而不是机器。机器可能不太关心你的变量名是什么，或者你是否使用了类型化语言。他们会完全按照你说的去做*。不多也不少。*

如果你曾经在任何你认为是“遗留”的系统上工作过，你可能会明白我的意思。代码库越大，有人进去清理它的可能性就越小。当有人不得不添加或修复一个特性时，他们可能会尽可能少地更新代码。

我有一个同事最近告诉我，他最近越来越不倾向于编写新代码。这是因为他已经开始意识到，你写的代码越多，产生的 bug 就越多。实际上，他开始将代码视为一种负债，而不是资产。

[![XKCD Comic](img/6512dd411bdb475339ee548bceb0bf1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3TqaQTU---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgs.xkcd.com/comics/fixing_problems.png)

* * *

我记得当我第一次开始过渡到 web 开发的时候。我开始自学 Angular。我学会了所有正确的写 Angular 的方法，并内化了最佳实践。我甚至开始了一个相当雄心勃勃的副业项目，以适应与我想要的技术一起工作，并进入正确的公司。当我被一家使用 Angular 的公司录用时，我非常兴奋！

我进来的时候没有真正的 web 开发经验。我带着对我棱角分明的知识的很高期望进来的。直到我看了代码库。我迷失了🙈。部分原因是我缺乏经验，但另一部分原因是他们如何构建他们的代码库。我开始欣赏的是*而不是*使用最佳实践。当我学习 Angular 的时候，我认为什么都不应该发生——你也可以把事情放在全局范围内😱。这显然不是一种共同的情绪。这只是这个巨大难题的一小部分。

这是我真正了解到代码不是在真空中编写的。每个人在编写和构建代码库时都有不同的经历和动机。其实这是我今天还在学习的东西。

* * *

让我们回到我的代码审查斗争。我如何学会处理自己的问题，并提供有价值的反馈，而不是吹毛求疵？

1.  我意识到代码没有我们试图提供的功能或特性重要。

    当我开始审查代码时，我首先会问它是否做了我们期望它做的事情，是否有测试来验证这一点？有什么可能的副作用吗？如果代码做了它应该做的事情，并且没有可预见的副作用，那么我将放弃任何风格细节或逻辑顺序。大多数时候，如果我们“提前退出”并不重要，除非我们会做大量的计算工作。有时我可能会添加一些关于可读性的评论，但我会给它一个勾号，然后继续。

2.  尽量先发制人解决问题。

    有人曾经告诉我，最在乎的人会写下规则。在我的例子中，我创建了一个编码风格指南，其中有一些基于我之前所做的代码评审的初始指导方针。然后，我得到了一些反馈，并让其他开发人员添加他们自己的风格偏好(如果有的话)。关键是让每个人都同意获得认同的指导方针。这是一个活的文档，可以随着我们决定更好的实践而发展。但它的最佳目的是有所指向，以说明我们作为一个团队决定如何为特定的代码库编写代码。这也是让新开发人员加入团队的一个很好的资源。

3.  有时我仍然只是吹毛求疵。

    我不再把吹毛求疵的事情视为障碍。我认为这是表达我的观点的一种方式——想想建设性的批评。有时候这对其他开发人员来说是一次学习经历，但是我承认有时候这可能是一个障碍。我学会了不要对那些表示他们只想要可操作的反馈的开发人员这样做。如果我真的那么在乎，我会跟在开发商后面清理干净。但是我没有时间这么做，任何人都不应该这么做——这损害了开发者的利益，破坏了信任。

* * *

那你呢？你对完美主义有意见吗？作为一个吹毛求疵的人还是挑剔的人？让我知道你是如何处理的？我也乐于接受如何做得更好的建议。