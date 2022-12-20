# Git 向分叉回购添加上游

> 原文：<https://dev.to/louhayes3/git-add-an-upstream-to-a-forked-repo-1mik>

感谢您花时间来检查这一点！请随时添加有用的意见或反馈！

这是一个小备忘单，假设您熟悉 git，安装了所有工具，并且在编辑器中打开了一个项目终端会话。

这假设您已经使用 GIT gui 派生了一个 repo。

```
git clone -v <git url> 
```

要用上游分支(分叉分支的远程分支)重新确定分支的基础，

您需要将分支作为上游添加到“远程分支”列表中，通过 git branch -av 命令将其添加到可见的远程分支列表中。

```
git checkout <your-origin-branchname> (e.g. git checkout master) 
```

切换到分支“您的原始分支”
您的分支是最新的“原始/您的原始分支”。

```
git remote add upstream <remote branch url> 

e.g. git remote add upstream https://github.com/cypress-io/cypress.git 
```

将远程分支作为上游添加到您的回购中。要检查这一点:

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

接下来，从正确的分支内部...

```
git pull upstream <your-origin-branch> 
```

这将从上游更新您的分支

检查所有分支#以确保所有分支都在同一个 git#上！

```
git branch -av 
```

你会看到:

1.  您的主机(6266a2b)匹配远程/上游/主机(6266a2b)。
2.  您的/remote/origin/master (6266a2b)与远程/上游/master (6266a2b)匹配。这 3 个 repos(本地主机、上游主机和来源主机)需要匹配。

```
git branch -av
* master                          6266a2b Update README.md
  remotes/origin/HEAD             -> origin/master
  remotes/origin/master           6266a2b Update README.md
  remotes/origin/textfile-working ead5671 added body-parser, jquery for  calls to fix /saved POST
  remotes/upstream/master         6266a2b Update README.md 
```

希望这有所帮助！