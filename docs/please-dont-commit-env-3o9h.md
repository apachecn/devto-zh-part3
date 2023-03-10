# 请不要承诺。包封/包围（动词 envelop 的简写）

> 原文：<https://dev.to/somedood/please-dont-commit-env-3o9h>

让我们面对现实吧。`.env`文件是*惊人的*。它们有一个易于阅读的语法，将我们所有的基本配置存储在一个文件中。不仅如此，作为 web 开发人员，他们保守着我们最深、最黑暗的秘密。他们确保我们宝贵的 API 密钥和数据库密码保存在本地，远离窥探的目光。在我们的代码库中扮演着如此重要的角色，社区不断提醒我们不要共享我们的`.env`文件；像政府对待他们的机密信息一样对待他们。

这在开源社区中尤其明显，在那里每个人都可以互相共享、复制和重用代码。许多人认为偶然提交和推送`.env`文件是一个值得纪念的时刻。就我个人而言，我还从未亲自做过*，但我确信我的命运就此注定。*

 *在一次聚会上，开发人员之间的随意对话中谈论“有一次你提交了*商品*”虽然很有趣，但它已经成为 web 开发中的一个常见对话，甚至可能是一个[的成人礼](https://en.wikipedia.org/wiki/Rite_of_passage)，这是非常令人担忧的。

# 犯罪史上的一个污点

[在 GitHub](https://github.com/search?q=add+env&type=Commits) 上快速搜索，发现仍有许多人没有收到备忘录。偶尔的`add .env`和`remove .env`提交标题会不时出现在搜索结果中。查看提交的内容确实显示了它们宝贵的 API 密钥和数据库密码。老实说，看到他们如何恢复他们的改变，就像一个孩子如何因为做了他们不应该做的事情而感到内疚，这很有趣。

更令人担忧的是，还有一些人没有恢复他们的提交。这个`.env`文件仍然在他们的存储库中存在。据我们所知，这些可能是真实的、实际的 API 密钥和数据库密码，它们目前经常依赖于这些密钥和密码。更糟糕的是，[通过最近提交的](https://github.com/search?o=desc&q=add+env&s=committer-date&type=Commits)对搜索结果进行排序，显示出这些提交是多么的普遍和频繁。

简单地删除工作树中的`.env`文件的问题是，Git 保留了存储库中所有提交的记录，甚至是最早的提交。除非有更巧妙的方法(稍后将详细介绍)，否则提交这个`.env`文件将永远成为提交历史中的一个污点。这正是一个[版本控制系统](https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control)最终应该做的事情:保留变更的历史，即使是不好的变更。

话虽如此，但是 one 如何处理存储库中的敏感数据呢？

# `.gitignore`是你最好的朋友

向您的存储库中添加一个`.gitignore`文件是您抵御这些中断的第一道防线。正确而明确地指定忽略哪些文件和目录是防止敏感数据被公共存储库和窥探的可靠方法。

[GitHub](https://github.com/github/gitignore) 和 [gitignore.io](https://www.gitignore.io/) 为特定语言和环境提供通用`.gitignore`模板。大多数时候，这些模板足以满足您的需求。

在已经提交了存储库中的敏感数据的情况下，GitHub 有一个关于如何从提交历史中清除文件的指南[。然而，“过度杀戮”并不是一件坏事。谈到安全，一个人再安全也不为过。😉](https://help.github.com/articles/removing-sensitive-data-from-a-repository/)

# 我们都会犯错

我们都是人类。没有人是完美的。错误只是生活的一部分。事实上，这可能是我们每天必须面对的最重要的现实之一。如果不面对这些错误，我们永远无法成为更好的开发人员。学习的很大一部分不在于对成功的肯定，而在于对失败的“是什么”和“为什么”的认识。

当然，提交`.env`文件是一个严重的错误。当然，它会给你的应用带来严重的安全风险。当然，企业将遭受重大损失。当然，清除提交历史会很麻烦。当然，知道自己已经受到威胁，晚上会很难入睡。但是如果这次经历有什么好处的话，那就是你最终会成为一个更好、更有经验的开发人员。

也就是说，为所有你永远不会提交`.env`文件的未来项目干杯。*干杯！* 🥂 * *碰杯* *我将以一个离别的想法和对读者的最后提醒来结束这篇文章:

*请不要犯`.env`。你不会想在 [GitHub 的搜索结果](https://github.com/search?o=desc&q=add+env&s=committer-date&type=Commits)中名列前茅。**