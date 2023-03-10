# 孔雀-选择颜色

> 原文：<https://dev.to/john_papa/peacock---choose-what-to-color-33mg>

上周我发布了 [Peacock](https://marketplace.visualstudio.com/items?itemName=johnpapa.vscode-peacock&wt.mc_id=devto-blog-jopapa) ，它解决了我的一个问题:快速直观地区分 VS 代码实例。是的，我通常为多个不相关的项目打开很多。更不用说我还用 VS 代码写文章(就像这篇)，做笔记，编辑几乎所有的东西。

[![john_papa image](img/e4bea158821130090634a4a56bffc625.png)](/john_papa) [## 孔雀-深夜编码 FTW

### 约翰爸爸 1919 年 2 月 22 日 2 分钟阅读

#vscode #javascript #fun](/john_papa/peacock---late-night-coding-ftw-3pk0)

当我宣布的时候，它似乎很受欢迎(感谢支持)。我通过 Twitter 和 GitHub 收到了一些好的建议/请求。所以我决定加几个。

## 最新消息

以下是新特性:(或者你可以查看一下 [CHANGELOG.md](https://github.com/johnpapa/vscode-peacock/blob/master/CHANGELOG.md) )

最大的新功能是...

1.  您可以重置(也称为清除)Peacock 在工作区中设置的所有颜色。

2.  你可以告诉 Peacock，当你选择一种颜色时，VS 代码的哪些部分会受到影响。您可以通过将属性`peacock.affectedSettings`设置为下面的一个或多个有效值来做到这一点。

```
// Valid settings you can choose to be affected
"peacock.affectedSettings": [
    "activityBar",
    "statusBar",
    "titleBar"
  ] 
```

所以你可以选择只影响其中一个，两个或者全部三个。你做你的！

> ![unknown tweet media content](img/016325c74de5a0e16d2db71c0c11c0a4.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/D0JRsvoX0AAbkH1.mp4" type="video/mp4"></video>![John Papa profile image](img/fa3b290a537c8a1dbb30a8f2f982d3e7.png)John Papa[@ John _ Papa](https://dev.to/john_papa)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)新版孔雀 v 0 . 0 . 0 . 7 可供 VS [@code](https://twitter.com/code) 。现在有了更多选择。给状态栏、标题栏和/或活动栏着色。你做你的！
> 
> 了解更多关于 [@ThePracticalDev](https://twitter.com/ThePracticalDev) 👇
> [dev.to/john_papa/peac…](https://t.co/81K9Pl9she)
> 
> 感谢支持！04:46AM-2019 年 2 月 24 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1099531106083328001)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1099531106083328001)1[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1099531106083328001)2

## 信用额度还原

我还想再次感谢 [@josephrexme](https://twitter.com/josephrexme) 为 Peacock 提供的名字和图标，感谢 VS 代码团队和他们令人难以置信的[对创建扩展的帮助指南](https://code.visualstudio.com/api/get-started/your-first-extension?wt.mc_id=devto-blog-jopapa)

## 得到孔雀

如果你有孔雀，想更新到 v0.0.7，VS Code 会很快提示你。

如果你有兴趣试用孔雀，你可以在这里的市场找到它。它目前正在预览中，这意味着前面可能有龙。

*   在这里获取扩展名
*   贡献给 GitHub 库[这里](https://github.com/johnpapa/vscode-peacock?wt.mc_id=devto-blog-jopapa)

[![peacock icon](img/20eccdf3cf62d77c11f20d58229e70e9.png)](https://marketplace.visualstudio.com/items?itemName=johnpapa.vscode-peacock&wt.mc_id=devto-blog-jopapa)

最坏的情况是，这个扩展只是我将使用的东西，这没关系。但如果你也喜欢，请试一试，并在 GitHub 中提交反馈。您可以[打开问题](https://github.com/johnpapa/vscode-peacock/issues?wt.mc_id=devto-blog-jopapa)或抓住[打开问题](https://github.com/johnpapa/vscode-peacock/issues?wt.mc_id=devto-blog-jopapa)并帮助投稿。

谢谢！