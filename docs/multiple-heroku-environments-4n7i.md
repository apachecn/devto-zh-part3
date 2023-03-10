# 多重 Heroku 环境

> 原文：<https://dev.to/scottw/multiple-heroku-environments-4n7i>

当 Heroku 上的一个应用程序有多个环境时，您执行的每个命令都要求您在应用程序名称中传递-a(–app)标志。Heroku 应用程序的名字需要是独一无二的，不仅对你来说如此，对整个 Heroku 来说也是如此。这意味着应用程序名称可能不会像你希望的那样容易记住。KickoffLabs 实际上被分成三个独立的应用程序，这使事情变得更加复杂(总共 6 个应用程序名称)。

除了使用-a 标志，还可以通过–remote 标志使用 git 远程名称。

远程名称只需要在您的应用程序中是唯一的。这意味着您可以通过使用自己的 git 远程名称来控制这种疯狂。默认情况下，Heroku 会创建一个名为`heroku`的遥控器。您可以使用`git remote rename`命令将您的遥控器重命名为更一致的名称

```
git remote rename original_name new_name 
```

有了这些知识，您可以为您的遥控器创建更一致的名称。我一般用`production`和`staging`去。

接下来，我将以下内容添加到我的 zsh 配置文件中:

```
function hs(){
  heroku "$@" --remote staging
}

function hp(){
  heroku "$@" --remote production
} 
```

现在，我想对产品做的任何事情都很简单:

```
hp config:set VAR=1234 
```

在 staging 中也可以这样做:

```
hs config:set VAR=1234 
```