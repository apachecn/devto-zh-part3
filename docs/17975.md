# 你如何命名你的 git 分支？

> 原文：<https://dev.to/michael/how-do-you-name-your-git-branches-157i>

我目前正在评估一种替代吉拉的方法来管理我们工作中的软件开发项目。我很难摆脱的一件事是吉拉拥有密钥和自动递增每个任务/问题 ID 的能力。

让我们假设我有一个 DEV.to 项目设置，我可以设置一个键为 DEV，我创建的每个问题都有一个前缀`DEV-`。目前，我们将使用这个键和 ID 组合来命名源代码中的 git 分支。因此，如果问题是 23，那么我们的分支将被切割为`DEV-23`，然后我们可以很容易地在 BitBucket 上从 [VCS](https://www.spellitout.xyz/vcs-version-control-system/) 跟踪到吉拉的 PRs。

但是当我评估替代方案时，我意识到这个键/id 可能是吉拉或软件开发管理工具所独有的。

所以我的问题是，如果没有键或 ID，并且你跨多个项目工作，你为你的 git 分支使用什么样的命名方案？