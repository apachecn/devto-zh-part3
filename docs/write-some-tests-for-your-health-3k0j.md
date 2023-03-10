# 写一些测试。为了你的健康！

> 原文：<https://dev.to/gmeluski/write-some-tests-for-your-health-3k0j>

你好。我来告诉你一些你可能不想听的事。但这是我作为一个碰巧在技术领域工作的有主见的人的角色(或者说我从小就相信有主见是好的，但我认为这取决于具体情况)。

你应该写测试。我的天啊。我知道，你以前都听过了，对吧？通常情况下，它是作为对更大利益的某种义务出现的，或者这就是好的开发人员所做的。这些“好”开发者是谁？迂腐！谁有时间听这些废话！

最重要的是，你应该为自己而做。因为你想要一个更健康的职业生活，你想要建立你的技术印章，你想要你的队友能够理解你所做的贡献，而不需要每次解释你的理解和实现的一些方面。听起来是不是很棒？

## 了解你的代码库和选择的语言

这是采用测试精神的一个非常实际的驱动因素。构建测试并让代码运行经常迫使我深入工作的技术细节，并使我能够从不同的角度接近预期的结果。它迫使我将较大的方法分解成较小的方法，在这个过程中，我发现自己理解现有的代码库的方式，是我不会直接跳进去并在其上构建的。我注意到通过测试使我能够以更高的确定性重构和重用现有代码。如果测试中断了，我可以恢复它，回去找出我哪里出错了。

测试使最终目标概念化，并为现有语言功能的利用创造空间。它将最终目标保持在适当的位置，让作者自由地去发现，嘿，语言本身是否支持我们的目标？如果没有，有没有现成的库，或者预先设计好的解决方案？

或者可能值得尝试集成一个我一直听说并打算探索的框架特性。无论选择什么路径，测试都提供了一致的反馈，即所尝试的是否使代码更接近目标。即使通过了测试，作者也可以自由地尝试新事物，看看它是否能通过测试，并探索它是如何造成这种破坏的。

## 将自己内心的想法和过程外化

太棒了。有测试，我们让他们通过了。根据测试框架，现在您的同事可以(轻松地？)阅读测试，理解这段代码到底要做什么。没有一个测试能够给予完全的理解，但是测试的缺失剥夺了第三方将代码放入上下文的另一个途径。这在 PR 阶段和向代码添加修复或特性时都很有价值。

通过写出测试，你也向自己解释了自己的逻辑，并给了自己一个修改的机会。就像一本日记。而是一份以工作为导向的技术性极强的期刊。写出这些测试也将与你的一般写作技巧联系起来。如果你能通过测试描述的魔力来表达你自己，那么你的通用交流工具集就越来越强大了。

## 更加自信，如果可能的话，开心地工作

作为一个公司里资历较高的人，我会陷入一个问题或一段现有的代码，我必须用它来建立理解感。可能其他高级开发人员或代码所有者正忙于其他项目，所以这变得更有压力，因为很容易破坏您没有完全掌握的代码。

如果有人为我做了构建测试的工作，我可以运行它们，进行更改并再次运行。如果它坏了，我可以很容易地恢复。这真的使我能够专注于我需要做出的改变，而不用试图理解所有的副作用——当测试中断时，我知道我需要做一些探索。

如果没有测试，通过利用现有的代码，然后进行修改，我会对这种情况感觉好很多。看到了吗？我已经没那么大压力了。

当它是一个新问题时，构建测试也有助于创建一个心理支架。测试有助于围绕正在发生的事情建立小块的理解，并且卸载经常伴随着逻辑设计而来的精神杂耍。当某样东西被适当利用时，它突然不再需要驻留在大脑中，大脑现在可以自由地思考其他事情。大多数时候，这将是更多的编码概念，但最好是阅读，理解和具体化概念，以供以后参考，而不是试图把它们都记在大脑中的令人厌倦的过程。

这就是了。所以写一些测试。或者不要。因为真的，是为了长远的帮助自己！

*最初发表于[meluski.com](https://meluski.com/engineering/2018/12/04/do-some-tests.html)T3】*