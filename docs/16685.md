# 如何在 git 中重命名本地和远程分支

> 原文：<https://dev.to/paramharrison/how-to-rename-a-local-and-remote-branch-in-git-12mh>

在进入细节之前，让我们看看为什么我们要重命名一个分支的一些原因，

*   分行名称中的错别字
*   创建了一个有名字的分支，然后意识到，这个分支做的不止这些(例如，`add-client`到`crud-client`)
*   创建了一个虚拟名称(例如，`yet-another-branch`到`fix/reports`)
*   还可以添加关于分支的更多细节(例如，`feat/branch-name`、`fix/branch-name`)

等等

### 如何在本地重命名分支

在本例中，我们将把分支从`old-reports`重命名为`new-reports`。

```
## go to the old branch
git checkout old-reports

## Rename the branch to a new name
git branch -m new-reports 
```

在 git 表示中，将这个旧分支移动到新分支，并移除旧分支。

### 如何远程重命名分行

老支被推到远程原点怎么办？非常简单，首先删除旧的远程分支，然后将新的本地分支推送到远程源。

对于删除远程分支，

```
git push origin -d old-reports 
```

这里`-d`或`--delete`将远程删除旧分支。如果你指定`-D`，它将只删除本地而不是远程原点。

然后将当前分支推到原点，

```
git push -u origin new-reports 
```

希望您学会了在 git 存储库中重命名本地和远程分支的技巧😎