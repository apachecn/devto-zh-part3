# Gitpod，我的新按需🛎开发环境👨‍💻

> 原文：<https://dev.to/doppelganger9/gitpod-my-new-on-demand-dev-environment-3n23>

[![gitpod logo](img/55e1998d07e1e2c8e9888913fd53a152.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bwFFge7L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://gitpod.img/gitpod-logo.svg)

我从一个 [Gitpod](https://gitpod.io) 环境中为我的个人博客写了最初的帖子，直接从我的博客的存储库 URL 启动。

你刚才可以试试这个:[https://gitpod.io/#https://github.com/doppelganger9/blog](https://gitpod.io/#https://github.com/doppelganger9/blog)

你看到了吗，在你的 GitHub repo 前添加“[https://gitpod.io/#](https://gitpod.io/#)”来启动环境是多么的简单和聪明🤓？

好了，是时候来点刺激的了:

[![hail hypnotoad](img/2ed629a796d0616abb1d54332079d37d.png)](https://i.giphy.com/media/rou0CTAp6Z8VW/giphy.gif)

[![mise en abyme](img/80a795650ba619dd37e0299ff5a57e1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M9we7evx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lacourt.dev/static/d7ab94fb640066bfb14d481388394ad3/301c0/screenshot.png)

## 这是什么？

如您所见，这个想法是让浏览器开发环境中的一个 **VisualStudio 代码**从您的存储库中引导。

更准确地说，它是基于[忒伊亚 IDE](https://www.theia-ide.org/)

### 分机

它没有随您的 VSCode 扩展集合一起提供，但是它在 95%的情况下都足够有用。

### 网络稳定性

它有几个断开的时刻。

### 暴露本地 web 服务器

它可以暴露端口，就像当你需要运行你的开发网络服务器，这是可能的，真的没有摩擦。

### 关闭文件

哎呀，如果你像我一样习惯于在 VSCode 上点击`CMD + W`来关闭文件，这在这里就不行了，因为它会关闭浏览器标签！😱
希望，打开返回 [https://gitpod.io](https://gitpod.io) 显示我的工作区，我意外关闭的工作区仍在运行，我可以以它原来的状态重新打开它(进程运行，等等)。)哪个想的好！

### 💸

不要忘记停止你的工作空间，免费层给你 100 个小时，这对于我的使用来说已经足够了:在工作时间之外使用 Github 库。

## 本次集成测试失败🤭

在我写完这篇帖子的初稿后，我试图`git push`，但是我遇到了这个错误:

```
 ! [remote rejected]  ... (refusing to allow an integration to create .github/main.workflow) 
```

Github 拒绝我的推送，因为似乎当你[启用 GitHub 动作](../2019/03/06/)时，它会阻止所有其他 GitHub 集成工作。Gitpod 就是这样一个集成。

良好的...同样，并非一切都是如此🌈和🦄。

该死，稍微搜索了一下，似乎真的有问题。

> ![Stefan Prodan profile image](img/56870a3c60100342f426277ce1e48a14.png)Stefan Prodan@ Stefan Prodan![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)哦，一旦您在回购上启用了@GitHub 操作，其他任何集成都将不起作用。例如，尝试使用 deploy 键推送 git 标记将会出错:“拒绝允许创建集成。github/main . workflow "2019 年 1 月 28 日上午 11:29[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1089848015626686465)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1089848015626686465)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1089848015626686465)5

> ![Patrick Reynolds profile image](img/e1b90ad91d14b2f5a728da6dabef61e8.png)帕特里克雷诺兹@ p _ 雷诺兹![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)[@ Stefan prodan](https://twitter.com/stefanprodan)[@ gudmundur](https://twitter.com/gudmundur)[@ github](https://twitter.com/github)[@ mcolyer](https://twitter.com/mcolyer)[@ weaver works](https://twitter.com/weaveworks)是的，这是一个 bug，我们正在修复。出于安全原因，不允许集成写入 main.workflow，但是我们当前版本的检查可能过于严格。谢谢你的报告。2019 年 1 月 28 日下午 13:38[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1089880487659737088)![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1089880487659737088)3

### 局部修复和 git-fu🥋

我无法下定决心既不删除 Github 动作也不放弃使用 Gitpod！

所以我...

*   编辑我未提交的更改，
*   `git checkout -`离开我为这篇文章专门撰写的 git 分支，回到上一篇文章(`git checkout master`也可以，但是要多输入 5 个字符？咩。)
*   `git cherry-pick` -ed 我做的提交，不知道`push`拒绝问题，
*   `git push`工作过🎉从`master`开始，所以我知道我不必将我在 Gitpod 中的所有更改复制到我的本地计算机上，
*   `git stash pop` -ped 并从这里着手继续写这一段，推送直播！

## 结论

因此，当我经历了某种情绪过山车时，
有一段时间我认为，即使我真的很喜欢拥有*“浏览器中的远程 VS 代码”*的想法，并且在过去发现它非常有用，
将它与 Github 动作结合起来毁了它。但是从`master`开始推就成功了！

* * *

我希望你学到了一些东西，或者至少见证了我是如何遇到问题并继续前进的🤣。

> 感谢你阅读这篇文章，如果你有任何问题，请使用下面的评论！

👋