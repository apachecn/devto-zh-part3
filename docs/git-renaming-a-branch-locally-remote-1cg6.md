# git——在本地和远程重命名分支

> 原文：<https://dev.to/stephencweiss/git-renaming-a-branch-locally-remote-1cg6>

要重命名分支的名称，有四个潜在的步骤:

1.  签出现有分支(您要重命名的分支):`git checkout <old_name>`
2.  通过移动来重命名本地分支:`git branch -m <new_name>`
3.  如果您已经将`<old_name>`分支推送到远程存储库，删除`<old_name>`远程分支:`git push origin --delete <old_name>`
4.  最后按下`<new_name>`本地分支，复位上游分支:`git push origin -u <new_name>`

就是这样。至此，您已经成功地重命名了本地和远程 Git 分支。

# 关于`-m`旗的一个说明:

类似于 Bash 中的`mv`命令，`-m`标志用于移动。因为你在一个分支上，Git 推断正在移动的分支，但是，你可以明确地用`git checkout -m <oldbranch> <newbranch>`合并步骤 1 和 2。

# 补充阅读

*   [如何重命名本地和远程 Git 分支](https://linuxize.com/post/how-to-rename-local-and-remote-git-branch/)
*   [10 个你希望存在的极其有用的 Git 命令——以及它们的替代品](https://dev.to/datreeio/10-insanely-useful-git-commands-you-wish-existed-and-their-alternatives-8e6)