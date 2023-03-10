# Git Cheat sheet 中的撤销更改，用于 git checkout、stash、reset、clean、revert、rebase -i、amend

> 原文：<https://dev.to/michi/undo-changes-in-git-cheat-sheet-for-git-checkout-stash-reset-clean-revert-rebase-i-amend-2h1h>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/undo-changes-in-git)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

Git 是你逐步学习的东西之一。您从`git add .`开始暂存文件，`git commit -m “message”`在本地提交文件，最后`git push`将文件推送到远程存储库。但是随着时间的推移，你会犯错误，如果你总是搜索并粘贴随机的命令，你可能会很容易被大量的命令弄糊涂，比如`git reset`、`git revert`、`git clean`、`git checkout .`、`git rebase -i`、`git commit --amend`等等。让我们一个一个来看！

# 目录

*   [理解关键术语](#understanding-key-terms)
*   [不撤销:保存更改以备后用](#dont-undo-save-changes-for-later-use)
*   [不撤销:在最近一次提交中添加内容或更改消息](#dont-undo-add-something-to-the-latest-commit-or-change-the-message)
*   [拆分文件(预提交)](#unstage-files-precommit)
*   [恢复更改(预提交)](#revert-changes-precommit)
*   [删除新的/未跟踪的文件和目录](#remove-newuntracked-files-and-directories)
*   [在新提交中恢复提交](#revert-a-commit-in-a-new-commit)
*   [删除最新提交而不进行新的提交](#remove-latest-commits-without-a-new-commit)
*   [改变历史](#change-history)
*   [本地移除分支](#remove-branch-locally)
*   [移除远程分支](#remove-remote-branch)
*   [结论](#conclusion)

## 理解关键术语

你总能在这里找到更多帮助[，但是理解一些关键术语是很重要的。](https://git-scm.com/docs/)

*   索引:当您使用`git add`存放文件时，它会将文件添加到索引文件中。所以 Staging 和 index 是一样的。

*   未跟踪:如果您创建了一个新文件，则在您暂存它之前，它不会被跟踪。

*   HEAD: HEAD 指向当前分支的最新提交。这就是为什么你有时会看到有趣的选择`detached HEAD`，例如当你检查一个旧的提交时。

*   `git add .`中的点是指当前目录。因此，当前目录和所有子目录中的所有文件都将被转移。我们将学习更多使用点号的命令。

## 不撤销:保存更改以备后用

> `git stash -u`

这将保存每个未跟踪的文件(-u 标志)，暂存和未暂存的修改。

要再次检索最新的存储，请运行

```
git stash apply 
```

Enter fullscreen mode Exit fullscreen mode

为了保持你的存储列表干净，你也可以执行`git stash pop`来代替。它将做同样的应用，但也从存储列表中删除已应用的存储。

使用`git stash list`来获取你所有物品的清单。

**用例**:你正在做某件事情，但是突然需要改变分支(例如，为一个突发的紧急 bug 创建一个补丁)

**奖励**:要知道什么藏毒属于什么，可以通过运行:`git stash push -u -m "your message"`给你的藏毒一个音符

## 不撤销:在最近一次提交中添加内容或更改消息

> `git commit --amend -m "added file and changed message to this"`

`amend`允许您在最新提交中添加更多文件。

**用例**:您忘记存放某个文件，该文件应该是提交的一部分。

**奖励**:即使没有添加文件，你仍然可以通过“修改”选项来修改信息。

如果其他人正在你的分支上工作:注意不要在最新的提交已经发布(推送)的时候修改。这就需要`git push --force`

## 卸载文件(预提交)

> `git reset .`

也常常被看作仅仅是`git reset`

示例:

```
echo "code code code" >> index.js
git add .
git reset . 
```

Enter fullscreen mode Exit fullscreen mode

它将简单地解除 stage `index.js`并将更改放回你的工作树中。您可以应用`--hard`标志来完全删除您的更改。

**用例**:出于习惯，您使用`git add .`暂存所有的修改，但是想要分离某些文件以分别提交它们。

**奖励**:要仅卸载特定文件，您可以使用与`git add`
相同的方法

```
git add User.js UserController.js UserService.js
git reset UserService.js User.js 
```

Enter fullscreen mode Exit fullscreen mode

这让`UserController.js`一直上演着。

## 还原更改(预提交)

> `git checkout .`

用于改变分支，但是如果你检查一个文件路径，它有不同的目的。

如果您在本地更改了任何文件，这将使用索引或提交中的内容恢复您的更改。稍后会有更多的介绍。

示例:

```
echo -n "1" >> newfile
git add .
git commit -m "added newfile"

echo -n "2" >> newfile 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们创建了一个新文件，暂存并提交它，然后将文本“2”附加到该文件。

如果您现在运行`git checkout newfile`，它将删除任何本地更改，在本例中为“2”。你的工作树将再次变得干净。

让我们来看看当你在两者之间开始登台时会发生什么。这会让你对`git add`有一个新的认识，让这个命令比以前更强大。

```
echo -n "1" >> newfile
git add .
git commit -m "added newfile"

echo -n "2" >> newfile
git add .

echo -n "3" >> newfile 
```

Enter fullscreen mode Exit fullscreen mode

这是文件在不同状态下的内容

*   头:“1”
*   指数:“12”
*   工作树:“123”

如果您现在运行`git checkout newfile`，它只会从您的本地更改中删除内容`3`。换句话说，如果它在索引中找到文件，它将根据索引中的内容而不是文件头中的内容来恢复更改。它仍将上演内容为“12”的`newfile`。

要删除“^ 2 ”,您必须首先按照我们所学的方法取消该文件的暂存，然后再次将其签出。

```
git reset
git checkout . 
```

Enter fullscreen mode Exit fullscreen mode

> 因为它只替换索引/头中的文件，`git checkout`不会对未跟踪的文件做任何事情。

**用例**:您对文件 A 进行了修改，当修改文件 B 时，您意识到文件 A 中的更改实际上是不必要的，最好再次检查它。

**奖励**:如果你想签出的文件不幸与一个分支同名，你必须像这样签出`git checkout -- master`以避免签出主分支。

## 删除新的/未跟踪的文件和目录

> `git clean -f`

类似于`git checkout .`，不同之处在于它只对未被跟踪的文件有效。你可以运行`git clean --dry-run`或`git clean -n`，看看哪些文件会被**永久删除**。添加`-d`标志来包含目录。

```
touch newfile
git clean -d -n 
```

Enter fullscreen mode Exit fullscreen mode

输出将是`Would remove newfile`

**奖励**:使用`git clean -i`开始交互式清理，给你更多的选择来单独处理每个文件。

## 在新提交中恢复提交

> git 回复`commit-id`

恢复提交 ID 的更改，并为其创建新的提交。

**用例**:一个已经被推送的提交导致了一个 bug，必须被恢复。

**附加**:应用`-e`或`--edit`标志修改提交消息。例如，您可以添加必须恢复该提交的原因。

## 删除最新提交而不进行新的提交

> git 重置 HEAD^

假设你不小心犯了某个错误，你想撤销这个错误。将恢复最近的提交，就像从未发生过一样，并将修改放回本地工作树。它不会创建新的提交，最新的提交将从历史记录中消失。

我们之前看到过`git reset`已经对文件进行了拆分。然而，当您传递一个提交时，您实际上可以将 HEAD 重置为您想要的任何提交。假设你有提交 A，B 和 c。用`git reset A` A 将成为最新的提交，如果你`git log`，你将不再找到 B 和 c

我们说过`git reset HEAD^`保持工作树中的变化。换句话说，`git reset HEAD^`重置了头部和索引。

使用`--soft`标志仅重置头部，这意味着更改将保持`staged`。
使用`--hard`标志重置磁头、索引和工作树，这意味着更改将被完全删除。

除了`git reset HEAD^`，你还可以写`git reset HEAD^1`、`git reset HEAD~1`或`git reset HEAD~`。

**如果其他人正在你的分支上工作:当你重置的提交已经被发布时，注意不要将 HEAD 重置为先前的提交。这就需要`git push --force`**

**用例**:您在本地提交了修改**，但是意识到您提交到了错误的分支。**

**奖励**:除了最近一次提交，总共有四种方法可以重置。

所有这四行都重置了最后两次提交

```
git reset HEAD^2
git reset HEAD^^

git reset HEAD~2
git reset HEAD~~ 
```

Enter fullscreen mode Exit fullscreen mode

**Bonus2** : `git reset`并没有真正删除最近的提交，它只是简单地“回滚”到你想要的提交。b 和 C 还是存 30 天。

## 改变历史

让我们快速地看一下交互式的重新基础。我们学习了`git reset HEAD~1 --hard`作为一种删除最近提交的方法。我们可以用交互式重定基础实现同样的事情，只是它更强大。`rebase`这次实际上改变了提交对象，而不仅仅是指向一个特定的提交。

```
git rebase -i HEAD~4 
```

Enter fullscreen mode Exit fullscreen mode

我们看到与之前的`git reset`相同的`HEAD~4`。4 是指您想要重新设定基数的提交数。

这将打开一个编辑器(vim？)有以下内容

```
pick 123a44b0 your latest commit
pick C23a44b0 commit 3
pick B23a44b0 commit 2
pick A23a44b0 commit 1

# Rebase ...
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit 
```

Enter fullscreen mode Exit fullscreen mode

让我们替换

```
pick 123a44b0 your latest commit
pick C23a44b0 commit 3
pick B23a44b0 commit 2
pick A23a44b0 commit 1 
```

Enter fullscreen mode Exit fullscreen mode

由

```
pick 123a44b0 your latest commit
fixup C23a44b0 commit 3
f B23a44b0 commit 2
drop A23a44b0 commit 1 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，您可以写出类似于`fixup`的选项，或者在本例中使用缩写`f`。

这将把`C23a44b0`和`B23a44b0`挤压(合并)成`123a44b0`，使其成为一个提交。关于`A23a44b0`，这个提交将被完全删除。所以一旦重定基础完成，你将只剩下一个提交`123a44b0`。

如果其他人正在您的分支上工作:当您想要重定基础的提交已经发布时，注意不要重定基础。这就需要`git push --force`

**用例**:你独自在**自己的分支**上工作，并希望 PR/MR 有一个清晰的提交列表

奖励:你甚至可以通过改变提交在列表中的顺序来移动它们。

## 本地删除分支

> `git checkout master`
> 
> `git branch -D branch-to-delete`

**用例**:有时删除本地分支并再次签出会更快，而不是恢复一堆提交。

## 删除远程分支

`git push origin branch-to-delete --delete`

**用例:**推送后，你意识到你选择的名字没有意义，你想改变它(**奖励**:用`git branch -m "new-name"`这样做)

* * *

## 结论

Git 是一个很难掌握的东西，我相信有其他方法可以完成这些任务。如果你知道，请留下评论，我可以把它们添加到列表中。

如果这篇文章对你有所帮助，我有更多关于简化编写软件的技巧[在这里](https://michaelzanggl.gumroad.com/l/intent-driven-development)。