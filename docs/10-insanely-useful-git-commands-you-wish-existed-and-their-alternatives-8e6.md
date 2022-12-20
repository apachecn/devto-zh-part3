# 你希望存在的 10 个非常有用的 Git 命令——以及它们的替代品

> 原文：<https://dev.to/datreeio/10-insanely-useful-git-commands-you-wish-existed-and-their-alternatives-8e6>

## 有一个 git 命令可以做到这一点

Git 命令并不总是直观的。如果是的话，我们将拥有这 10 个命令。它们对于完成一些常见的任务非常有用，比如创建或重命名 git 分支、删除文件和撤销更改。

对于 wishlist 中的每个 git 命令，我们将向您展示实际存在的命令，您可以使用这些命令来完成相同的任务。如果你还在学习 Git，这个列表读起来像一个教程，值得作为一个备忘单保存。

* * *

## # 9–git 创建分支:使用 git 签出创建一个新分支

创建新分支的最快方法是从 git 终端实际创建。这样你就不用用 GitHub UI 了，比如你用 GitHub 做版本控制。

这个命令实际上存在于 git 中，只是用了一个不同的名字——`$ git checkout`。

### 如何用 git checkout 创建分支:

单行命令:`$ git checkout -b <branch-name> master`

```
-> commit-test git:(master) $ git checkout -b feature-branch master
-> commit-test git:(feature-branch) $ 
```

Enter fullscreen mode Exit fullscreen mode

**Git 提示**:就像[提交消息](https://datree.io/blog/git-commit-message-conventions-for-readable-git-log/)一样，为 git [分支](https://stackoverflow.com/questions/273695/what-are-some-examples-of-commonly-used-practices-for-naming-git-branches)制定一个命名约定是一个很好的最佳实践。

* * *

## # 8–git 强制拉取:用 git 拉取覆盖本地

你发现你所做的改变似乎与上游的改变相冲突。此时，您决定覆盖您的更改，而不是保留它们，所以您执行了`$ git pull`操作，并得到了这个错误消息:

```
-> commit-test git:(master) $ git pull
Updating db40e41..2958dc6
error: Your local changes to the following files would be overwritten by merge:
README.md
hint: Please, commit your changes before merging.
fatal: Exiting because of unfinished merge. 
```

Enter fullscreen mode Exit fullscreen mode

### 如何用 git pull 覆盖本地更改:

1.  隐藏本地更改:`$ git stash`
2.  从远程获取更改:`$ git pull`

```
-> commit-test git:(master) $ git stash
Updating db40e41..2958dc6
Saved working directory and index state WIP on master: d8fde76 fix(API): remove ‘test’ end-point
-> commit-test git:(master) $ git pull
Auto-merging README.md
Merge made by the ‘recurive’ strategy.
README.md     | 1 +
ENDPOINT.js    | 3 ++–
2 files changes, 3 insertions(+), 1 deletions(-) 
```

Enter fullscreen mode Exit fullscreen mode

**Git 提示**:如果你想取回你的修改，只需:`$ git stash apply`

* * *

## # 7–git 删除未跟踪文件:从工作树中删除未跟踪文件

当您自己的存储库本地副本中有不必要的文件和目录，并且您想要删除这些文件时，与忽略它们相反(使用。gitignore)，您可以使用 git clean 删除所有不由 git 跟踪的文件。

### 如何删除未被跟踪的文件和目录:

1.  从试运行开始，看看将删除哪些内容:`$ git clean -n -d`
2.  确定之后，运行带有“-f”标志的 git clean 命令:`$ git clean -f -d`

```
-> commit-test git:(master) $ git clean -n -d
Would remove dontTrackDir/untracked_file1.py
Would remove untracked_file2.py
-> commit-test git:(master) $ git clean -f -d
Removing dontTrackDir/untracked_file1.py
Removing untracked_file2.py 
```

Enter fullscreen mode Exit fullscreen mode

**Git 提示**:与其不跟踪文件，一个好的做法是首先防止这些文件被[使用。gitignore](https://docs.datree.io/docs/include-mandatory-files-gitignore) 文件。

* * *

## # 6–git 卸载:从索引中卸载文件

当您将文件(`$ git add`)添加到工作树时，您正在将它们添加到暂存区域，这意味着您正在暂存它们。如果您希望 Git 停止跟踪工作树上的特定文件，您需要将它们从您的 stage 文件(。git/index)。

### 如何从索引中卸载文件:

*   保留文件，但将其从索引中删除:`$ git rm --cached <file-name>`

```
-> commit-test git:(master) $ git rm –cached unstageMe.js
rm unstageMe.js 
```

Enter fullscreen mode Exit fullscreen mode

*   要保持整个工作树不变，请不要存放所有文件(清空您的索引):`$ git reset`

```
-> commit-test git:(master) $ git reset 
```

Enter fullscreen mode Exit fullscreen mode

**Git 提示**:你也可以取消跟踪已经添加到 Git 库[的文件。gitignore](http://www.codeblocq.com/2016/01/Untrack-files-already-added-to-git-repository-based-on-gitignore/) 。

* * *

## # 5–git 撤销合并:在合并发生后中止(取消)合并

有时你会遇到这样的情况(我们都经历过)，你合并了分支，并且意识到你需要撤销合并，因为你不想发布你刚刚合并的代码。

### 如何中止(取消)合并并维护所有已提交的历史:

1.  签出至总分行:`$ git checkout master`
2.  运行 git log 并获取合并提交的 id:`$ git log --oneline`
3.  按提交 id 还原合并:`$ git revert -m 1 <merge-commit-id>`
4.  提交恢复并将更改推送到远程存储库。你可以开始摆出扑克脸，假装“什么都没发生”。

```
-> commit-test git:(master) $ git log –oneline
812d761 Merge pull request #524 from datreeio/DAT-1332-resolve-installation-id
b06dee0 feat: added installation event support
8471b2b fix: get organization details from repository object

-> commit-test git:(master) $ git revert -m 1 812d761
Revert “Merge pull request #524 from datreeio/DAT-1332-resolve-installation-id”
[master 75b85db] Revert “Merge pull request #524 from datreeio/DAT-1332-resolve-installation-id”
1 file changed, 1 deletion(-)
-> commit-test git:(master) $ git commit -m “revert merge #524”
-> commit-test git:(master) $ git push 
```

Enter fullscreen mode Exit fullscreen mode

**Git 提示**:与恢复合并不同，处理拉请求，建立或改进你的[代码审查](https://phauer.com/2018/code-review-guidelines/)过程可以降低错误合并的可能性。

* * *

## # 4–git 移除文件:从远程提交中移除文件

您希望删除远程上的一个(或多个)文件，可能是因为它已被弃用，或者因为该文件本来就不应该在那里。所以，您想知道，从远程 git 存储库中删除文件的协议是什么？

### 如何从提交中删除文件:

1.  移除您的文件:`$ git rm <file-A> <file-B> <file-C>`
2.  提交您的更改:`$ git commit -m "removing files"`
3.  将您的更改推送到 git: `$ git push`

```
-> commit-test git:(delete-files) $ git rm deleteMe.js
rm ‘deleteMe.js’
-> commit-test git:(delete-files) $ git commit -m “removing files”
[delete-files 75e998e] removing files
1 file changed, 2 deletions(-)
delete mode 100644 deleteMe.js
-> commit-test git:(delete-files) $ git push 
```

Enter fullscreen mode Exit fullscreen mode

**Git 提示**:当一个文件从 Git 中移除，并不意味着它从历史中被移除。该文件将“活”在存储库历史中，直到该文件将被[完全删除](https://help.github.com/en/articles/removing-sensitive-data-from-a-repository)。

* * *

## # 3–git 取消提交:撤消上一次提交

你做了承诺，但现在你后悔了。也许你不小心[提交了秘密](https://datree.io/blog/secrets-management-git-version-control/)——这不是一个好主意——或者也许你想在你的代码变更中添加更多的测试。这些都是撤销你上一次承诺的正当理由。

### 如何取消提交(撤销)上一次提交:

*   要保留您想要撤消的提交中的更改:`$ git reset --soft HEAD^`
*   要销毁您要撤消的提交中的更改:`$ git reset --hard HEAD^`

```
-> commit-test git:(undo-commit) $ git commit -m “I will regret this commit”
[undo-commit a7d8ed4] I will regret this commit
1 file changed, 1 insertion(+)
-> commit-test git:(undo-commit) $ git reset –soft HEAD^
-> commit-test git:(undo-commit) $ git status
On branch undo-commit
Changes to be committed:
(use “git reset HEAD <file>…” to unstage)

    modified: README.md 
```

Enter fullscreen mode Exit fullscreen mode

**Git 提示** : Git [预提交钩子](https://gist.github.com/eyarz/64770d343b9cab442b257869f497af9e)是一个内置特性，允许你定义在每次提交前自动运行的脚本。使用它来减少取消提交的需要。

* * *

## # 2–分支之间的 git 差异

当您处理多个 git 分支时，能够比较同一存储库中两个不同分支之间的差异是很重要的。您可以使用`$ git diff`命令来完成此操作。

### 如何得到两个分支之间的差异:

*   找出两个分支顶端的差异:`$ git diff branch_1..branch_2`
*   从共同祖先提交中产生两个分支之间的差异:`$ git diff branch_1...branch_2`
*   比较分支之间的文件:`$ git diff branch1:file branch2:file`

```
-> commit-test git:(diff-me) $ git diff master..diff-me
diff –git a/README.md b/README.md
index b74512d..da1e423 100644
— a/README.md
+++ b/README.md
@@ -1,2 +1,3 @@
# commit-test
-Text on “master” branch
+Text on “diff-me” branch 
```

Enter fullscreen mode Exit fullscreen mode

**Git 提示** : [diff-so-fancy](https://github.com/so-fancy/diff-so-fancy) 是一个很棒的开源解决方案，可以让你的 diff 变得更容易被人理解。

* * *

## # 1–git 删除标签:从分支中删除标签

在“错误”发布的情况下，您可能不希望有人意外地使用链接到这个标签的发布。最好的解决方案是删除标签，并删除发行版和其相关标签之间的连接。

### 如何通过从分支中删除标签来删除标签:

1.  如果你有一个远程标签要删除，而你的远程是 origin，那么简单:`$ git push origin :refs/tags/<tag-name>`
2.  如果还需要本地删除标签:`$ git tag -d <tag-name>`

```
-> commit-test git:(delete-tag) $ git push origin :refs/tags/v1.0.0
To github.com:datreeio/commit-test.git
– [deleted]         v1.0.0
-> commit-test git:(delete-tag) $ git tag -d v1.0.0
Deleted tag ‘v1.0.0’ (was af4d0ea) 
```

Enter fullscreen mode Exit fullscreen mode

**Git 提示**:不确定什么时候或者为什么要使用标签？[阅读这里](https://medium.com/@kevinkreuzer/the-way-to-fully-automated-releases-in-open-source-projects-44c015f38fd6)了解更多(TL；DR:自动释放)

* * *

## # 0–git 重命名分支:更改分支名称

正如我提到的，拥有一个分支命名约定是一个很好的实践，应该作为编码标准的一部分被采用，并且它在支持 git 工作流的自动化方面特别有用。但是，在已经将代码推送到分支之后，当您发现您的分支名称不符合约定时，该怎么办呢？不要担心，您仍然可以重命名您的分支机构。

### 分支机构名称创建后如何重命名:

1.  签出到需要重命名的分支:`$ git checkout <old-name>`
2.  本地重命名分支名称:`$ git branch -m <new-name>`
3.  从远程删除旧分支:`$ git push origin :<old-name> <new-name>`
4.  将上游分支重置为新的分支名称:`$ git push origin -u <new-name>`

```
-> commit-test git:(old-name) $ git branch -m new-name
-> commit-test git:(new-name) $ git push origin :old-name new-name
Total 0 (delta 0), reused 0 (delta 0)
To github.com:datreeio/commit-test.git
–  [deleted]             old-name
* [new branch]      new-name -> new-name
-> commit-test git:(new-name) $ git push origin -u new-name
Branch new-name set up to track remote branch new-name from origin.
Everything up-to-date 
```

Enter fullscreen mode Exit fullscreen mode

**Git 提示**:想要确保所有的分支名称都遵循您的惯例吗？设置 Git 强制的[分支命名策略](https://docs.datree.io/docs/branch-name)。

* * *

## 接下来是什么？

找到了一些有用的命令？还可以为他们设置[别名命令](https://github.com/GitAlias/gitalias)！
本帖相关别名由 [@mfrata](https://dev.to/mfrata) 在[他的评论](https://dev.to/mfrata/comment/aab8)中提供——感谢他:)

* * *

最初发布于[https://datree.io/resources/git-commands](https://datree.io/resources/git-commands)