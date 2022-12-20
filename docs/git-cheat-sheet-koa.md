# Git 备忘单

> 原文：<https://dev.to/louhayes3/git-cheat-sheet-koa>

# 团队中无错 Git 的最佳命令和步骤。

我在工作中经常使用这个。

我看到过项目失败，当我们没有这个地方和松散的动力时，一些团队从来没有掌握 GIT 的窍门，因为合并错误和问题是很容易避免的。

几年前，在我的 UNH 训练营，我看到一些团队对 git 失去了信心。

在最近的 [MomsCanCode](https://dev.to@MomsCanCodePgh) 黑客马拉松上，我在为期一周的项目的第一天分享了这一点，我们实现了无缝的代码共享，团队的大多数人以前从未使用过 git。我们的组长 [@kudacoder](https://dev.to@kudacoder) 用这个然后做了一个 [Git 视频](https://www.useloom.com/share/b4c036c2af1b47489667d26b14cba9ed)给新手程序员补缺。这些加在一起是无价的。我们凭借名为 [RealUp](https://realupapp.herokuapp.com) 的冒名顶替综合症应用赢得了黑客马拉松，我相信能够轻松共享代码是一个关键因素。

这里假设您已经在本地安装了 git，并且已经创建了一个 [git repo](https://www.youtube.com/watch?v=LR5BYZjuXMU) 或者有一个 repo 要从中克隆。

这是为你的第一个团队项目做准备，通过你自己的编码和 git 提交。

仅供参考-分叉只有在您不是合作者的情况下才有必要。
在你从 github 桌面 GUI
中分叉出回购协议([https://help.github.com/articles/fork-a-repo/](https://help.github.com/articles/fork-a-repo/)

```
git clone -v <GitForkedRepo> or <AnyRepoName> 
```

从您的项目目录中，

创建一个. gitignore 文件，至少有一行，如

```
/node_modules 
/cypress 
```

添加任何其他大型 npm 库，以及您不希望放在团队共享 git repo 上的任何凭证 env 文件。

```
git status 
```

将显示你在正确的分支，这应该是你的分支。

在使用
开始任何更改之前，请务必下载最新版本的 master

```
git checkout master 
```

```
git pull 
```

现在你在最新的版本上，但是你不能在 master 中编码，所以创建或者如果已经有切换到你的分支

从主服务器切换到分支服务器(如果不存在，则创建分支服务器)

```
git checkout -b your-branch 
```

或者如果确实存在，你只需要切换到你的分支…

```
git checkout your-branch 
```

更改代码后。

```
git add . 
```

这将添加您的所有更改
您也可以

```
git add file.ext file2.ext 
```

仅添加特定的文件(我建议在 90%的情况下这样做，以避免意外添加大量的库，或者检查您是否只更改了想要更改的文件)。

```
git commit -m “desc of changes to be specific as possible” 
```

```
Git push origin <your branch> 
```

千万不要推给师傅！！

现在在浏览器中转到 GitHub repo，创建一个 pull 请求。在这里，您将自动获得通知/检查冲突。如果没有冲突，合并到 master，您就完成了。
(创建拉式请求的步骤是…另一个文档！).

在浏览器上合并到母版后…

返回到项目文件夹的终端。

```
git Status 
```

```
git checkout master 
```

这真的很重要-你必须拉下最新的 master，以查看你的新变化，新合并到 master 中，并确保你有一个工作的应用程序，否则你可能会引入错误。另外，如果你不这样做，你将会比其他人使用更老的版本！
这将切换到 masterbranch

但是你的可视化代码中的代码还没有切换！！！！这可能会令人困惑，并导致人们丢失更改，因为他们认为他们有最新的主版本，但他们没有。

```
git pull 
```

让母版中的内容显示在可视代码中，这样您就不会提交旧版本的代码

当您签出 master 时，您还必须进行 pull(仅供参考，pull 等同于 git Fetch 后跟 git Merge)。

这听起来很复杂，但相信我，这是可行的！！我们可能需要使用更多的命令，但这是我每天的团队编码圣经！

从 git 结帐主机和 git 拉取开始。
创建一个新分支(如步骤 4)或

```
git checkout <previously used branch> 
```

```
git rebase master 
```

现在是最后的检查:在开始任何新的开发工作之前，你需要重新测试你的应用程序，以确保一切正常。很多时候，人们不这样做，对于团队中的其他人来说，合并和应用程序失败了。

## 其他有用的命令

要删除刚刚通过 git add 添加的文件。

```
git rm -f auth.json 
```

(这只会将文件从暂存提交列表中取出！)

要签出团队成员远程分支:

```
git fetch origin 
```

```
git branch -v -a 
```

```
git checkout -b branchname origin/branchname 
```

如果您需要回滚合并，即在您的本地独立分支中的 git rebase master 之后

```
git checkout master 
```

```
git reset --merge 
```

### 查看所有版本

```
git branch -va 
```

### 要删除本地 GIT 分支，我们可以尝试以下命令之一:

```
git branch -d branch_name
git branch -D branch_name 
```

### 要删除一个远程分支可以使用以下命令:

```
git push <remote_name> --delete <branch_name> 
```

## 用上游分支/分叉分支重置您的分支

首先将远程分叉分支作为上游添加到您的远程分支。

```
git checkout <your-origin-branchname> (e.g. git checkout master) 
```

切换到分支“您的原始分支”
您的分支是最新的“原始/您的原始分支”。

```
git remote add upstream <remote branch url> e.g. https://github.com/cypress-io/cypress.git 
```

将远程分支作为上游添加到您的回购中，请检查此选项:

```
git remote -v 
```

你应该看到:

```
git remote -v
origin  https://github.com/louise-hayes/Project-3-Speech.git (fetch)
origin  https://github.com/louise-hayes/Project-3-Speech.git (push)
upstream        https://github.com/peraltot/Project-3-Speech.git (fetch)
upstream        https://github.com/peraltot/Project-3-Speech.git (push) 
```

```
git pull upstream <your-origin-branch> 
```

从上游更新您的分支

检查所有分支#以确保所有分支都在同一个 git#上！

```
git branch -av 
```

你会看到:你的主人(6266a2b) ==远程/原点/主人(6266a2b)。

```
git branch -av
* master                          6266a2b Update README.md
  remotes/origin/HEAD             -> origin/master
  remotes/origin/master           6266a2b Update README.md
  remotes/origin/textfile-working ead5671 added body-parser, jquery for  calls to fix /saved POST
  remotes/upstream/master         6266a2b Update README.md 
```

要在事后更改提交:

```
git commit --amend 
```

要从 git add 中卸载文件(与 git add 相反)
运行

```
git status 
```

应该会告诉你如果你想拆散文件该怎么做

选项 1:

```
git rm /path/to/file 
```

选项 2:

```
git reset HEAD /path/to/file 
```