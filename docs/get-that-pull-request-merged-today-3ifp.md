# 今天就合并拉取请求。

> 原文：<https://dev.to/barrymcgee/get-that-pull-request-merged-today-3ifp>

你已经完成了艰难的工作，新的功能或错误修复现在就像你希望的那样工作——现在剩下的就是提交你的拉请求，让它着陆，每个人都获利，对吗？

没那么快。

两天、三天、四天后，当你处理重构代码、质量保证(QA)错误和合并冲突的建议时，你仍在*努力争取那个拉请求(PR)。😭*

当你提议改变时最初的成就感现在变成了一种怨恨的感觉，因为你被迫一次又一次地修改你的代码。🤬

那么，我们如何避免这种情况，并确保您的拉取请求更快、更少麻烦地到达？

1.  ## 限制你改变的范围

    您应该总是尽量保持您的拉取请求尽可能小。一次专注于固定或添加一件东西。您在 PR 中包含的变更越多，引入错误的风险就越高。

2.  ## 避免驾车路过

    “路过”是指在浏览代码时所做的更改或重构，您注意到一些明显应该更改的内容。这可能很简单，就像图像上丢失了一个`alt\`标签，或者更新了代码块的缩进。无论如何，要做出这些改变，但是要分别提出来。

3.  ## 在提出您的更改之前调整基础

    [将您的变更重新基于您提议变更的分支之上](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)将会减少合并冲突的机会，也意味着您提议的变更是针对当时存在的代码库，而不是您创建特性分支时的代码库。

4.  ## 确保您在本地运行测试

    如果项目有一个测试套件，希望它有，在提出你的变更之前，确保在本地运行那些测试。这将有助于突出变更中的错误或林挺问题。

5.  ## 吸走非阻塞问题

    通常当一个人审查你的代码时，他们会提供非常有效的建议，但并不严格阻止你提议的改变。这些可能是 UI 改进、代码抽象或对现有功能的重构。在我看来，只要你的改变没有加剧现有的问题，这些建议应该被搁置，作为独立的问题，并相应地进行优先排序。

将上述步骤融入到我自己的工作中，确实提高了我合并 PRs 的节奏。希望它也能帮助你。🔥