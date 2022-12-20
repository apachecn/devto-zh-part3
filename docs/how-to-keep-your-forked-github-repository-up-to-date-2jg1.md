# 如何让你的分叉 GitHub 库保持最新

> 原文：<https://dev.to/wincentbalin/how-to-keep-your-forked-github-repository-up-to-date-2jg1>

### 1。克隆你的叉子:

```
git clone git@github.com:YOUR-USERNAME/YOUR-FORKED-REPO.git 
```

Enter fullscreen mode Exit fullscreen mode

### 2。从原始存储库添加远程到分叉存储库:

```
cd into/cloned/fork-repo
git remote add upstream git://github.com/ORIGINAL-DEV-USERNAME/REPO-YOU-FORKED-FROM.git
git fetch upstream 
```

Enter fullscreen mode Exit fullscreen mode

### 3。从原始回购更新您的分支，以跟上他们的变化:

```
git pull upstream master 
```

Enter fullscreen mode Exit fullscreen mode

来源:[此要诀](https://gist.github.com/KINGSABRI/b7a07fee84affc7d041acc48e9cb4a7e)