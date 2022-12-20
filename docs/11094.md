# 建立多个 GitHub 账户，更好的方式

> 原文：<https://dev.to/arnellebalane/setting-up-multiple-github-accounts-the-nicer-way-1m5m>

*这篇文章最初发表在我们的 [UncaughtException](https://medium.com/uncaught-exception) 博客上。通过点击[这个链接](https://medium.com/uncaught-exception/setting-up-multiple-github-accounts-the-nicer-way-5ab732078a7e)阅读这篇文章。*

*由[布丽娜·布鲁姆](https://unsplash.com/photos/Bb_X4JgSqIM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/git?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的封面照片。*

* * *

我以前写过关于设置多个 GitLab 帐户的文章，它主要依靠 SSH 配置文件，并使用自定义主机作为 git 存储库的远程。可以按照这个链接阅读:[设置多个 GitLab 账号](https://medium.com/uncaught-exception/setting-up-multiple-gitlab-accounts-82b70e88c437)

前面的设置是可行的，但是我发现在克隆存储库或者一直使用 git 远程时必须使用定制主机是不方便的，比如:

```
$ git clone git@gitlab.com-work:work/repository.git 
```

我最近(几乎)不得不处理相同的场景，这次是在 GitHub 上(因此有了这个标题)，所以我决定看看我是否能实现一个更好的设置。事实证明，有一种更好的方法可以做到这一点——一种不依赖于定制主机或修改任何 SSH 配置文件的方法。

在本文中，我们将假设我们有两个 GitHub 帐户，`personal@email.com`和`work@email.com`。当处理与工作相关的项目时，我们希望使用我们的工作帐户提交和推动我们的更改，而使用我们的个人帐户处理其他事情。

我们还将假设我们已经定义了全局配置，告诉 git 使用我们的个人帐户。如何做到这一点，请参见本指南。

# 为每个用户生成 SSH 密钥

GitHub 不允许我们在多个帐户中使用同一个 SSH 密钥，所以我们必须为每个帐户创建单独的密钥。我们可以通过遵循 GitHub 文档中的指南来创建 SSH 密钥并将它们添加到我们的 SSH 代理中。

完成后，我们将有两组 SSH 密钥，例如:

*   `~/.ssh/id_rsa`和`~/.ssh/id_rsa.pub`(让我们用这个作为我们的个人账户)
*   `~/.ssh/work_key`和`~/.ssh/work_key.pub`

现在将 SSH 密钥添加到它们对应的 GitHub 帐户中。本指南向我们展示了如何做到这一点。

# 作为正确用户提交

然后，当我们在一个与工作相关的存储库中时，我们修改我们的`~/.gitconfig`文件以使用我们的工作帐户。为此，我们将使用“ [Conditional Includes](https://git-scm.com/docs/git-config#_conditional_includes) ”，它根据特定条件动态添加/删除配置。让我们将下面的代码片段添加到我们的`~/.gitconfig`文件中:

```
[includeIf "gitdir:/path/to/work/repository/"]
    [user]
        email = "work@email.com" 
```

这使得 git 覆盖了我们的全局配置，并在工作存储库中提交变更时使用`work@email.com`。在任何其他存储库中，将使用我们的个人帐户。

## 对所有工作存储库执行此操作

我个人在文件系统上构建存储库的方式是将所有与工作相关的存储库放在`~/work`目录中。这使得通过使用`includeIf`指令中的工作目录来为所有与工作相关的存储库启用上面的配置变得更加容易:

```
[includeIf "gitdir:~/work/"] 
```

# 作为正确用户推送

当我们现在提交我们的提交时，它们将被推送到我们的个人 GitHub 帐户，即使我们已经使用我们的工作电子邮件提交了它们。那是因为在引擎盖下，git 使用 SSH，SSH 默认使用`id_rsa`键。因此，当 GitHub 接收到推送时，它会根据所使用的 SSH 密钥(结果是我们的个人帐户)来尝试确定是哪个用户推送了提交。

我们可以通过在推送到我们的工作存储库时使用`work_key` SSH 键来解决这个问题。在 SSH 中，这可以通过`-i`标志来完成，就像这样:

```
$ ssh -i ~/.ssh/work_key ... 
```

我们可以通过设置`core.sshCommand`来配置 git 使用自定义的 SSH 命令。由于我们只希望在任何工作存储库中设置它，所以我们也有条件地将它包含在`includeIf`指令中:

```
[includeIf "gitdir:/path/to/work/repository/"]
    [user]
        email = "work@email.com"
    [core]
        sshCommand = "ssh -i ~/.ssh/work_key" 
```

这些配置现在让 git 使用我们的工作电子邮件提交，并在我们推送它们时让 GitHub 与我们的工作帐户关联提交🎉。

# 总结

我们已经成功地配置 git 在提交时使用不同的配置，并在推送时将这些提交关联到不同的 GitHub 帐户，这基于我们正在执行这些更改的存储库——我们在 git 的配置文件中使用了条件包含来实现这一点。

与之前的设置相比，我更喜欢这种方式，因为它使用起来更方便(不再需要定制主机)，并且所有的配置更改只影响 git(不再需要更改 SSH 配置文件)。

如果你以不同的方式处理这些多个 GitHub 账户，请随时与我们分享你是如何做的！