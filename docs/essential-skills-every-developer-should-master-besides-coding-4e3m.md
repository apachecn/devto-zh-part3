# 每个开发人员都应该掌握的基本技能(除了编码)

> 原文：<https://dev.to/jpdelimat/essential-skills-every-developer-should-master-besides-coding-4e3m>

无论你是在学习编码，还是在找新工作，或者只是想提高自己作为开发人员的技能，你都需要掌握团队协作的必备工具。这些和知道如何编码一样重要。

> 团队合作决定了软件项目的成败。

您的代码最终会工作。如果你想让它“准时”工作并且质量好，你和你的团队需要有组织。

*   每个人都需要知道他们必须做什么以及什么时候做
*   人们的工作不应该重叠或冲突
*   每个人都需要遵守共同的规则

您可以使用正确的流程和工具来实现这一点。您希望您的团队在 90%的时间里专注于编码(剩下的 10%用于休息和 Windows 更新)。

以下是你需要掌握的四个基本要素。

# 1。Git 和 Pull 请求

[配置管理](https://en.wikipedia.org/wiki/Configuration_management)是软件中团队协作的基础。有许多工具为它而存在，但幸运的是有一个已经成为绝对和最终的参考: [Git](https://git-scm.com/) 。

关于关键方面的文档在 [Git 书籍](https://git-scm.com/book/en/v2)中有很好的描述。

有大量的交钥匙服务可以开始使用: [GitHub](https://github.com/) 可能是最受欢迎的，但也有 [BitBucket](https://bitbucket.org/) 或 [GitLab](https://about.gitlab.com/) 。

一个很好的图形工具是 [SourceTree](https://www.sourcetreeapp.com/) 。不过，我建议您在使用 UI 工具之前掌握命令行。

以下是你应该知道如何回答的问题:

*   我如何合并或重定我的分支，有什么不同？
*   我如何解决冲突？例如，我的对他们的对手动合并
*   什么是[特征分支](https://bocoup.com/blog/git-workflow-walkthrough-feature-branches)？
*   什么是[拉取请求](https://yangsu.github.io/pull-request-tutorial/)？
*   Git 中有哪些不同的[协作工作流](https://www.atlassian.com/git/tutorials/comparing-workflows)？

就是这样，这很简单。在阅读完理论并进行一些实践后，你很快就会成为一名 Git 大师。

# 2。敏捷董事会

当开始一个项目或一个更大的任务时，团队需要做的第一件事是拆分工作。在过去的 10 年里，“敏捷”方法取代了传统的瀑布规划。敏捷宣言在这里是。

实际上，它说“让我们不要提前计划太多，因为我们今天对项目的投入和假设将会改变”。这几乎总是正确的，现在几乎没有任何团队不是(有时自称)“敏捷”的。

以下是你需要知道的一些实用的事情:

*   最流行的敏捷工作方式是 Scrum。将你的项目分成两周的“冲刺”，并将“任务”放在冲刺的内容中。剩下的都是为了将来，叫做“[积压](https://www.scrum.org/resources/what-is-a-product-backlog?gclid=EAIaIQobChMIrYurlr7o2AIVEhoYCh0dwQVdEAAYASAAEgIXtPD_BwE)”。这有助于跟踪进度，调整未来的计划，并随着时间的推移提高[团队的速度](https://www.mountaingoatsoftware.com/blog/know-exactly-what-velocity-means-to-your-scrum-team)。
*   另一种敏捷方法是[看板](https://en.wikipedia.org/wiki/Kanban)。这个想法是为了限制“进行中”的任务数量。这样，你就可以在进入下一个项目之前完全关闭一个项目。没有像 Scrum 那样的冲刺或时间框架。你只是一个接一个地完成任务，直到完成为止。在敏捷中，一个软件项目会被分割成几十或几百个任务。你需要一个工具来管理它们。参考工具是 [JIRA](https://www.atlassian.com/software/jira) 。

当然，还有其他工具[存在，但是在某些时候你可能不得不与 JIRA 合作。所以如果你不熟悉这些工具，就去 JIRA 吧。有一个免费的 7 天试用期。对事物如何工作有一个大概的了解就足够了。同样，这相当简单，并且有很好的文档记录。](https://blog.capterra.com/5-outstanding-atlassian-jira-alternatives-for-your-growing-tech-team/)

# 3。测试和持续集成

Git 和敏捷工具让团队走得更快。速度不可避免地会带来错误和缺陷。考虑一个由五名开发人员组成的团队，他们开发相当独立的代码。他们每个人都会向存储库发出一个拉请求。可能会出现两个问题:

*   一旦“第一个开发人员”的代码到达 Git 存储库，其他人的代码就不再有效或有效，因为有些事情已经改变了。这不是“第一个开发者”的错误，这只是生活，它会发生。
*   一旦所有开发人员将他们的代码推送到 Git 存储库，开箱后一切正常工作的可能性就相当低了。还是那句话，这并不是团队合作不好的反映。它就会发生。

因此，你需要测试你的软件。即使在每个开发人员都推出了他们的代码之后？这将是伟大的，但浪费时间。

当其他开发人员推出他们的代码时，我们需要重复这项任务。一旦每个人都提交了他们的代码，你应该测试吗？这也很好，但是您会在过程的后期发现错误，这会延迟整个项目。那么如何解决这个问题呢？

自动化测试和持续集成有助于解决问题。

自动化测试是一个可以写很多书的话题。每种语言和框架都有自己的一套工具。在这里列出它们是没有意义的。请记住，测试是耗时的，并不总是计划好的。

无论如何，您应该知道如何为您的代码编写单元测试，并且积极主动地编写它们。如果你没有时间实际去做，你至少应该意识到这是错误的。

持续集成是一个过程，在这个过程中，对您的存储库的每一次推送都会触发一次构建并自动运行您的测试。一旦出现错误的提交，就会出现一个红色标志。在合并之前，还应该自动测试拉请求，以避免影响整个团队的错误。

持续交付是持续集成的延伸。如果测试顺利通过，测试后的版本将自动被推送到生产环境中。

举个例子:在 Quora，他们跟踪代码被推送到存储库和部署到生产服务器之间的时间。我读到的最后一个基准测试是 15 分钟……大约 100 名开发人员。这是一个团队所能期望的最强大的设置。

流行的持续集成服务器有 Jenkins、Travis、CircleCI 等等。我们在 Fire CI 推广一种[无服务器的持续集成方法](https://fire.ci)以使开发人员的生产力达到最佳状态。你不需要知道如何设置服务器和所有的东西，但是你应该知道这样的工具存在，如果你的团队不使用任何工具，你的头脑中就会触发红色警报。

# 4。耐心和理解

作为一名开发人员，你的主要挫折不会来自代码。你可能会花几个小时在 Google 和 Stack Overflow 上搜索答案
,但这是一项驱动活动。真正的痛苦来自外部:

*   不清楚或被误解的需求
*   人们对你工作的最终结果不满意，因为他们一开始就不知道自己想要什么，他们希望你能弄清楚
*   由于某些原因变得缓慢或停止工作的工具
*   不时被打扰，因为人们认为坐在你的办公桌前意味着他们可以随时打扰你

这里的关键词是“耐心”。试着去理解你周围的人。他们不是什么都知道。记住你也不知道。一定要让他们明白你的时间是宝贵的，他们应该尊重你的时间。

最后但并非最不重要的一点是，当涉及到客户和需求时:不要接受低层次或笨拙的详细需求，如果你没有得到一个大图片的简要。你需要了解你正在为之构建软件的业务。在一天结束时，您的代码在这里改进现有的流程或启用新的流程。

> 你对事情的商业方面理解得越好，你就会成为更好的开发人员。

# 结论

开发人员工作的核心是编码。一旦你从个人爱好者转变为执行团队的一员，正确使用关键的协作工具就像编写干净的代码一样重要。希望这篇文章能让你对这些工具有一个大概的了解，以及如何进一步挖掘，使你的工作做到最好！

原载于 2019 年 4 月 5 日[火词博客](https://fire.ci/blog)。