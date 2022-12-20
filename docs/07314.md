# GitFlow:方法和实践

> 原文：<https://dev.to/mindsers/gitflow-methodology-and-practice-142l>

GitFlow 就是我们所说的工作流，Git 的工作策略。还有很多其他的，但是 GitFlow 是最著名的一个。他被 nvie 认为。用它有什么意义？

## 为什么拥有 Git 策略很重要？

Git 是一个强大的工具，但是经常被误用。我们都知道，一个伟大的工具如果使用不当，会适得其反。

在 Git 的情况下，它可以是每次(或几乎)提交/合并时的冲突、数据丢失(即使您真的必须为此而推动它)等形式...

## git flow 是如何工作的？

GitFlow 是基于 Git 分支模型的简单规则列表。

这里是基础:

我们的项目基于两个分支:`master`和`develop`。严禁开发人员在这两个分支上写东西。

分公司是我们生产的镜子。因此，我们不能直接在这里进行修改。

分支集中了将在下一版本中交付的新特性。你必须强迫自己不要直接修改它。

另外三种类型的分支将允许我们工作:

*   `feature`
*   `release`
*   `hotfix`

### 开发特性

所以，我将开发一个`feature`类型的分支。

```
git checkout -b feature/<nom> develop 
```

Enter fullscreen mode Exit fullscreen mode

如果我开发了一个新特性，逻辑上它将被应用到下一个版本，所以我将从`develop`分支开始创建我的分支。

我开始为新版本更新代码。

```
git checkout develop
git merge feature/<nom> --no-ff
git branch -d feature/<nom> 
```

Enter fullscreen mode Exit fullscreen mode

当我完成时，我将代码放在`develop`分支上，并删除现在废弃的`feature`分支。

### 我准备制作新版本

我要做一个`release`型的分支。

```
git checkout -b release/<version> develop 
```

Enter fullscreen mode Exit fullscreen mode

我从`develop`分支创建我的分支，所以我可以运行我的测试并应用我的修正，而我的同事已经开始为下一个版本开发新特性了。

如果您的同事已经合并了一些针对下一个版本的代码，您可以用正确提交的散列替换`develop`分支名称。

```
git checkout develop
git merge release/<version> --no-ff

git checkout master
git merge release/<version> --no-ff
git tag <version>

git branch -d release/<version> 
```

Enter fullscreen mode Exit fullscreen mode

当我所有的测试都成功通过，并且我的新版本已经准备好投入生产时，我把所有的事情都推到了`master`分支上，同时也没有忘记把我的修改应用到开发分支上。

我还在生产分支的最后一次提交上创建了一个标记，用我的版本号来保持清晰。

最后，我删除了`release`分支，因为它现在已经没用了。

### 我在生产中修复了一个错误

我将在一个`hotfix`类型的分支机构工作。

```
git checkout -b hotfix/<name> master 
```

Enter fullscreen mode Exit fullscreen mode

对于这个特殊的例子，我从生产的镜像开始创建我的分支，因为当我修复一个简单的生产 bug 时，我不希望开发分支的所有特性都在生产中。

```
git checkout develop
git merge hotfix/<name> --no-ff

git checkout master
git merge hotfix/<name> --no-ff
git tag <version>

git branch -d hotfix/<name> 
```

Enter fullscreen mode Exit fullscreen mode

我的 bug 被修复了，我需要把它应用到开发和生产部门。我再一次在我的`master`分支上添加了一个标签，并删除了我的`hotfix`分支。

## GitFlow，覆盖层

你觉得你刚刚读到的一切都很棒，但是很难在项目中执行？我明白了。当你刚开始时，根据你所处的情况，很难记住分支的名称、每个分支的起点和目标。

谢天谢地，GitFlow 的创建者考虑到了你，为 Git 开发了一个 overlayer 来简化这个过程。它为您提供了新的高级命令，例如:

`git flow init`:初始化项目中的 Git 和 GitFlow。

开始开发新功能。

`git flow feature finish <name>`:结束新功能的开发。

开始新版本的开发。

结束新版本的开发。

`git flow hotfix start <version>`:开始开发新的热修复程序。

`git flow hotfix finish <name>`:结束开发新的热修复程序。

GitFlow 将为您选择:起始分支、目标分支、创建标签和删除正确的分支。

当然，还有很多其他的命令，但是这些对我来说是最重要的。

至于其他，只需使用常规的 Git 命令。

## 深挖

这个工作流程很漂亮。每个使用它的人都可能在他们的工作质量上取得了很大的进步。但是有很多可能的改进。

例如，单元、端到端和性能测试自动化，以及生产自动化中的部署和/或直接来自那些先前测试的集成。

编辑:

看完这篇文章后，我的一些同事让我在一个更复杂的项目中执行 GitFlow，在这个项目中，开发人员必须同时维护几个版本。这个问题是公平的，我写了一个关于这个话题的的[帖子。](https://blog.nathanaelcherrier.com/en/several-versions-gitflow/)