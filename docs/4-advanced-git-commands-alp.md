# 4 个高级 Git 命令将节省您的时间

> 原文：<https://dev.to/codicacom/4-advanced-git-commands-alp>

*这篇文章最初发表在 [Codica 博客](https://www.codica.com/blog/useful-git-commands-that-save-time/)上。*

当我们谈论任何 IT 项目开发时，我们肯定会提到版本控制系统。其中最流行的是 Git。

使用 Git，您可以跟踪和比较项目目录中的变化，管理和调整多个软件工程师之间的工作过程。它给你一个机会来自动化和加速许多过程，并得到一个问题的完整解释:谁在什么时候做了什么？

今天，我们想让你关注一下 **4 个有用的 Git 命令**，它们会让你的生活变得更简单。它们节省您的时间，自动化您的过程，并允许避免大量的日常工作。

所以，言归正传。

## `1\. git branch --merged | egrep -v "(*|master|dev)" | xargs git branch -d`

这组命令是删除已经合并的分支的魔棒和救命稻草。此外，它允许您指出那些不应该删除的重要分支(如 master 或 dev)。

让我们仔细看看这个例子:

让我们假设您有许多分支已经合并到分支机构主服务器中。您希望删除所有这些文件，并指定重要的文件，如 master 和 dev。好消息是，这是你的问题的解决方案:

```
$ git branch --merged | egrep -v "(*|master|dev)" | xargs git branch -d

Deleted branch feature/one (was 949df0d).
Deleted branch feature/three (was 450980a).
Deleted branch feature/two (was 1ac4223). 
```

Enter fullscreen mode Exit fullscreen mode

这里简单讨论一下是怎么回事:
`git branch --merged`。该命令行列出了所有被合并的
分支`egrep -v "(^*|master|dev)"`。这个命令行允许您将那些重要的分支作为不应该删除的参数进行传递。
`xargs git branch -d`。这个命令行处理前两个操作，并实际删除合并的分支。

## `2\. git clean`

此命令使您有机会删除未添加到临时区域的未跟踪文件。

让我们看看这个例子，看看它是如何工作的:

让我们假设您正在开发一个特定的功能，您首先创建一个目录来存储项目文件。例如，您创建一个名为 dogs 的新文件夹，并在该目录中插入一幅图像。

```
$ mkdir dogs
$ cp ~/Downloads/birman.jpg ./dogs 
```

Enter fullscreen mode Exit fullscreen mode

当一些功能完成时，您意识到您需要删除特定的项目文件。要删除这些文件，首先你需要检查你的工作目录是否有变化:

```
$ git status -s
?? dogs/ 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以看到哪些文件和目录发生了更改。最后，您可以使用以下命令删除所有文件:

```
$ git clean -fdx
Removing dogs/ 
```

Enter fullscreen mode Exit fullscreen mode

事实是，您将主要使用`$ git clean -ndx`和`$ git clean -fdx`命令。

## `3\. git pull --rebase --autostash`

这个 Git 命令让您有机会获取另一个工程师的提交，并在上面重新构建您的更改。

让我们用下面的例子来定义我的意思:

让我们假设您通过创建一个新文件(即`new-changes.txt`)开始开发，将它添加到登台区并提交所有的更改。

```
$ touch new-changes.txt
$ git add new-changes.txt
$ git commit -m "New changes" 
```

Enter fullscreen mode Exit fullscreen mode

然后，您创建另一个文件(`another-change.txt`)，而不将其添加到临时区域。您记录了更改的历史，并看到第一个暂存文件被隐藏在顶部。

```
$ touch 'another-change.txt'
$ git log --oneline

c23f82a (HEAD -> master, origin/master) A lot of changes
ffe7aa0 birman
0a074ca hello, world
cd2639b Initial commit 
```

Enter fullscreen mode Exit fullscreen mode

如果有任何未提交的更改，您需要首先保存这些更改，然后提取远程更新，并弹出您的保存以继续您的工作。这听起来很乏味，但是 Git 为您提供了一个额外的`--autostash`选项来自动保存和弹出您未提交的更改。

```
$ git pull origin master --rebase --autostash -v 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以检查我们的文件`one-more-change.txt`，它没有被添加到暂存区，没有消失，并且自动地进入和离开存储区。

```
$ git status
?? one-more-change.txt 
```

Enter fullscreen mode Exit fullscreen mode

## `4\. git reflog`

此命令允许您列出所有已执行的命令，然后恢复特定的提交。

为了理解这个命令是如何工作的，我为您准备了一个清晰的案例。

让我们假设出了问题，所有先前创建的更改都消失了。要检查提交历史，您需要使用`$ git log`命令:

```
$ git log --oneline 
cd2639b (HEAD -> master) Initial commit 
```

Enter fullscreen mode Exit fullscreen mode

终端的致命输出证明了这个事实。最初，您需要通过使用以下命令来获取命令的历史列表。

当然，我们可以从一个远程存储库中拉入更改，但是我们没有推送我们的功能，所有的更改都丢失了。幸运的是，这里来了`$ git reflog`求助:

```
$ git reflog 
cd2639b (HEAD -> master) HEAD@{0}: reset: moving to HEAD~9
e8e8b2f HEAD@{1}: commit: [Feature] big feature
b7b2808 (origin/master) HEAD@{2}: pull origin master: Fast-forward
... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以看到有人删除了我们的提交或者错误地使用了 Git 命令。好消息是我们可以使用`$ git reflog`回到执行 `$ git reset` 之前的状态。

我们所需要的就是引用下面的提交索引并使用`$ git reset`命令:

```
$ git reset e8e8b2f --hard
HEAD is now at e8e8b2f [Feature] big feature 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

因此，我们已经讨论了高级 Git 命令，它们可以在需要的时候伸出援手。它们并不意味着每天都要使用，但是对于一些特定的任务来说非常有价值。

如需进一步阅读，请查看我们的文章: **[你应该知道的 4 个有用的 Git 命令](https://www.codica.com/blog/useful-git-commands-that-save-time/)** 。