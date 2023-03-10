# Git，Github: Git 初始化错误“错误:src refspec master 不匹配任何。”

> 原文：<https://dev.to/arisa_dev/git-github-git-initialize-error-error-src-refspec-master-does-not-match-any-2lcj>

从我的媒体条目交叉过账。
[中:阿里萨 F](https://medium.com/@shabibi/git-github-git-initialize-error-error-src-refspec-master-does-not-match-any-f90ffb76f0c4)

大家好，我是 Arisa，一名生活在德国的自由职业全栈开发人员。

我确信您看到这个条目是因为您的终端或 PowerShell 从 Git 得到了一个错误。

我还得到错误消息说，

```
error: src refspec master does not match any.
error: failed to push some refs to '<REPO_URL>' 
```

# 情况

这里的情况很基本。考虑在我的 Github 中创建一个新的存储库。

我已经在本地准备好了我的项目，只需要创建一个新的回购协议，然后推动我的项目。

您应该会在刚刚创建的 Github repo 页面中看到这样的内容。

[![Github, after created a new repo](img/48536e588ec7583d3eabd6bb8d8c354d.png "Github, after created a new repo")](https://res.cloudinary.com/practicaldev/image/fetch/s--sKmTFoyN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ypzb5nwuxp7gfbtn71j7.png)

这很简单，只要按照“…或者在命令行上创建一个新的存储库”中所写的那样去做就行了

通常情况下，它的工作，你会看到 README.md 被添加。但是如果你不幸的话，你会看到一个类似上面的错误。

# 解

怎么办？没什么大不了的。

只是添加和提交一些改变你的回购，如添加。gitignore 或任何更改。
然后重新运行推送命令。

为什么有效？

这后面的一个窍门就是开始知道这一点。只有在提交到您的本地回购之后，Git 才会创建一个主分支。
如果您只是初始化回购协议，那么就没有主协议。

这就是为什么 Git 会返回一个错误，

src refspec 主数据不匹配。很简单，对吧？
再也不会因指挥失误而心脏病发作。

希望这篇文章对你的项目有所帮助。

再见！再见！