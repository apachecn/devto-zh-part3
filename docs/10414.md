# git 中修改的 yaml 文件的简单 linter

> 原文：<https://dev.to/melezhik/simple-linter-for-yaml-files-modified-in-git-35mf>

# Yaml 很脆弱

如果你经常编辑 yaml 文件，你可能会知道 YAML 语法太脆弱了，意外的错误很容易侵入并破坏你的代码。

这里有[YAML-lint](https://github.com/melezhik/sparrow-plugins/tree/master/yaml-lint)[sparrow 6](https://github.com/melezhik/Sparrow6)插件来拯救。

每当您在 git 中更改 yaml 文件时，通过 yaml python 模块 lint 它们。

Yaml-lint 获取所有 git `A`或`M`文件，并为它们运行 yaml linter。

# 安装插件

```
$ tom --profile yaml
install yaml@yaml-lint ... 
```

Enter fullscreen mode Exit fullscreen mode

# 使用插件

只需编辑您想要编辑的任何文件:

```
$ nano main.yml # already added to git modified file - `M`
$ nano foo.yml   
$ git add foo.yml # newly added to git file - `A` 
```

Enter fullscreen mode Exit fullscreen mode

并在提交前运行 linter:

```
$ tom yaml-lint

gimy@77b3f88acea1:~/projects/LFA-Applications$ tom yaml-lint
19:41:47 03/13/2019 [repository] index updated from file:///home/melezhik/projects/repo/api/v1/index
19:41:48 03/13/2019 [lint modified files] python -c 'import yaml,sys;yaml.safe_load(sys.stdin)' < main.yml
19:41:48 03/13/2019 [lint modified files] python -c 'import yaml,sys;yaml.safe_load(sys.stdin)' < foo.yml 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以提交了:

```
git commit -a -m "we have a good yaml" 
```

Enter fullscreen mode Exit fullscreen mode