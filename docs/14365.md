# QuizNerd——一款面向开发者的问答应用

> 原文：<https://dev.to/n1try/quiznerd---a-quiz-app-for-developers-4p52>

[![](img/8ccf40647b99be0bae0ef2643a7037a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QXQS1p3K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nsgixkftwjr1a0extypl.png)

**嗨，开发者社区！🙋‍♂️**

我想和你们分享一个我几个月前在 [Udacity Android 开发者 Nanodegree](https://muetsch.io/quiznerd-my-experiences-with-the-android-developer-nanodegree.html) 的背景下创作的 Android 应用。

## 什么事？

QuizNerd 是为程序员和 IT 爱好者开发的多人问答游戏。您可以挑战您的朋友，并在由各种不同的编程问题组成的比赛中竞争。

它包含 9 个类别的 800 多个不同的问题，包括 Android、C++、C#、HTML、Java、JavaScript、PHP、Python 和 Swift。

[![](img/562a1663d2680618e843efd81e4e9e60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZLZyRMMn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oww9i27vntbgtyr8cmko.png)

## 怎么玩？

您需要使用您的电子邮件地址或 Google 帐户创建一个帐户。一旦登录，你可以选择上述语言中的一种作为类别，并挑战一个随机的对手或朋友，如果你知道她的昵称。

## 里面有哪些技术？

这是一个原生的 Android 应用程序，用 Java 编写。我没有使用任何架构框架，只使用了普通的 Android APIs 和一些常见的库，如 Apache Commons、 [OkHttp 3](http://square.github.io/okhttp/) 、 [Project Lombok](https://projectlombok.org/) (如果你问我，这是任何 Java 项目的必备组件)和常见的 Android 支持库。作为后端，它使用谷歌的 [Firebase](https://firebase.google.com/) 和由一个叫做 [DiceBear](https://avatars.dicebear.com/) 的开源网络服务生成的古怪、乏味的化身。

## 如何投稿？

*   **给予反馈。**如果你喜欢我的应用程序，我希望得到你的反馈！将来还会增加很多功能，我一直在努力改进 QuizNerd。
*   **添加问题。**目前，每个类别都有 50 到 150 个问题。这不是很多，所以你会发现问题有时会重复。如果你是一个🤓并且您有适合您选择的语言的很酷的测验问题，请随时提交它们！

我也在考虑在某个时候开源这个应用程序，但我仍然需要清理一下。