# 使用拉请求提高代码质量

> 原文：<https://dev.to/arminaskatilius/improving-code-quality-using-pull-requests-2e9i>

*最初发布于公司[博客](https://www.devbridge.com/articles/improving-code-quality-using-pull-requests/)T3*

拉请求有点像编写自动化测试。首先，它们的有用性很难衡量，因为每个项目都是不同的。同时使用两者成为行业标准。然而，仍然有许多怀疑论者。他们认为测试/拉取请求会减缓开发人员更快地交付功能。最后，如果只是为了使用而使用，两者都失去了意义。

另一件有趣的事情是，除了它们的主要目的之外，测试和拉请求是如何改进其他方面的。例如，测试，尤其是 TDD 可以帮助提高代码质量和设计。在这篇文章中，我将尝试提供拉请求的良好实践。我也会试着提及他们如何改善其他领域。

在开始之前，人们可能会问为什么要做拉请求。我不会深入讨论它，每个团队可以决定为什么拉请求是重要的，或者为什么它们是不重要的。然而，这些是拉请求有用的地方:

*   给作者信心，代码对合并来说足够好
*   分享知识和理解
*   让我们不那么懒惰
*   注意 bug

# 如何更好地准备拉式请求

## 保持小规模

这是 PRs 最常见的问题之一，它会影响使用拉请求时的整体体验。当有大量文件时，很难理解变化并注意到问题。根据团队成员的不同，这通常会导致两种情况:

审阅者只是浏览文件并批准而不阅读
审阅拖拖拉拉，因为它会引起多次讨论，作者必须修改大量文件
有不同的技术来减少 PR 的大小。你能做什么取决于项目的类型和发布周期。您可以首先引入一个部分工作的解决方案，然后通过后续的拉请求来扩展它。例如，如果您需要实现一个带有过滤和排序的数据表，您可以从一个表开始，然后添加带有其他拉请求的排序，最后是过滤。或者，如果项目只有一个存储库，那么可以先引入 API 变化，然后再引入 UI。

如果您不想将部分工作功能合并到一个公共分支中，还有其他技术。您可以在特性分支中引入拉请求。使用这种技术，您需要从您的主特性分支创建分支，并创建对它的拉请求。这确实增加了开销，也增加了对分支的处理，但是它可能对你有用。

还有其他方法可以把一个大问题分解成小问题，这取决于每个人如何处理它。但是通过把它分成小块，你可以同时提高你解决问题的能力。反过来，这有助于任务评估和其他领域，因为即使在编码之前，您也可以将任务分成更容易理解的部分。

总的来说，考虑大的拉取请求是否是太大的任务/故事的征兆。如果任务足够小，很少需要所有这些技术。

## 用单独的拉请求进行重构

这与上一篇关于小型拉请求的技巧紧密相关，但是我想提到它，因为即使不使用拉请求，这也是一种很好的开发技术。总的规则是:*“先重构，使添加新功能变得容易，然后才添加新功能”*。这是一项伟大的技术，因为你允许新的功能引导系统的设计，而不是过度工程化。

如果您遵循这个规则，您可以创建更简单的 pull 请求供您的团队审阅。这就增加了很多好处。它会自动将巨大的拉取请求分割成几个较小的请求。同时，如果重构影响了很多文件，你应该尽早引入它们。我并不总是遵循这条规则，这经常会导致痛苦的合并冲突，因为其他团队成员正在编辑相同的文件。

这种方法的唯一缺点可能是，如果您将重构紧密地引入到正在实现的新功能中，由于某种原因，该功能不再被添加到代码库中，而您只剩下重构的代码，并为不需要的功能做准备。

## 明智地使用 git 提交消息

另一个适用于拉请求的通用编程技巧是提交消息。它记录了拉请求中的代码和所有更改。请这样想:提交消息将记录您所做的更改，而不是创建描述。在拉取请求描述中，您可以写入其他相关信息。作为一个副作用，我真的很喜欢我从同事那里听到的描述:*“写有意义的提交消息，让你更好地组织任务，并将你的工作分成有意义的部分。”*

## 自己注释代码

如果你对一个特性的实现有疑问，最好在开始编码前讨论，不要在拉请求中讨论。但是有时候你不确定的事情可能不那么重要。例如-命名方法或类。你可以在拉取请求中提及，有人可能会建议你更喜欢的另一个名字。

另一种帮助评审者的方法是对代码中重要的地方进行注释，甚至解释你为什么要做这些事情。您可以使用它来引导审阅者了解您所做的更改。

## 用 PRs 引入新思想

正如我前面提到的，在开始编码之前最好先讨论一些事情。但有时您可能会遇到这种情况，在没有看到实际代码的情况下，很难想象建议的解决方案是否可行。在这种情况下，您可以创建概念证明解决方案，并为其创建一个拉取请求。不一定要合并，但是 PR 评论页面是讨论解决方案的好地方。

# 如何更好地审核拉动式请求:

## 不要盲目批准采购申请

如果您正在处理拉取请求，最糟糕的做法之一就是。如果多个开发人员没有阅读就批准了请求，这意味着您实际上并没有进行代码审查和拉式请求，您只是在制造一种假象。

在开始的时候，我提到过，拉请求过程的一个品质是建立信心，相信你的代码足够好来合并。您之所以有这种信心，是因为您知道其他开发人员根据代码审查了需求，并检查了可读性。合理大小的拉取请求在创建后 5 秒钟被批准，您就有问题了。

如果您当时很忙，最好将拉式请求留给其他人审阅，稍后再回来处理。我宁愿等待更长的时间来等待我的拉取请求被批准，但至少这样我会知道有人在调查它。

## 有礼貌，先问

有时你不知道为什么某些代码是这样写的，你可能会告诉某人去解决某个问题。有时候，礼貌的做法是先提出问题，然后提出改进建议。比如:为什么你迭代了 list 四次而不是一次？

当你真正熟悉代码时，继续提出改进建议。但是不要忘记要有礼貌。传达相同信息的方式有所不同:

将这个方法分成三个更小的部分对
T3】如果你将这个方法分成三个更小的部分，会增加可读性。要记住的一件事是，阅读你评论的人看不到你的面部表情。你的建议可能是出于好意，但一旦变成文字，它可能会显得刻薄或粗鲁。我也遇到过很多情况，我只想简洁明了，但是收到建议的人对此并不高兴。

## 使用代码片段

当我开始做代码评审的时候，我经常写一些类似于*“这可以简化”*，*，“你可以在这里使用 lodash”*等等的东西。但是作者不确定我的意思或者不知道如何实现这个建议。渐渐地，我用代码片段代替了一般的消息。这确实需要更多的时间，但同时，将当前代码与建议的改进进行比较，看看是否值得做，也容易得多。此外，片段可能不是 100%准确，主要目标是更清楚地向作者传达信息。

需要记住的是，这个建议并不是一个硬性规定。如果你觉得代码的某个部分难以阅读，你可以用不同的方式来表达。有时仅仅陈述对复杂性的关注就足够了。这可能有助于作者注意到问题并自己改进代码。

## 即使是新手也要复习代码

新团队成员通常不确定他们可以审查谁的拉动式请求，甚至不知道他们什么时候可以审查 PRs。当一个人可以*“批准”*拉式请求时，每个团队可以有不同的协议，但是你应该马上开始阅读甚至评论拉式请求。您甚至可以为作者添加关于项目中编码的一般方法的问题，并学习最佳实践。

# 如何改善整体拉动式请求流程

## 利用静态分析工具

评审人员倾向于注意的一件事是代码风格。它可能是多个空行、未使用的变量和函数等。拉请求有助于协调开发人员之间不同风格的代码。这并不意味着一种风格比另一种更好，但是对于每个文件来说，在代码库中保持一致的风格比随机的风格要好。但是，对其使用拉请求会有多个问题。一个是开发者也只是人，不会一直注意到这些问题。

另一个原因是关于代码风格的评论可能需要大量的拉式请求评审。它们当然很容易修复，但是当有十条关于风格的评论和一条关于可读性的评论时，最重要的就很容易丢失了。

总的来说，我不是说不值得在公关中提及风格问题，但是如果你经常注意到某些问题，考虑使用林挺工具在拉请求之前注意到这些问题。如果机器人能为你做事情，甚至做得更好，为什么要自己做呢？此外，当机器人注意到问题时也更容易——你不会生气。

## 审批前有一个要求事项清单

每个开发人员决定一个拉请求是否可以被合并。但是如果评审者很顽固，他可以避免批准拉式请求，即使它没有什么大问题。当你开始注意到这种行为时，讨论并同意代码的共同价值。然后以价值为基础，决定批准拉动式请求所需的条件。例如，如果测试很重要，制定一个规则，bug 修复需要一个新的测试或现有的测试修复。在批准前列出必要事项确实有助于新团队成员，并减少 PR 未被批准时的讨论次数。

## 尽早停止大量的讨论

程序员热爱他们的代码。想象一下，你在某个特定的算法上工作了一整天，有人开始剖析它，并告诉你用不同的方式做事情。更糟糕的是，评论者可能对此并不特别客气。他可能是错的，也可能是对的，但关键是这可能会引起长时间的讨论，在这里你可以交换为什么这样或那样的方式更好的论点。即使谈话是礼貌的，你也应该在它变得太长之前停止，并就此进行一次现场讨论。有时去其他开发人员的办公桌前聊天就足够了，有时您可能需要安排一次会议。

## 游戏化过程

当你发现让开发人员审查拉请求有困难时，不要立即添加规则，试着给这个过程增加一些乐趣。如果你有一个显示统计数据的电视，添加一个新的部分来显示一些关于拉取请求的信息。例如，您可以显示等待很长时间且未被审查的请求。此外，你可以有像*“本月添加评论最多的评论者”*或其他有趣的统计数据，这将鼓励人们致力于评论。有许多其他创造性的方法来改进过程并鼓励开发人员参与。