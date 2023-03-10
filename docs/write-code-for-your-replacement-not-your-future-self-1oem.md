# 为你的替代者写代码，而不是你未来的自己

> 原文：<https://dev.to/jdforsythe/write-code-for-your-replacement-not-your-future-self-1oem>

你肯定读过许多关于可维护代码的文章。他们中的大多数人都指出，你将不得不在将来的某个时候回来阅读你自己的代码，所以你应该以一种你能够理解的方式编写你的代码。这是错误的。

为未来的自己或其他当前团队成员编写代码的问题是，你将在未来用一些领域知识来解决这个问题。你可能已经离开代码几个月或几年了，但是你还没有失去你第一次着手解决问题时所拥有的知识。以这种方式思考将会导致你忽略那些你认为每个从事代码工作的人都已经理解的关键细节。

相反，你应该在编写代码、文档和设计文档时，脑子里要有这样的想法:你将不再在公司工作，或者至少会得到提升，下一个接触代码的人将是一个完全没有领域知识的新人。您的替代者应该能够通过设计文档理解概念，并通过您的代码和文档理解逻辑，而不必打电话给您询问任何问题。

这会让你写出更好的代码和更好的注释。你的代码注释应该限于解释逻辑的*为什么*(应该发生什么)，代码应该表达关于*如何*的内容。例外情况是，当不可避免地要编写一些难以解析的代码时(这种情况*会发生*)，在这种情况下，除了普通的“应该发生什么”注释之外，你应该编写完整的代码注释来解释为什么你选择这样编码。

请记住，可维护的代码并不意味着您可以维护的代码。如果你的目标是在你的职业生涯中前进，那么你希望将来不要成为维护这一准则的人。总是这样写，就好像你明天不会在那里，你以后会感谢你自己。