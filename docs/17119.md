# 将 Git 子模块库更改为其他 URL/分支

> 原文：<https://dev.to/serhatteker/changing-git-submodule-repository-to-other-url-branch-356p>

### 列出详细内容。gitmodules 文件

```
$  git config --file=.gitmodules -l 
```

Enter fullscreen mode Exit fullscreen mode

该命令列出当前存储库中存在的所有子模块及其路径、URL 位置和它在存储库中映射的分支。

输出如下:

```
submodule.themes/hugo-coder.path=themes/docker
submodule.themes/hugo-coder.url=https://github.com/user/coder.git
submodule.themes/hugo-coder.branch=docker 
```

Enter fullscreen mode Exit fullscreen mode

### 编辑子模块的网址

该命令将编辑子模块的 URL，并将其放置在指定的新 URL 存储库中。

```
$  git config --file=.gitmodules submodule.Submod.url https://github.com/username/ABC.git 
```

Enter fullscreen mode Exit fullscreen mode

### 编辑子模块分支

```
$  git config --file=.gitmodules submodule.Submod.branch development 
```

Enter fullscreen mode Exit fullscreen mode

该命令将编辑子模块的分支`docker`，并将其放入指定的分支`development`。

### 同步和更新子模块

```
$  git submodule sync
$  git submodule update --init --recursive --remote 
```

Enter fullscreen mode Exit fullscreen mode

这些命令将成功地将子模块同步和更新到新指定的分支或 URL。