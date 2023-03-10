# 合并冲突地狱

> 原文：<https://dev.to/pencillr/merge-conflict-hell-46on>

想象一个用饭桶做成的城堡！
等一下。
这不是我一开始想说的句子。总之。

* * *

## 问题

如果你曾经在 git 中与一个团队一起工作过，你可能已经结束了在提交链上的工作。所有这些都排列得很好，准备好被推送到位于您团队的 gihub/bit bucket/Gerrit/whatever 服务器后面的公共远程存储库。
这时你的一个同事会这样说:

嘿，卡伦，你忘了在那个文件里加一个分号。您知道您在提交链中间添加的那个。”

这就是你开始结帐和重新定基的地方，并且可能在合并冲突的地狱中结束。

### 如果你真的不明白这里发生了什么，这篇文章是给你的！

让我们用一个非常简单的例子来发现问题。这就是城堡出现的地方。

* * *

## 遇见你的 git 城堡！

我们将创建我们的 **git 城堡**就像这样简单:

```
mkdir git-castle
cd git-castle
git init
touch castle.yaml 
```

Enter fullscreen mode Exit fullscreen mode

> 正如你可能认为的，我们的 git castle 将**只是一个文件，代表一段代码**,我们将对其进行编辑和提交，直到我们被合并冲突所窒息。

让我们创建我们的**代码表示**，作为一个 yaml 文件，描述我们城堡的参数。我们还将创建一个提交链来处理。

**我们的代码表示文件:**

```
# This is my git castle!
--------
location:   Camelot
built:      2010-10-10
owner:      Penny Bunn

rooms:
    - room_name: Living Room

    - room_name: Kitchen 
```

Enter fullscreen mode Exit fullscreen mode

让我们通过添加这个文件来创建我们的初始提交。

```
git add castle.yaml
git commit -m "Initial Commit" 
```

Enter fullscreen mode Exit fullscreen mode

现在我们添加一些参数，**创建第二个提交**作为**“添加房间参数”** :

```
# This is my git castle!
--------
location:   Camelot
built:      2010-10-10
owner:      Penny Bunn

rooms:
    - room_name: Living Room
      size:      30m2
      windows:   2
      doors:     2

    - room_name: Kitchen
      size:      15m2
      windows:   2
      doors:     1 
```

Enter fullscreen mode Exit fullscreen mode

现在我们添加另一个房间，以及一个名为**【添加浴室】** *:
的**第三次提交**

```
 - room_name: Bathroom,
      size:      10m2
      windows:   1
      doors:     1 
```

Enter fullscreen mode Exit fullscreen mode

酷！我们有一个三提交长提交链！

* * *

## 回去修理某物

但是没有！我们突然发现，根据我们的计划，客厅应该有三扇窗户！

当然，在最初的“添加房间参数”提交上修复它会更好。尤其是现在，我们在将提交链推送到远程主机之前就注意到了我们的错误。

这就是**交互式重定基础**的由来。

> 在 git 中，`rebase`命令用于将提交重新应用到所选的基本提示。幸运的是，它有一个交互式标志，有了它，您可以简单地返回到一个选定的提交，编辑它，并重新应用所有后续的提交，直到掌握。

`git rebase -i ${base_tip}`

作为一个`base_tip`，我们将选择任何比我们想要编辑的点*更早*(更早)的提交。当你有一个远程回购，该点将是远程主，这也将是你的提交链的基础，所以最好在此基础上。现在，在我们的例子中，我们将只使用初始提交的散列。

> 我们可以用`git log`或`gitk`或`git rev-parse HEAD~$num`来计算，其中$num 是我们与初始提交的*距离*。

点击回车后，我们会看到一个**交互式编辑器窗口**。我们将看到我们选择的`base_tip`之上的所有提交，并且我们可以通过简单地用`e`替换所选择的`pick`来选择编辑哪个提交。像这样:

```
e ddbef6b Add room parameters
pick 607bd93 Add bathroom

# Rebase cf0e9f4..550ab2c onto cf0e9f4 (3 commands)
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
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom. 
```

Enter fullscreen mode Exit fullscreen mode

> 如果您从未在这里或提交时看到过这些交互式窗口，并且您也不知道 git 的默认编辑器(emacs 或 vim ),那么您可能会在编辑和保存文档时遇到一些麻烦。如果是这种情况，您可能需要研究一下 vim/emacs。

在成功编辑之后，我们可以用`gitk`或`git rev-parse HEAD`检查我们是否确实在我们想要编辑的提交处。我们将这样做我们的修复:

```
rooms:
    - room_name: Living Room
      size:      30m2
      windows:   2
      doors:     2 
```

Enter fullscreen mode Exit fullscreen mode

通过`git add`和`git commit --amend`，我们可以将更改添加到“添加房间参数”提交中。在这之后，我们可以重新提交“添加浴室”并通过一个简单的命令检查它:`git rebase --continue`

> 实际上，如果我们做了一个`git status`操作，甚至 git 本身也在警告我们，我们正处于一个交互式的 rebase 会话中，我们应该中止或继续它。

## 现在一切都显得那么简单，但仍然...

让我们把浴室变得更大，有两个窗户，还增加一个餐具室，并提交一个名为“调整浴室大小并增加餐具室”的命令

```
 - room_name: Bathroom,
      size:      20m2
      windows:   2
      doors:     1

    - room_name: Pantry,
      size:      5m2
      windows:   0
      doors:     1 
```

Enter fullscreen mode Exit fullscreen mode

现在问题来了。我们决定在城堡的这一层建造厕所，而不是浴室。为了澄清，我们决定这样做:

```
# This is my git castle!
--------
location:   Camelot
built:      2010-10-10
owner:      Penny Bunn

rooms:
    - room_name: Living Room
      size:      30m2
      windows:   3
      doors:     2

    - room_name: Kitchen
      size:      15m2
      windows:   2
      doors:     1

    - room_name: Toilet
      size:      3m2
      windows:   0
      doors:     1

    - room_name: Pantry,
      size:      5m2
      windows:   0
      doors:     1 
```

Enter fullscreen mode Exit fullscreen mode

好吧，很简单。我们知道交互式 rebase，所以我们可以在“添加浴室”提交时进行整个交换。我们简单地返回到“添加浴室”来使它成为“添加厕所”，也改变了 yaml 文件。我们相对于初始提交进行了重新调整，编辑“添加浴室”，并在浴室的位置创建厕所:

```
 - room_name: Toilet
      size:      3m2
      windows:   0
      doors:     1 
```

Enter fullscreen mode Exit fullscreen mode

把`git add`和`git commit --amend`做为“加厕所”然后简单的`git rebase --continue`。
但是这次我们得到了一个**错误** :

```
git-castle> git rebase --continue
Auto-merging castle.yaml
CONFLICT (content): Merge conflict in castle.yaml
error: could not apply 30637b3... Resize bathroom and add pantry
Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
Could not apply 30637b3... Resize bathroom and add pantry 
```

Enter fullscreen mode Exit fullscreen mode

现在，发生这种情况是因为在“调整浴室大小和增加餐具室”提交中，我们也编辑了相同的行。git 看到的是，我们在这些行上对新的“添加厕所”提交进行了编辑，然后在重置基础时，它意识到在同一行上有冲突的编辑。这很有意义，因为**git 如何知道哪个编辑应该持久化**呢？这就是为什么重定基础在这一点上停止，所以我们可以通过选择在发生冲突的提交上持久化哪个更改来编辑文件(*这次是“调整浴室大小并添加餐具室”提交*)。

如果你打开文件，你可以看到 **git 甚至插入了辅助分隔符来查看哪些内容与**有矛盾。我们进行修复，并通过`git add`和`git rebase --continue`进行重新调整。
自然地，编辑窗口会为固定提交而跳出来，我们可以将其重命名为“添加餐具室”，因为这是没有浴室大小调整后的真实内容。

沃伊拉。
检查 gitk 以确定。
`git push`
获利。