# 颤动的安卓 64 位:那又有什么大惊小怪的

> 原文：<https://dev.to/truongsinhtn/flutter-android-64-bit-so-what-the-fuss-25fa>

更新:颤振团队正在努力工作，他们可以实现普遍的 AAB(然后“胖”APK)。在 [PR#33696](https://github.com/flutter/flutter/pull/33696) 查看进度，如果可以的话，尝试并给出反馈。同时，不要担心来自谷歌 Play 商店的警告。

[![](img/1562aca53877d1ef82fcae865984c517.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VgXEMG_Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/283/1%2Ae7UDNZHcJEl0GTy9fiNJ7A.jpeg)

自 2019 年 5 月初以来，人们一直担心 Google play 警告不符合 Play 64 位要求。在 Flutter 的 Github 问题之后，我看到有多个重复的问题(例如 [#18494](https://github.com/flutter/flutter/issues/18494) 和 [#31922](https://github.com/flutter/flutter/issues/31922) )，每个都有重复的问题，这些问题在之前的评论中已经回答过了。在这篇文章中，我将用一个简单的流程图来总结整个事情，并将在下面详细解释流程图中的每个节点。

[![](img/0fc595b249d2cbc4ec1d46b97033f2d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3pCXuyKr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/806/1%2AAwm6pB8jR3wGdHMC4DsatQ.png)

### 在现有安卓 App 中嵌入 Flutter

如果你遵循简单的官方维基[将 Flutter 添加到现有应用](https://github.com/flutter/flutter/wiki/Add-Flutter-to-existing-apps)或更高级的指令[如何将 Flutter 添加到你的生产 Android 应用](https://hackernoon.com/how-to-add-flutter-to-your-production-android-app-4b5150de9e44)(免责声明:我是作者)，除了使用 ABI 过滤器仅构建 32 位 apk/aab，我不知道你能做什么。