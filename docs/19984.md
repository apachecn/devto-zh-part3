# Git Checkout Tag Git Tagging Tutorial

> 原文：<https://dev.to/rrampage/git-tags-from-command-line-103l>

我在 [dev.to](https://dev.to/zellwk/git-tags--37a) 上看过一篇帖子，展示了如何使用基于 GUI 的 git 客户端创建 git 标签。我认为即使在使用 git cli 时，知道标签也是有用的。

### 什么是标签

标签是代码历史中的特定点，对以后再次访问很有用，例如
你刚刚发布了一个新版本的应用。您可以使用 **`git tag v1.0`** 将提交标记为`v1.0`。任何时候你想重现那个版本遇到的错误，只需做 **`git checkout v1.0`** 并调查。

### 如何使用`git tag`更好

#### 结账码为标签

标签链接到特定的提交，而不是分支。当您签出标签时，git 告诉您处于“分离头”状态。不要担心，这意味着如果您想保留签出标记后所做的任何更改，您需要创建一个新的分支。

使用 **`git checkout -b BRANCH_NAME TAG_NAME`** 在标签提交时创建一个新的分支

#### 让你的标签更信息化！

您可以使用 **`git tag -a TAG_NAME -m 'MESSAGE'`**
添加更多信息，无需使用 **`git show TAG_NAME`** 检查标签即可查看标签信息。

#### 在特定提交时创建标签

您不必总是在树枝的顶端或尖端来创建标签。如果您想在 HEAD 之前创建标记，比如说 5 次提交，您可以使用`git log`来获得正确的提交散列，例如 **`git log --pretty=oneline -10`** ，它显示了当前分支上的最后 10 次提交。

然后，使用 **`git tag -a TAG_NAME -m 'MESSAGE' COMMIT_HASH`** 创建标签

#### 列出和删除标签

列出所有标签很简单: **`git tag`**
同样，删除一个标签用 **`git tag -d TAG_NAME`**

#### 与他人分享您的标签！

创建的标签不会自动推送到远程。如果你想让其他贡献者也使用你的标签，你需要使用 **`git push origin TAG_NAME`** 推送它们

#### 签下你的标签！

如果您正在进行一个大型项目，并且希望毫无疑问地表明您已经完成了发布，您可以使用您的 GPG 私钥 **`git tag -s TAG_NAME -m 'MESSAGE'`** 进行签名。任何在标签上运行 **`git show TAG_NAME`** 的人也会看到您的公钥签名以及标签信息。

他们还可以使用 **`git tag -v TAG_NAME`** 来验证标签。这将使用您的公钥检查签名是否确实是您的。

### 标签 vs 分支

有分支为什么要用标签？因为分支可以改变，并且标签与特定的提交相关联。因此，用一个标签来标记发布会给你一个特定软件发布完成时的代码状态。

### 总结

*   标签是记住具体提交的好方法
*   最好在标记中添加一条消息，这样人们就可以很容易地看到为什么您标记了一个特定的提交

### 参考文献

Git 图书章节:

*   [标记](https://git-scm.com/book/en/v2/Git-Basics-Tagging)
*   [签约](https://git-scm.com/book/en/v2/Git-Tools-Signing-Your-Work)