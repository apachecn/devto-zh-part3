# 存档您的 Git Repo

> 原文：<https://dev.to/walkingriver/archive-your-git-repo-3opa>

从[https://walkingriver.com/archive-your-git-repo/](https://walkingriver.com/archive-your-git-repo/)交叉发布

您知道吗，有一个非常简单明了的命令可以归档一个完整的 Git 存储库，同时不包含任何历史信息。在这篇简短的文章中，我解释了我是如何做的以及为什么这样做。

## 为什么要存档你的 Git 回购？

每隔几周左右，我发现自己需要归档我的 Git 回购。我经常这样做是为了给没有理由直接访问回购的人发送代码。他们只需要代码的快照，没有历史信息，因为他们不会参与其中。

我还提供了我的 Pluralsight 课程中每个模块的代码快照。能够将我的 Git repo 归档到一个 zip 文件中非常方便。

出于某种原因，我总是记不住该怎么做，并且发现自己在反复查找。我决定把它写在这里，这样当我需要它的时候，我就可以随时找到它。也许它会帮助其他人，而不仅仅是我。

## 怎么做

如果您想要备份 Git repo，将其从 Git 中完全分离，请使用以下`git archive`命令的变体之一:

### Zip 文件

```
git archive --format zip --output /full/path/to/zipfile.zip master 
```

Enter fullscreen mode Exit fullscreen mode

### Tar 文件:

```
git archive master > /some/other/path/my-repo.tar 
```

Enter fullscreen mode Exit fullscreen mode

### Tar / Bzip:

```
git archive master | bzip2 > my-repo.tar.bz2 
```

Enter fullscreen mode Exit fullscreen mode

在每一种情况下，单词`master`都是我要归档的分支。您可以归档您想要的任何分支，只需将`master`替换为您想要的分支的名称。

虽然不是绝对必要，但我喜欢从我的回购根创建我的归档，并在它之外创建我的归档。

*注意:归档文件不包含。git 目录，但是将包含其他隐藏的特定于 git 的文件，如。gitignore，。gitattributes 等。*

## 引用

* * *