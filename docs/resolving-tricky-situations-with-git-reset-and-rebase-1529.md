# 用 git 重置和重置🧶解决棘手的情况

> 原文：<https://dev.to/aleccool213/resolving-tricky-situations-with-git-reset-and-rebase-1529>

> 想要更多这样的精彩内容？访问: [https://blog.alec.coffee](//alec.coffee)

您应该关心您的 Git 历史有多干净。它提供了一种与其他开发人员交流代码库变更的方式，但是这样做需要一些思考和意图。见过这么干净的吗？

[![A clean Git history](img/5402748a07d707ac11cb79c72c9eaac6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k0eF9-Sz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dscgr6mcw/image/upload/v1557317705/git-post/1.png)

大概不会。为什么？开发人员经常太懒(有充分的理由),在优先级方面把 Git organization 放在`nice-to-haves`和`TODO`旁边。借助`git reset`和`git rebase`的力量，我将向你展示拥有一个干净易读的历史是快速而容易的！

让我们首先向您介绍一些 Git 技巧，您可以轻松地将它们集成到您的工作流程中。这些技巧不仅可以帮助您理解使用 CLI 时的 Git，还可以帮助您理解 GUI 客户端。这些主题我从未在 Git 教程中看到过，也是我自己或从别人那里了解到的🍻和同事一起。

## WIP 工作流

你经常会发现自己处于这种情况:

[![](img/7164d17e8f6609d7c2bb7898c5462a5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nzNU4fAR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/dscgr6mcw/image/upload/v1557317705/git-post/2.gif)

您刚刚开始处理一个新的分支，重构了一段代码，安装了一个包，并编写了该功能所需代码的一半。

下午 6 点左右，你承诺一切来保存你的进展。

[![](img/a8a72232aabd5b55e8e5da040f13572a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PkofX1ij--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/dscgr6mcw/image/upload/v1557317705/git-post/3.gif)

当你回到这个项目时，你意识到，“哦，不！将它放入单独的提交中会花费很多时间！”。好吧，所以你可能不这么想，但是，嗯，你可能和我一样懒。

```
 git reset HEAD~1 
```

你的朋友在这里吗？这将在您的`wip`提交之前重置您的当前头，但是您的工作树(您的所有文件更改)将被保留以继续编辑。

[![](img/926dfa3afa4be71f521df321d0f4689f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BW4ijRKg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/dscgr6mcw/image/upload/v1557317705/git-post/4.gif)

> 这是这个伟大司令部的众多能力之一。我建议你多研究一下这个。

厉害！现在我们可以开始单独提交一些东西来做一些不错的提交了！

[![](img/1c7528bbd47722c0cf8705ec9823391e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NptgfIrZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/dscgr6mcw/image/upload/v1557317705/git-post/5.gif)

我们的提交现在更有意义了！让我们扔一个🔧变成东西。

## 改写历史...正确的方式

你已经在一个分支上工作了一段时间，你所有的提交都很好并且很有条理，但是有人评论了一个问题，代码退回了两次提交😱

您在文件中犯了一个小错误，需要快速更改。

[![opening-a-file-for-a-change](img/11d7fb81ef30a52afd5b5630f40b4162.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4CpiWtYr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/dscgr6mcw/image/upload/v1557317705/git-post/6.gif)

这种变化在技术上与一个旧的提交相关，那么我们如何及时返回并在那里插入它呢？

```
 git stash; git rebase -i HEAD~insert_commit_history_depth 
```

这将保存我们的单行更改供以后使用，并将我们带到交互式 rebase 屏幕。让我们继续编辑我们想要将更改引入提交。

[![](img/46501664e66b4cff80c9a7cacc19902b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zpo0Eq2E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/dscgr6mcw/image/upload/v1557317705/git-post/7.gif)

既然我们正在编辑旧的提交，我们需要把我们的更改带回来。

```
 git stash pop; git add .; git rebase --continue 
```

这将把我们之前藏在 git 索引中的更改，添加到我们想要编辑的 commit 中，然后继续。

就是这样！要更快地做到这一点，有一件事是将这些命令串别名化为快捷方式。

### 对一些牛逼工具大喊大叫

两个 Git 工具的 S/O，它们最近切入了我的工作流，因为它们非常实用和漂亮:

*   Gitup 是一个很好的最小化的工具，它不会尝试做太多的事情。它的分支视图，存储查看器和集成是了不起的。Git CLI 用户的绝佳开源伴侣。
*   gitmoji-cli 使得可视化地组织提交变得非常容易。它不仅通过强迫你分类来组织你的提交，而且还有表情符号！💸

> 喜欢这个帖子？考虑给我买一杯咖啡来支持我写更多的东西。
> 
> 想收到有新帖子的季度邮件吗？[注册我的简讯](https://mailchi.mp/f91826b80eb3/alecbrunelleemailsignup)