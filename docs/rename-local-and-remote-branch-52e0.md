# 重命名本地和远程分支

> 原文：<https://dev.to/davidcoy/rename-local-and-remote-branch-52e0>

我们都经历过(或者你可能没有，希望这有助于防止接下来的事情):克隆项目，在我们的本地分支中进行更改，然后将这些更改提交给存储库，却发现你没有遵循分支命名的贡献指南。最近，我针对 GitLab 的[文档打开了一个合并请求，但是没有按照我应该做的那样遵循我们关于](https://gitlab.com/gitlab-org/gitlab-ce/merge_requests/25498)[分支命名](https://docs.gitlab.com/ee/development/documentation/index.html#branch-naming)的文档指南。

最初，我创建了我的分支:`add-efs-warning`，提交了我的更改并打开了合并请求。然而，这是错误的，因为分支名称*也*需要加上前缀`docs/`、`docs-`或`-docs`。我学会了如何重命名我的本地分支*和*远程分支，而不是将我所做的更改复制到一个空白文本文件，更改到主分支，删除旧分支，然后创建一个新分支并应用这些更改。

假设你做了和我完全一样的事情，你在你错误命名的本地分支机构——让我们称之为`bad-branch-name`。如果要重命名分支，可以运行:

```
git branch -m docs/new-branch-name 
```

这将把当前的本地分支重命名为`docs/new-branch-name`，并避免来自审查您的更改的技术作者的反馈。接下来，您可以运行以下命令将`bad-branch-name`重命名为`docs/new-branch-name`，并推送新的本地分支:

```
git push origin :bad-branch-name docs/new-branch-name 
```

最后，您可以为新的本地分支重置上游分支(如果您当前不在该分支中工作，请切换到该分支):

```
git push origin -u docs/new-branch-name 
```

由[泰迪·凯利](https://unsplash.com/photos/H-_Qtte5YU8?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的封面图片