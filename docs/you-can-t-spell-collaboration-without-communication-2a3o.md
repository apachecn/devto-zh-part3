# 没有沟通，就拼不出协作

> 原文：<https://dev.to/lostintangent/you-can-t-spell-collaboration-without-communication-2a3o>

在[之前的一篇文章](https://dev.to/lostintangent/collaboration-doesn-t-come-in-one-size-fits-all-33ai)中，我讨论了 [Visual Studio Live Share](https://aka.ms/vsls) 如何实现一种灵活的协作形式，这种形式可以适应不同的开发人员偏好和[用例](https://aka.ms/vsls-usecases)(例如结对编程、入职、教育)。然而，再多的灵活性也无法弥补这样一个事实:有效的协作需要更基本的东西:沟通。

当我们构建 Live Share 时，我们希望它能与团队、Slack 或 Discord 等交流服务一起使用。这样，Live Share 可以提供协作开发体验(例如，共同编辑、调试、终端)，同时补充团队已经在使用的任何通信解决方案。然而，随着时间的推移，我们开始听到对**轻量级/同伴聊天体验**的[相当大的兴趣](https://github.com/MicrosoftDocs/live-share/issues/69)，这将允许开发人员作为实时共享会话的一部分直接交流(不需要使用代码注释！)，并提供三个潜在的好处:

1.  有一个**单独的集合点**(实时共享会话 URL)用于加入开发者协作会话，以及建立通信线路。对于某些用例(例如，技术访谈、黑客马拉松、产品支持)，这可以提供简化的体验。

2.  **减少上下文切换(和干扰)**，能够直接在您的 IDE 中与其他开发人员在实时共享会话中聊天。

3.  使**关于代码**的交流更加容易，因为聊天消息可以自然地与特定的代码行相关联，而不是
    不得不四处复制粘贴代码片段。

遵循 Unix 的理念，T2 的 Live Share 本身仍然专注于解决开发者的协作，并把交流的选择权留给每个团队。然而，为了解决上述好处，并使开发人员能够选择加入一个更加固执己见的堆栈，我们决定创建 [Live Share 扩展包](https://aka.ms/vsls-pack)，它代表了**协作和通信**的一站式商店。让我举例说明这是什么样子！

## 音频通话

安装了 [Live Share 扩展包](https://aka.ms/vsls-pack)后，Live Share“会话详情”视图将有一个闪亮的新选项:`Start Audio Call..`。如果你点击它，它将启动一个语音通话，**不需要你登录或使用一个单独的服务**🚀。更酷的是，它会自动通知客人来电，并让他们选择加入。

[![Live Share Audio](img/923e1e7cc6f9ae9b13cb7e365e8fe1ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TLllxMs7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/116461/58754395-d3c50f00-8483-11e9-8877-59389f675910.gif)

*现场分享主持人(右)直接在 VS 代码*内开始音频通话

默认情况下，您必须明确开始并加入呼叫。但是，如果您想要在共享实时共享会话时自动开始通话，或者在加入实时共享会话时总是接受通话，可以设置更改此行为*(见下文)*。一旦启用，实时共享会话 URL 就变成了一个**单一集合点**，开始编码并与其他开发人员交流。我们发现这非常强大。

[![Screen Shot 2019-06-01 at 2 55 36 PM](img/878920cf0c4c9709850e742affee6b76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sxC_NmEz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/116461/58754032-7ded6880-847d-11e9-8275-2e35a6879203.png)

## 文字聊天

在许多情况下，在实时共享的同时进行语音通话可以提供最佳(也是最常用的)协作体验。但是，在有些情况下，语音交流可能不是首选(例如，您在团队房间中)，在有些情况下，客人总是通过音频与主人交流可能会分散注意力(例如，直播、教室、团队棕色包)。为了适应这些场景， [Live Share 扩展包](https://aka.ms/vsls-pack)还提供了集成的文本聊天体验，完全消除了应用程序之间的**上下文切换**。

[![Team Chat](img/93e8b4024ca0aea6b893066a9ab1d392.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lQUX5qZ_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/116461/58754219-7c716f80-8480-11e9-92c0-16bec0c5c33a.gif)

*在 VS 代码*中，一名现场分享主持人(右)正与一名嘉宾(左)聊天

安装后，当您共享或加入会话时，实时共享聊天窗口将自动打开。因为聊天线程是一个常规的 Visual Studio 代码文档窗口，所以您可以随意安排它，这使得每个人都可以轻松地同时编码和聊天。除了在实时共享会话中与每个人进行群聊，你还可以通过点击“联系人”面板中开发人员姓名旁边的聊天图标，直接向他们发送消息。

这允许你快速地问某人一个问题，或者看看他们是否有时间分享，而不需要切换上下文。

## 代码注释

在协作时，音频和文本聊天通常构成通信的基础。然而，有些情况下，你想谈论的是一个特定的代码行/代码块，因此，如果你可以直接注释代码，而不必费力地通过聊天记录来讨论它，那就简单多了。在实时分享会话中，你会注意到一个新的“评论栏”会出现在编辑器的
行号和代码本身之间。使用它，你可以点击任何一行代码，开始一个新的评论线程。这些注释将立即与所有 guests 同步，并且在文件中以及 Visual Studio 代码中的`Comments`窗格中可见。

[![Comments](img/483a96aa7a34756b73b138388911e272.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QQlNE-yp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/116461/58754450-002d5b00-8485-11e9-982d-3dc1cbd550d5.gif)

*现场分享主持人(右)直接在 VS 代码中留下实时代码评论*

这种体验可以实现一些有趣的场景，例如在长时间运行的实时共享会话期间为自己留下待办事项/提醒，使用实时共享来执行特别/非正式的代码审查，或者在他们离开或在另一个文件中工作时简单地为他们留下评论。comment UI 与用于通过 [GitHub Pull Requests](https://marketplace.visualstudio.com/items?itemName=GitHub.vscode-pull-request-github) 扩展查看 PR 评论的 UI 完全相同，这在 Visual Studio 代码中的协作之间提供了良好的连续性，无论它是实时的还是异步的。

> 由于代码评论功能应用广泛，它直接包含在 Live Share“核心”中，而不是 Live Share 扩展包(如音频和文本聊天)。

## 我们很想聊天！

通过使开发人员能够使用音频通话、文本聊天和代码注释进行交流，我们相信 Live Share 可以更好地适应已经在使用的[用例](https://aka.ms/vsls-usecases)的广度。然而，为了支持开发者的选择，音频和文本聊天体验是完全可选的，并且只包含在 [Live Share 扩展包](https://aka.ms/vsls-pack)中。这样，不管你在做什么样的合作，也不管你的团队在使用什么样的交流工具，Live Share 都可以称赞它，并有希望让它[更愉快](https://aka.ms/vsls-why)👍

> ![International Space Station profile image](img/e97c173ca991c8476464231a4677a871.png)国际空间站@ intlspace![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ LostInTangent](https://twitter.com/LostInTangent)[@ erinfoox](https://twitter.com/erinfoox)我认真地，感激你为使我的工作流程更优化所做的一切。最近刚刚发现了实时共享音频和聊天，它允许在结对编程期间通信只在 ide 内进行，而在 slack 等其他应用程序之外进行。2019 年 12 月 18 日上午 04:04[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1207149543424311296)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1207149543424311296)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1207149543424311296)