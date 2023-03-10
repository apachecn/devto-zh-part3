# 这不太像数据仓库——反对优雅的 SQL 代码的理由

> 原文：<https://dev.to/alexantra/thats-not-very-data-warehouse---an-argument-against-elegant-sql-code-4ikj>

我的职责是为客户创建一次性报告。

最近，我让我的代码接受了同行评审(正如我们对所有代码所做的那样)，它获得了批准。

太好了！竖起两个大拇指...它做它设定要做的事情，不会对客户撒谎。

然而，同行评审提出了一些建议。我的方法不是很“数据仓库”,也不是在高级应用中使用的方法。

现在，我不是在这里对那个人主动提出的建议写一个严厉的(和懦弱的)回应。我是来挑战这种建议的文化的。

在过去的几年里，我训练了四个不同的人使用 SQL，出于某种未知的原因，他们中的每一个人都自然而然地倾向于同一个错误观点，即:越多的连接、聚合、函数、CTE 和子查询，它们就能以最少的代码量成为越好的编码器。他们认为优雅的代码是最好的代码。

这就是他们批评我的方法时的意思，它并不优雅，我认为那是一派胡言。

我不确定每个人如何以同样的心态结束，但我知道为什么优雅的代码是错误的。

简单地说，我不是为我自己写 SQL 脚本，我是为我的测试人员和/或我的团队成员写的，他们以后必须阅读它(无论出于什么原因)。

为了做到这一点，我保持简单。

我写了很多笔记，把脚本分成小块，试图一次只做一两件事。

我这样做是为了让另一个人可以很容易地理解这个脚本在做什么，对于一个测试人员来说，这样做很容易理解，测试点也很明显。如果另一个编码者被赋予了增强代码的任务，或者想要提取代码块用于他们自己的目的，他们可以很容易地做到。他们不必为了理解而对核弹头进行逆向工程。

有时候，我的代码非常优雅，以至于一个简单的添加需要几个小时的重组。

是的，在步骤 1 中添加一个额外的列有利于步骤 7，我对此没有问题，只要它被正确地注明，但是当您可以向步骤 1 添加有利于步骤 3、8、9 和 11 的位时，这对第三方来说就变得太复杂了，很难快速理解，如果您的测试人员努力理解您的代码，他们遗漏某些东西的风险就会增加。

我做的另一件事是通过不做太多的连接、联合或 CTE 来降低复杂性。每次你做这样的事情时，你都会增加或减少你的行数，做得太多，你就失去了 100%肯定地说你正在精确地丢失或获得行的能力，尤其是在处理大型数据集时。

一个好的测试人员会尝试验证每个连接都在做它应该做的事情，通过对一个源数据集做六个连接意味着你的测试人员必须做更多繁重的工作来确保它做了它应该做的事情。

众所周知，一个错误的连接丢失了 10 万行数据，但是通过在末尾进行简单的 count(*)可能看不出来，而且代码的复杂性降低了测试人员发现这一点的几率。

这就是我编码的方式，它有益于我的团队和我的测试人员，虽然我们总是会找到改进的方法，但我不会因为它看起来不优雅或事后看来它可能只有一半的长度而偏离以上几点。你总是可以回顾代码并使其变得更好，但是仅仅因为你可以重写代码，并不一定意味着你应该重写，这对于我的团队来说肯定不是一个有效的观点，我们的目标是更快、更安全地获得结果。

更不用说:优雅的代码并不重要。

代码应该是准确的，而不是不必要的缓慢。优雅是主观的，人类主观的，从根本上说，SQL 编译器并不关心它是否被要求在一行中做 12 个简单的语句，或者你是否已经将所有 12 个步骤加入到一个“优雅的”大型查询中。它不在乎。

这就是我想要挑战的。编写为你的团队目标服务的代码，不要努力使你的代码优雅，如果这会使测试人员的工作更加困难，不要重写它使它变得更小，也不要为你而写。你已经知道你在做什么，为什么，但是没有人能读懂你的想法，他们只能读懂剧本和门票，如果这不能帮助他们理解，那不是他们的失败，而是你的失败。