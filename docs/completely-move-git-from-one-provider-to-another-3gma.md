# 将 GIT 从一个提供者完全转移到另一个提供者。

> 原文：<https://dev.to/sakko/completely-move-git-from-one-provider-to-another-3gma>

我在谷歌上搜索了很多，看看如何将 git 存储库从一个远程主机移动到另一个。

在克隆中，我的目标是

*   确保克隆源远程上的所有分支
*   确保源远程上的所有标签都被克隆

克隆之后，我的目标是

*   将所有分支推送到另一台远程主机
*   将所有标签推送到另一台远程主机

做到这一点的方法很简单。我已经在我的 20 多个仓库中这样做了。

*   Bitbucket -> Github
*   位桶->另一个位桶
*   Github -> Bitbucket
*   Github ->另一个 Github
*   Github -> Gitlab
*   Bitbucket -> Gitlab

我们开始吧

*   注意:
*   `TEMP_REPO_FOLDERNAME`只是一个文件夹名。什么名字都可以。
*   `SOURCE_REMOTE_URL`是一个源(原始)git 存储库 url。它可以是 http 或 ssh。
*   示例:`https://github.com/SaKKo/SKTipAlertView.git`
*   `NEW_REMOTE_URL`是您想要将存储库移动到的位置。(必须是空白回购)。
*   示例:`https://sakko@bitbucket.org/sakko/SKTipAlertView.git`

让我们开始吧(用你想要的替换`TEMP_REPO_FOLDERNAME` `SOURCE_REMOTE_URL` `NEW_REMOTE_URL`，然后运行代码)。

```
mkdir TEMP_REPO_FOLDERNAME # create a new folder
cd TEMP_REPO_FOLDERNAME
git init --bare .git # create a bare repo
git remote add origin SOURCE_REMOTE_URL # add a remote
git fetch origin refs/heads/*:refs/heads/* # fetch heads
git fetch origin refs/tags/*:refs/tags/* # fetch tags
git init # reinit work tree
git checkout master # checkout a branch
git remote rm origin # remove remote repo
git remote add origin NEW_REMOTE_URL # new remote url
git push -u origin master # now push master
git push --all # now push all other branches 
```

搞定了。
希望你能找到这个有用的:D