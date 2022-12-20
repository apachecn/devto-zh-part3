# 使用“Git fsck”恢复丢失删除的“Git stash”

> 原文：<https://dev.to/egitsptr/recover-lost-deleted-git-stash-using-git-fsck-1e2l>

1-在 Terminal / Git Bash 中打开存储库目录。

`git fsck | awk ‘{print $3}’ > ids.txt
cat ids.txt | xargs git show > logs.txt`

2-然后打开`​logs.txt`，找到被删除的藏匿点`id`。

```
example:  
commit a31b0a30701933cfb3d8a55cd4b96320865ae558  
Merge: c465de7 57a3521  
Author: Egit Saputra \  
Date: Tue Feb 19 14:37:29 2019 +0900  
On development: deleted stash test

```

3-用`git stash apply`恢复到当前活动分支。
T3`git stash apply a31b0a30701933cfb3d8a55cd4b96320865ae558`