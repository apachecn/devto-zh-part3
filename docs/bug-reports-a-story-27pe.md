# 错误报告:一个故事

> 原文：<https://dev.to/zmdominguez/bug-reports-a-story-27pe>

> “这个工具太烂了👊😡😡😡😡"
> 
> “唉这太傻了搞什么鬼💩🔥🔥🔥"

这些只是我多年来在 Android 上工作时听到开发者说的一些事情。有时他们试图弄清楚一些事情，但没有足够的文档，有时他们试图让他们的工具做一些事情，但就是不行。通常，这是因为他们期待某事发生，但它并没有发生。

我知道这可能令人沮丧，毕竟我已经做了 12 年多的开发人员了。但是话说回来，无论你多么大声地诅咒机器人之神，你又能得到什么呢？这能解决问题吗？这能改善现状吗？我不这么认为。

那我们该怎么办？

#### 我的回答已经是，而且永远是，“你给它备案 bug 了吗？”

Android 有自己的专用问题跟踪器，它被组织成几个组件。本页列举了这些组件，并提供了一些关于如何以及何时提交 bug 的非常好的提示。

选择正确的组件很重要，因为(1)它让相关团队意识到您的问题，以及(2)每个组件可能都有一个报告问题的模板。

[![](img/474a2ce670f19b18224192a45236a2ae.png)](///assets/bug_reports/components_filter.png)
T4】

<center><small>Android Studio component with template</small></center>

当写一份错误报告时，想想作为一名开发人员，如果是*你*收到问题，你会发现什么是有用的。*“这个应用太烂了”*远不如*“我无法用我电脑上的凭证登录”*。

我尽我所能帮助开发者，因为我们都有相同的最终目标:**修复一个已验证的问题**。

这可能很费时间，但是当你报告的错误得到修复，其他开发人员开始注意到并从中受益时，这是非常值得的。💚

> ![Bartek Lipinski profile image](img/5a0ff2f58f1b8230ab6a95a7b731d4d1.png)Bartek Lipinski[@ blipinsk](https://dev.to/blipinsk)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)WOOOOW Android 数据绑定中的错误信息改进了这么多！现在很有用👏👏我该感谢谁[@ yigitboyar](https://twitter.com/yigitboyar)[@ georgemount 1](https://twitter.com/georgemount1)？[@ Android dev](https://twitter.com/AndroidDev)[# Android dev](https://twitter.com/hashtag/AndroidDev)2019 年 2 月 19 日下午 12:17[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1097832566512644096)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1097832566512644096)2[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1097832566512644096)9

<center><small>I might be totally wrong but I’d like to think [my bug report](https://issuetracker.google.com/issues/62685775) helped nudge this along 😅</small></center>

> ![unknown tweet media content](img/9bcd5676a0a862cde8b1687fa2e83814.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/DXMRkUeX0AAc82G.mp4" type="video/mp4"></video>![Stephan profile image](img/2bedf90b75ec0721942fedcfedef9911.png)Stephan@ theearlofego![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)哦你可以在 Android Studio 中将你的根布局转换成数据绑定布局(以防你像我一样懒)[# Android dev](https://twitter.com/hashtag/AndroidDev)[# Android Studio](https://twitter.com/hashtag/Androidstudio)

<center><small>Sometimes, [my feature requests](https://issuetracker.google.com/issues/37136823) actually get built too! 💃</small></center>

#### 你想让*你的*问题也得到解决吗？

诀窍是让 bug 报告尽可能的棒。让我们以[这个 bug 为例](https://issuetracker.google.com/issues/38213600)。我在 Android Studio 中运行“移除未使用的资源”时遇到了一个问题，我想报告这个问题。

为了收集我的机器的相关信息，我打开了 Android Studio，然后选择了“关于 Android Studio”。然后我点击了出现的对话框中的复制图标；它将模板要求的几乎所有信息复制到我的剪贴板中！

[![](img/dd41cbec3bfd9647e4e624e273df6716.png)](///assets/bug_reports/copy_info.png)
T4】

<center><small>Click that!</small></center>

然后，我提供了复制步骤的简短概述；我试着遵循既定的时间模式。首先设定场景，给开发人员一些我们试图实现的背景。在这种情况下，我提供了一个示例项目的 zip 文件，该文件再现了错误。

接下来，我报告了我试图做的事情和结果。如果有多个步骤需要首先执行，我会按顺序一一列举。在这个 bug 中，我还提供了一个截图，以便团队可以快速验证他们是否完全按照我看到的那样重现了我的错误。

最后，我试图提供尽可能多的细节。有没有不发生这种情况的场景？

> 看起来 Studio 将通过生成的绑定文件的`.inflate()`方法膨胀的布局检测为未使用。使用 DataBindingUtil 展开的视图不会被移除。([从此处](https://issuetracker.google.com/issues/38213600#comment1))

如果是功能请求，我希望看到什么样的最终结果？修复此问题有什么好处？

> 如果我开始数据绑定，“用户定义的类型”可能是陌生的。([从此处](https://issuetracker.google.com/issues/62685775#comment1)

据我所知，有什么解决方法吗？

> 找到了另一种解决方法。去掉箭头前后的空格，如下:`android:onClick="@{()->handlers.onSendNotificationClick()}"` ( [从此处](https://issuetracker.google.com/issues/37136809#comment2))

当我发现初始修复遗漏了一个用例时，我提供了另一个样例项目。

真的，一个样本项目就像一幅画。它描绘了千言万语。

**编辑(20190221):** 我不敢相信我需要说这些，但我还是要说: ***友善点*** 。没人想和混蛋说话。

#### 一份好的 bug 报告就像一个好故事。

它介绍了人物，引入了冲突，最后是结局。

做一个善于讲故事的人，俘虏你的听众。毕竟，是这些故事让这个世界变得更加有趣。☺️