# 使用 SVN 管理 GitHub 中的代码片段！

> 原文：<https://dev.to/aurelkurtula/using-svn-to-manage-your-code-snippets-in-github-29fa>

自从我学习 Github 以来，我一直在寻找这样的能力，但我从未找到任何解决方案，我甚至不认为这是可能的。

每次我对我的工作流程感到沮丧时，我都会结束对同一主题的研究，并最终找到解决方案

**问题**:当开始一个新项目的工作时，你希望能够将相同的 SASS 目录粘贴到你的项目中，你可能有可重用的组件、字体等等。把它们放在你机器的一个目录里。我觉得很烦。为什么要使用额外的 GUI。

**欲望:**我一直想让 GitHub 成为答案。但是当然，当我们通常使用 Git 时，它不起作用，工作流程将与我们试图解决的问题相同(但是有版本控制)

**方案:** SVN 的命令。

在写这篇文章之前，我应该更多地了解 SVN，但我从中得到了我想要的，所以我写这篇文章是为了不忘记我创建的这个设置，希望它能帮助你们中的一些人。

使用 SVN 命令，Github 库就像一个文件存储站点，我可以导入和导出我想要的资源。

我在 Github 中做了一个私有的资源库，并推送了一些初始资源

https 可以是:`https://github.com/aurelkurtula/starters.git`

我可以从我的电脑上删除该项目，但仍然可以用`svn import`和`svn export`从该项目中导入和导出任何内容

进入您想要导入的资源所在的目录并运行

```
svn import -m -F "readme.md" https://github.com/aurelkurtula/starter.git/trunk/readme.md 
```

在那里，位于您计算机中的文件`readme.md`被推送到 Github 存储库。

与导出资源完全相同的事情

```
svn export https://github.com/aurelkurtula/starter-folders.git/trunk/readme.md 
```

请注意 URL 模式:克隆 HTTPS 地址，后跟`/trunk/`和您想要导出或导入的文件或文件夹。

### 使用终端/外壳方法作为快捷方式

我真的很喜欢上面的内容，我可能会更多地阅读 SVN，但现在它确实起作用了，我的用途很清楚，用于存储库，我真的不关心提交日志等等，只是一个下拉框类型的功能，我可以从我的终端管理。

我使用 [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) 框架来管理我的 zsh 配置。对于任何会重复的命令，我总是在 zsh 配置中添加一个方法:

```
svnpush(){
    # file = $2
    # path = $1
    svn import -m -F $2 $1/trunk/$2
} 
```

现在，如果我想将任何文件/文件夹导入到我的任何存储库中，我只需运行`svnpush https//:github...reponame.git localFoder`

```
svndelete(){
    # file = $2
    # path = $1
    svn delete $1/trunk/$2 -m 'deleted $1'
} 
```

从存储库中删除资源的能力是 import，这是“修改”现有资源的唯一方法。

```
svnexport(){
    # file = $2
    # path = $1
    svn export $1/trunk/$2
} 
```

终于出口了。

### 结论

我对此非常兴奋，因为老实说，我已经尝试了这么多事情，它们似乎都让我分心。

这让我想起了我的,我已经用了很多年了，仍然发现它比我试过的其他方法都简单。

如果你用这个代替 git 来写你的代码，肯定会有缺点。我想强调的是，这只是一个向其中倾倒资源的容器