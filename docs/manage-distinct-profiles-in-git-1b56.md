# 在 Git 中管理不同的概要文件

> 原文：<https://dev.to/jygastaud/manage-distinct-profiles-in-git-1b56>

我的博客上的法文原创文章

您使用计算机工作，并且比以往任何时候都更可能使用 Git(如果不使用，您会对本文做些什么？).或许你也在同一台电脑上处理个人项目？

或者为一个开源项目做贡献，总是在同一台机器上。

您可能已经为您的机器定义了一个全局标识(名称+电子邮件),并根据上下文为每个项目重载了它。

完美！完美！

然而，尽管有这些通常的预防措施，谁从来没有以错误的身份参与过一个项目呢？有时你可能会发现得很晚，你的个人邮箱现在已经在项目日志中的*ad vitam ternam*了。

那么，我们如何才能试图限制出错的风险呢？

我们能为我们的项目自动选择正确的身份吗？

好消息是**答案是肯定的**,之后我们会看到结果。

## [T1。gitconfig 和条件包含感谢 IncludeIf](#gitconfig-and-conditional-includes-thanks-to-includeif)

就拿文件`.gitconfig`来说吧，通常存在于你的`$HOME`中。

```
[user]
    name = Jean-Yves Gastaud
    email = mon.super@mail.com

[alias]
  amend = commit --amend
  st = status
  co = checkout 
```

这个文件定义了我们的默认身份和全局别名。

由于在我们的`.gitconfig`文件中使用了`includeIf` <sup id="fnref1">[1](#fn1)</sup> 指令，我们现在可以添加条件包含链接，尤其是目录。

```
[includeIf "gitdir:/workspace/work/"]
  path = ~/.gitconfig-work

[includeIf "gitdir:/workspace/opensource/"]
  path = ~/.gitconfig-opensource

[includeIf "gitdir:/workspace/perso/"]
  path = ~/.gitconfig-perso 
```

对于每个定义的`includeIf`，我们所要做的就是创建与我们在`path`中定义的路径相对应的文件。

在`~/.gitconfig-work`文件中，我们只需添加或覆盖我们感兴趣的配置。

示例:

```
[user]
        email = my.work@mail.com

[core]
        editor = "code -w" 
```

在这个文件中，我们重载了我们的电子邮件并添加了一个默认的代码编辑器。

现在，`/workspace/work`目录下的任何文件夹都将自动受益于加载`.gitconfig`文件的配置，重载或补充`.gitconfig-work`文件的配置。

要检查您的配置，请转到目标目录下的任意目录，在我们的示例中为`/workspace/work`，并运行命令`git config -l`

您应该会看到配置文件的合并。

```
user.name=Jean-Yves Gastaud
email = my.super@mail.com
alias.amend=commit --amend
alias.st=status
alias.co=checkout
includeif.gitdir:/workspace/work/work/.path=~/.gitconfig-work
user.email=my.work@mail.com
core.editor=code -w
includeif.gitdir:/workspace/opensource/.path=~/.gitconfig-opensource
includeif.gitdir:/workspace/perso/.path=~/.gitconfig-perso
core.repositoryformatversion=0
core.filemode=true
… 
```

如您所见，这两个定义的电子邮件出现在列表中。

最后一次“发言”是正确的，因此在提交期间将考虑文件`.gitconfig-work`的值。

您还会注意到，列出了其他两个 include，但是没有添加新的变量/重载，因为它们的应用条件没有得到满足。

像以前一样，这总是给我们在存储库本地过载所有期望的配置的可能性。

通过几行文字和对你的工作目录的简单管理，你就有可能不再冒险去改变你的身份。

* * *

1.  自 Git，2.13 版(2017，5 月)。[文档包含在 Git](https://git-scm.com/docs/git-config#_includes) 中 [↩](#fnref1)