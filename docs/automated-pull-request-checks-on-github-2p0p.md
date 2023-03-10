# Github 上的自动拉取请求检查

> 原文：<https://dev.to/samturrell/automated-pull-request-checks-on-github-2p0p>

没有多少工作能让你直接提高日常工作的效率。这是成为一名开发者如此伟大的原因之一。解决问题是我们的难题。我们可以识别问题并简化它们，或者完全自动化它们。

作为一名主管，我的职责之一是批准和合并 GitHub 上的 Pull 请求。这项任务本身可能需要足够长的时间(取决于 PR 的大小)，而不需要半频繁地来回确保遵循适当的指导方针。随着几周前 [GitHub Actions](https://github.com/features/actions) 的测试版发布，这似乎是开始关注这一领域并看看我们如何简化这一过程的最佳时机。

所以，有些问题:

*   标题没有遵循正确的格式
*   未充分填写请购单模板
*   用户在提交之前没有审查他们自己的代码
*   调试代码留在
*   确保 WIP 分支不能合并
*   等等等等。

不要误会我的意思，这些事情不会一直发生，但所有这些都可以在我们来检查公关之前自动消失。这意味着合并不会被不必要的延迟，我们的项目经理也不会焦躁不安。那么我们该怎么做呢？嗯，如果您曾经使用过 Travis 或者类似的 CI 工具，您可能已经看到了如何使用“检查”来防止 PRs 被合并，直到测试套件已经通过或者资产已经被构建。似乎是解决我们大多数问题的完美功能。

<figure>[![Travis CI Checks on Github](img/2718061a3b755f26947e6bfa45e41593.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8NLrUqKU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://samturrell.github.img/github-travis-checks.png) 

<figcaption>特拉维斯在 Github 上检查 CI</figcaption>

</figure>

方便的 GitHub REST API 允许我们在这里实现我们需要的一切，甚至比手动调用多个端点更好的是，有一个完整的框架来构建 GitHub 应用程序，以便在整个组织中安装您的集成。如果你对自己建造一个感兴趣的话，可以查看一下关于 Probot 的文档。

因为这是我第一次使用 Probot，所以我决定从一个简单的开始:PR Titles。一个标题只需要两样东西。吉拉任务标识符和更改的简短描述。

计划是:

*   创建 PR 后，我们将进行检查
    *   通过 Probot ✅的 Webhook
*   检查标题是否符合格式
    *   JavaScript 逻辑✅
*   如果检查失败，请在 PR 上留下评论，以便用户进行修改
    *   通过 probot✅github API
*   当 PR 更新时，我们将再次检查
    *   冲洗并重复♽

Probot 的使用非常简单，在几个小时内我就完成了这个应用程序，并使用 jest 和 nock 对所有可能的标题变体进行了完整的测试(同样，Probot 几乎毫不费力地做到了这一点)。

你的机器人的部署被证明是非常简单的，但是以我的经验来看，这有点痛苦。然而，我把这归因于以前从未使用过 now.sh，以及他们的版本中最近的一个重大改进，这个改进在设置上不太好。我降级到早期的专业，这是一个愉快的多。

还有一些工作要做，以消除这些缺陷，但这个最初的应用程序让我兴奋地扩展我们在这一领域的自动化。希望我们可以建立一套全面的检查，让一切运行得更顺畅一点。

如果你想检查应用程序的代码，你可以在这里这样做[。](https://github.com/netsells/probot-pr-title-check)