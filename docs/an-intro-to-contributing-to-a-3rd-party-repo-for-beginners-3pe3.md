# 新手参与第三方回购简介

> 原文：<https://dev.to/oleksiyrudenko/an-intro-to-contributing-to-a-3rd-party-repo-for-beginners-3pe3>

固执己见且可能过于简单的指导方针。请在评论中随意挑战。

你会在[谷歌幻灯片](https://docs.google.com/presentation/d/13dati5gvA5f_hQFgxJPhPicjF5CRKu1e75RSsahmEaU)中看到这篇文章的内容。

## 先决条件

您知道并使用基本的 git 命令，如`init`、`clone`、`add`、`commit`、`branch`、`checkout`、`merge`、`pull`、`push`。

您可能知道并使用类似于`fetch`、`pull --rebase`、`rebase`、`cherry-pick`的 git 命令。

您希望参与一个您没有写权限的回购。

你不知道如何处理你没有写权限的回购协议，或者对此没有信心。

您没有遵循任何处理您自己的回购或您没有写权限的回购的固定工作流程。

## 你将学到什么

*   如何让您的 fork 与源 repo 保持同步
*   如何将冲突风险降至最低
*   如何让你的回购历史记录树尽可能的干净
*   如何通过拉式请求做出适当的贡献

## 一个好的工作流程开始

一些定义:

*   **上游**是您想要参与的回购
*   **原点**或**分叉**是自己上游的一个分叉
*   **local** 是 origin 的本地克隆

### 初始化事物

1.  逆流而上。在 GitHub 上导航到上游，点击 **Fork** 按钮。
2.  `git clone origin-url.git target-directory`在你的机器上克隆你的叉子
3.  `cd target-directory`
4.  `git remote add upstream-url.git`启用上游相关动作

### 添加您的修改

重要的是让你的分叉与上游同步，使用分支并遵循特定的工作流程，以最小化冲突风险并尽可能保持历史树的整洁。

#### 初始化一个特征分支

1.  本地和原点与上游同步`git checkout master && git pull upstream master && git push origin master`
2.  创建一个有意义名称的特征分支`git checkout -b feature-branch`

> 有许多分支命名约定。值得一提的有:
> 
> *   从作者姓名首字母开始，这样其他合作者就知道是谁初始化了分支，也可以找到每个作者的分支，例如`or-feature-portview`
> *   使用斜线来命名作用域，例如`feature/api/rest`、`bugfix/sleep`
> *   使用标签/问题/史诗/故事 id，例如`feat/epic123/story128/search-people`

有用的别名:

`git config --global alias.sync-master '!git checkout master && git pull upstream master && git push origin master'`

用法:`git sync-master`

#### 功能开发周期

1.  想做什么就做什么
2.  经常提交:`git add . && git commit -m "Add REST api PATCH method"`
3.  适当地挤压最终分组提交:`git rebase -i <REF>` ( [利用 git rebase](https://gist.github.com/OleksiyRudenko/232e1ebe6ed0780fc69d7391723cc75b)
4.  将最终本地、原点和特征分支与上游同步
    *   `git checkout master && git pull upstream master && git push origin master`
    *   `git checkout - && git merge master`
    *   使用 IDE 或命令行解决冲突
    *   完成时`git add . && gir commit -m "Resolved merge conflict by incorporating both suggestions"`
    *   参见[使用命令行解决合并冲突](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/)
5.  转到`#1`
6.  最终推至原点:`git push origin feature-branch`

有用的别名:

`git config --global alias.sync-branch '!git sync-master && git checkout - && git merge master'`

用法:`git sync-branch`(它也做`sync-master`作为流程的一部分)

**提交消息**

有许多提交消息结构约定。
举几个例子:

*   从提交范围开始，例如`feat|fix|docs|style|refactor|test|chore`之一
*   添加特征/修复范围，例如`(api)`
*   在主题行中使用祈使语气，例如`Add|Change|Fix|Refactor|Remove|Bump version|Release version`之一

如果您解决了一个问题(在上游或分叉中),做一个关于问题解决的注释是很有用的。

您的提交消息可能如下所示

```
fix(foo api): Fix ABC component render conditions

Resolves: #12
See also: #34, #78 
```

延伸阅读:

*   [常规提交 1 . 0 . 0-β2](https://www.conventionalcommits.org/en/v1.0.0-beta.2/)
*   [如何编写 Git 提交消息](https://chris.beams.io/posts/git-commit/)
*   [更好提交消息的 5 个有用提示](https://robots.thoughtbot.com/5-useful-tips-for-a-better-commit-message)
*   [语义发布](https://github.com/semantic-release/semantic-release)
*   [甚至更多...](https://www.google.com/search?q=writing+a+good+commit+message)

**不要做**

不要挤压或改变遥控器上已经有的东西！

不要覆盖遥控器上的历史记录！

**NB**

以上并没有涵盖当你和别人在同一个分支工作时，你会更喜欢`git pull --rebase`而不是`git pull`的情况。

### 通过拉式请求进行贡献

贡献并不是必须的。您可以为自己的目的开发您的 fork(遵守上游许可条件)。

否则，坚持以下投稿工作流程:

1.  `git push origin feature-branch`将你的特征分支推到原点。
2.  导航到云 git hub(例如 GitHub)
3.  使用云用户界面创建 PR
4.  通过代码审查过程，直到您的更改被合并或拒绝

一旦您的功能分支合并，您将在下次与上游同步时发现您的提交。因此，您不需要在本地将它们合并到您的`master`中。

然而，如果你没有为上游做出贡献或者你的 PR 被拒绝并且你想为自己保留这些，你将需要合并到`master`中。

### 打扫卫生

一旦你的公关批准和合并，你可以摆脱你的功能分支。

`git branch -D feature-branch`

* * *

随意派生[源要点](https://gist.github.com/OleksiyRudenko/236c3046fbba028e0555fa847dae7001)并进行编辑，使其符合您的特定需求(例如，删除您已经非常熟悉的部分)。