# Git 别名不一定要很无聊！

> 原文：<https://dev.to/switowski/git-aliases-dont-have-to-be-boring-3b5e>

在[的前一篇文章](https://dev.to/switowski/configuring-git-2409)中，我们讨论了在`.gitconfig`文件中放入什么来增强 git。我们已经讨论了一些基本的别名，但是你可以做的远不止简单的`ci = commit`类型的东西。你可以定义完整的函数来帮助你完成日常任务。以下是我的一些想法:

## 1。挤压提交

```
squash = "!f(){ git reset --soft HEAD~${1} && git commit --edit -m\"$(git log --format=%B --reverse HEAD..HEAD@{1})\"; };f" 
```

Enter fullscreen mode Exit fullscreen mode

这是我最喜欢的一张。我来解释一下原因。我经常在一个特性上工作，当我完成时，我写了一个很好的提交消息来总结我刚刚做的事情，只是注意到一些测试没有工作。所以，我修复了它们，并创建了另一个提交。终于结束了。除了我忘了记录一个函数。又一次犯罪。啊，我在文档里打错了。诸如此类。一旦我*真的*完成了，我就有了我描述得很漂亮的提交，然后是一堆其他不太漂亮的提交。这里是`squash`函数的用武之地。如果我有五个提交想要变成一个提交，我就运行`git squash 5`。它会将所有五个提交合并成一个，并打开一个文本编辑器，其中包含我可以编辑的所有五个提交消息。一旦我保存并退出编辑器，我最终只能用一个漂亮的 commit 来描述我的特性。太棒了。

## 2。合并拉取请求

```
mpr = "!f() { \
        declare currentBranch=\"$(git symbolic-ref --short HEAD)\"; \
        declare branch=\"${2:-$currentBranch}\"; \
        if [$(printf \"%s\" \"$1\" | grep '^[0-9]\\+$' > /dev/null; printf $?) -eq 0 ]; then \
            git fetch origin refs/pull/$1/head:pr/$1 && \
            git checkout -B $branch && \
            git rebase $branch pr/$1 && \
            git checkout -B $branch && \
            git merge --ff-only pr/$1 && \
            git branch -D pr/$1; \
        fi \
    }; f" 
```

Enter fullscreen mode Exit fullscreen mode

这个看起来很复杂，但如果你是 GitHub 上一个项目的维护者，它实际上相当有用。有两种方式可以调用:`git mpr 123`或者`git mpr 123 master`。第一个版本将在当前分支的顶部合并第 123 个拉请求。第二个将把第 123 个拉请求合并到名为 master 的分支之上。如果你正在使用 GitHub，你可以很容易地使用 web 界面合并一个 pull 请求，但是如果你想对合并 pull 请求的方式有更多的控制(例如，我使用的是`git merge --ff-only`选项，而 GitHub 将使用`git merge --no-ff`)，或者你只是想在不离开终端的情况下合并一堆 pull 请求——这个功能将帮助你。

**注**:目前只支持 GitHub。我需要为 GitLab 找到一个相似的别名，因为我已经开始更频繁地使用它了。

## 3。结帐拉取请求

```
copr = "!f() { git fetch -fu ${2:-origin} refs/pull/$1/head:pr/$1 && \
               git checkout pr/$1; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

有时，仅仅读取一个*拉请求*(或者 GitLab 称之为*合并请求*)的代码可能不足以验证它是否应该被合并。也许你想添加一些东西，或者一些测试失败了，你想在你的计算机上本地运行它们。多亏了 Ned Batchelder 的博客，我找到了一个简单的方法。`copr`函数将从远程存储库下载 pull 请求到您的计算机，作为一个新的分支。有两种方式可以使用:`git copr 123`或`git copr 123 remote_repo`。第一个将从名为`upstream`的远程存储库中下载编号为 123 的拉请求。第二个将下载相同的拉请求，但是这次是从一个名为`remote_repo`的远程存储库中下载。

**注意**:同样，只支持 GitHub。

## 4。删除合并的本地分支

```
bclean = "!f() { git branch --merged ${1-master} | grep -v " ${1-master}$" | xargs -r git branch -d; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

该功能将删除所有已经合并到`master`分支的本地分支。您可以指定一个不同于`master`的分支作为参数(如果出于某种原因，您的**主**开发分支被不同地调用)。我之所以使用这个函数，是因为当我的本地分支被合并时，我从来不记得删除它们，而且在某些时候，我会迷失在我最终拥有的几十个分支中。

## 5。暂时忽略文件

```
ignore = update-index --assume-unchanged
unignore = update-index --no-assume-unchanged
ignored = !git ls-files -v | grep "^[[:lower:]]" 
```

Enter fullscreen mode Exit fullscreen mode

从版本控制中排除一些文件的一种方法是将该文件添加到`.gitignore`文件中。但是也许你想暂时忽略一个文件只有*。在这种情况下，我使用这三个函数。第一个会让 git 忽略一个给定的文件或目录(所以即使您对该文件进行了更改，当您调用`git status`时，它也不会显示为已修改)。第二个将停止忽略该文件。最后一个会显示一个被忽略文件的列表。*

 *## 6。显示所有别名

```
aliases = config --get-regexp alias 
```

Enter fullscreen mode Exit fullscreen mode

尽管我试图使用容易记住的名字，但有时我会忘记给定的函数是如何被调用的。当我这样做时，我可以调用`git aliases`来查看我的`.gitconfig`文件中定义的所有别名的列表。

## 7。忽略重复的`git`

```
git = !exec git 
```

Enter fullscreen mode Exit fullscreen mode

这是我在[凯厄斯理论博客](http://caiustheory.com/git-git-git-git-git/)上发现的一个有趣的好奇心。想象你开始输入`git`，然后你分心了一会儿，但是当你回到终端时，你继续输入`git status`。但是，由于失误，你最终选择了`git git status`。如果你经常遇到这种情况，那么上面的别名可以帮助你。它会简单地忽略重复的`git`命令，所以像`git git git git status`这样的命令会以和`git status`完全相同的方式执行。*