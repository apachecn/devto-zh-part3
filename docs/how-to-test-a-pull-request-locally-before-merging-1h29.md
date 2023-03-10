# 合并前如何在本地测试拉请求？

> 原文：<https://dev.to/bolajiayodeji/how-to-test-a-pull-request-locally-before-merging-1h29>

在本文中，我将向您展示如何在合并之前检查、测试和修改一个拉请求。

你的仓库收到了一个新的拉取请求？，在本地机器上测试之前，您不希望合并它。你如何着手做这件事？

pull 请求在这个 git ref `pull/{ID}/head`上可用，您可以使用这个获取它，其中 ID 是 pull `request id`。

**要求**

*   确保你的机器上有一个库的克隆版本，
    点击 *[这里](https://help.github.com/articles/cloning-a-repository/)* 了解如何从 GitHub 克隆库。

*   将您的克隆库集成到您的 [IDE](https://en.wikipedia.org/wiki/Integrated_development_environment)
    中，最好是 *[Visual Studio 代码](https://code.visualstudio.com/)* 【在本
    教程中，我使用了 VsCode】

*   熟悉 *[git](https://git-scm.com/)* 命令

*   转至您的存储库文件夹

*   初始化 git(可以通过右键单击根文件夹来完成)

*   确保你的工作树是干净的(你可以通过运行`git status`来完成)

*   运行以下命令从 GitHub 获取拉请求，其中 **ID** 是拉请求的 ID

```
git fetch origin pull/ID/head && git checkout FETCH_HEAD 
```

或者，如果你只想[摘](https://git-scm.com/docs/git-cherry-pick)它，而不是检查它，你可以使用

```
git fetch origin pull/ID/head && git cherry-pick FETCH_HEAD 
```

*   打开你的 IDE (VsCode)你的文件夹结构现在应该改变了，这取决于 PR 的内容。

您现在拥有的是拉请求的内容，而不是您的主
分支，运行您的检查和测试来确定拉请求是否值得合并。

此时，你可以用这个分支做任何你想做的事情。你可以运行一些本地测试，或者将其他分支合并到其中，包括`master`。做你认为合适的修改！

一旦满足`git checkout master`返回主分支。

您还可以对 pull 请求进行更改，并作为新的 commit 或 pull 请求推回 GitHub。

进行更改后:

```
git add --all
git commit -m "Modified PR"
git push origin BRANCHNAME (e.g master or test) 
```

就这样，您已经测试了拉取请求并做出了更改！

如果这篇文章对你有帮助，请分享！！！