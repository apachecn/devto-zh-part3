# 使用 git rebase 拆分提交

> 原文：<https://dev.to/erikthered/splitting-a-commit-with-git-rebase-2ihp>

我最近正致力于将一个项目迁移到 GitLab，并且有一个分支有一些无关的提交，来自于试图正确地设置 CI。当我重定基准时，我注意到我的提交中有两个不同的变化。经过一番挖掘，我发现我还可以使用交互式 rebase 将这个提交分成两个不同的原子提交。我将介绍我们如何实现这一目标。

首先，让我们执行一个交互式的 rebase，这样我们想要分割的提交就包含在这个范围内了。在这种情况下，我们可以将整个分支重置到它从主分支分叉的地方，因此只需运行`git rebase -i master`。下面我们可以看到交互式 rebase 的一个片段:

[![Screenshot of rebase](img/f9120bcefb3164d405f9b8ae3f67d424.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AeFoRDAY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wkzh83m016ypjlcnad0x.png)

我们想要分割的提交具有散列值`bc38ac55`，所以让我们将*选择*改为*编辑*。

[![Screenshot of updated rebase with edit](img/973884af5bb31b739420e392ece4af77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nF-B_5Qy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yn0gm08q2p0bnx7ua1a2.png)

我们可以保存并退出编辑器，现在我们已经得到消息“在 bc38ac55 停止”。好吧？不完全是。我们真正想要做的是编辑`bc38ac55`，所以我们需要运行`git reset HEAD~`以到达一个状态，在该状态下，更改还没有被登台和提交。在运行该命令并执行`git status`后，我们可以看到更改已经存在，但没有进行:

[![Screenshot of git status](img/c61d137b6aa003e57e83d701cfb46853.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g8tWevEo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d2onnj4iu9onr1si6hbl.png)

现在我们可以根据需要准备和提交，创建 2 个(或更多)提交。完成后，我们可以继续运行`git rebase --continue`来完成 rebase。现在，当我们运行`git log --oneline`时，我们可以看到我们的变化。

[![Screenshot of git log after changes](img/cf89fec488190faec8eac3f4316acac9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1olsoKH2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d1xuwr7etsjnkiov6mtj.png)

我们现在可以看到流程中创建的两个提交，并注意到哈希也发生了变化。显然，这是最好在本地分支机构或您独自工作的分支机构完成的事情，并且可以安全地强制推送到远程回购。

希望这对你和我一样有帮助！