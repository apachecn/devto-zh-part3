# 冲突解决:Git 合并实践

> 原文：<https://dev.to/cookrdan/conflict-resolution-git-merge-practice-3iab>

这假设您对 git 和命令行(我使用 bash)有一些基本的了解。为了便于参考，我在底部加入了一些命令和一些定义。

我是 git 和命令行的新手，但是我想分享一些我正在学习的东西。我发现在终端中用一个假项目练习使用 git 是很好的。仅仅阅读 git 命令和它们做什么对使用 git 本身没有帮助。不可否认，在一个真实的项目中尝试一些东西有点吓人。所以，打开终端，用它来练习合并冲突。

我们将在 git 中经历一个冲突合并的过程。我们将看看会发生什么，然后需要做些什么来解决它。

## 上手，制造冲突

首先，设置一个用于此目的的文件夹，切换到该目录，然后执行以下操作:

```
git init
echo "line 1" > conflict.txt
git add .
git commit -m "line 1"
echo "line 2" >> conflict.txt
git commit -am "add line 2" 
```

我们在`master`分支上，现在有一个包含两行文本的文件(conflict.txt)。

接下来，我们将对这个文件进行更多的修改:

```
git checkout -b morelines
echo "line 3" >> conflict.txt
git commit -am "add line 3" 
```

我们在`morelines`分支上。现在每个分支上的 conflict.txt 如下所示:

| 行号 | conflict.txt(主) | conflict.txt (morelines) |
| --- | --- | --- |
| one | 第一行 | 第一行 |
| Two | 第二行 | 第二行 |
| three |   | 第 3 行 |

如果我们现在运行`git checkout master`然后运行`git merge morelines`，git 将能够无冲突地合并，这将是一个快速前进的合并。

**但是！**我们要冲突！所以我们要打一架，算是吧。

这样做:

```
git checkout master
echo "line 4" >> conflict.txt
git commit -am "add line 4" 
```

如果您现在可以并排查看这两个文件，它们应该是这样的:

| 行号 | conflict.txt(主) | conflict.txt (morelines) |
| --- | --- | --- |
| one | 第一行 | 第一行 |
| Two | 第二行 | 第二行 |
| three | **第 4 行** | **第 3 行** |

你可以看到这里的冲突。

## 试合并

现在让我们尝试将`morelines`分支合并到`master`分支，看看会发生什么。我们已经在`master`上了，所以这样做:

```
git merge morelines 
```

在我们尝试合并的过程中，git 给了我们这样的反馈:

> 自动合并 conflict.txt
> 
> CONFLICT(内容):conflict.txt
> 
> 中的合并冲突自动合并失败；修复冲突，然后提交结果。

我们有冲突！还要注意——git 现在正在合并过程中，希望我们做一些事情。如果您想停止合并过程，您可以运行`git merge --abort`。但是我们不会那么做。我们将解决冲突并完成合并。

## 解决冲突

在上面的消息中，git 告诉我们下一步该做什么:“修复冲突，然后提交结果。”换句话说:

**步骤 1:** 打开冲突的文件，合并修改内容。

第二步:实施变革。

**步骤 3:** 运行`git commit`以完成合并过程(这将进行一次合并提交)。

在继续之前，您可以运行`git status`并阅读它所说的内容。我不打算在这里写它，只是给它一个很好的阅读。

接下来，让我们解决冲突。

### 第一步:打开冲突文件，合并修改内容。

这个意思就是这个意思。这里没有什么是自动的。你必须用你的眼睛看着冲突的文件，并手动修改。

运行`open conflict.txt`打开文件。会是这样的:

```
line 1
line 2
<<<<<<< HEAD
line 4
=======
line 3
>>>>>>> morelines 
```

这看起来很奇怪！让我们稍微分解一下。我们可以很容易地看到我们自己放在那里的文本。但是 git 增加了额外的标记。它们有意义:

`HEAD`部分显示当前检出版本的冲突部分:

```
<<<<<<< HEAD
line 4
======= 
```

上述内容与`morelines`部分指示的传入变更相冲突:

```
======
line 3
>>>>>>> morelines 
```

总之:从我们想要合并的分支(`morelines`)传入的变更与我们已经检出的版本(`HEAD`)冲突。

Git 包含了所有这些来帮助我们手动合并变更。

我们将需要删除这些标记，并重新安排文字一点。为了让这在视觉上有意义，继续编辑文件，最终看起来是这样的:

```
line 1
line 2
line 3
line 4 
```

如您所见，我们需要的行处于正确的顺序，标记行不在那里，分隔线也不见了。

*附注:如果你不删除 git 放在文件中的标记，它们将留在那里。Git 不会把他们带走。*

补充说明:从技术上讲，你可以在这一点上做任何你想做的改变，但这不是合并的目的。坚持将改变合并在一起，而不是进行更多的改变。把它留给普通的提交吧。

保存并关闭文件。

### 步骤 2 和 3:暂存并提交

在终端:

```
git add .
git commit 
```

您的编辑器将给出提交文本，git 方便地将提交消息作为`Merge branch 'morelines'`包含在内。这对于我们的假项目是没问题的，所以保存它并关闭。

附注:在所有冲突解决之前，您将无法完成合并。如果需要，重复步骤 1 和 2，最后提交以完成合并。

合并完成！你甚至可以检查两件事情来确定:`git status`来表明我们在`master`分支上。要查看我们的`line 3`文本从`morelines`分支合并到`conflict.txt`，您可以运行`cat conflict.txt`。

## 包装完毕

解决合并冲突:

1.  `git merge branchname`
2.  git 会告诉你有冲突(如果有的话)
3.  打开并编辑冲突的文件，并合并更改。删除 git 放入的引用和分隔符。
4.  保存文件。
5.  使用`git add`进行编辑。
6.  使用`git commit`完成合并过程。
7.  完成了。

合并冲突可能不止这些，尤其是当把别人的工作合并到你的工作或类似的事情中时。更多的交流可能是必要的等等。但我希望这有助于了解基本情况。

### 引用一些命令

下面是我们使用的一些命令的简短定义。Git 命令以`git`开头:

| 命令 | 它的作用 |
| --- | --- |
| `echo "text" > filename` | **将**“文本”写入文件`filename` |
| `echo "text" >> filename` | **将**“文本”添加到文件`filename`(注意两个箭头`>>`) |
| `git add .` | 在当前目录中暂存更改 |
| `git commit -am "message"` | 在项目中存放所有变更(不考虑目录)的快捷方式，并提交一条消息。相当于`git add -A; git commit -m "message"` |
| `git checkout -b branchname` | 创建一个分支的快捷方式(如果它不存在)，然后检查它。相当于`git branch branchname; git checkout branchname` |