# ⚡，你的安卓工作室总是很慢吗？下面介绍一下如何立刻提速。

> 原文：<https://dev.to/wajahatkarim/-is-your-android-studio-always-slow-heres-how-to-speed-up-immediately-4fi1>

#### 一个非常快速有效的提升 Android Studio 的技巧

[![](img/f92899ff3a18b101b8215b365aa01a3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rmsN2F3Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/840/0%2A8CuucCNm9IOIfzdt.png)

> 最初发布于[我的媒体简介](https://android.jlelse.eu/is-your-android-studio-always-slow-heres-how-to-speed-up-immediately-326ef9238024)。

作为一名安卓开发者，我们要花大量时间在安卓工作室 上，这是谷歌开发原生安卓应用的官方编译器。多年来，它已经发展成为一个非常强大的 IDE，具有许多特性和功能。但仍然存在一些问题，由于各种原因，如 Gradle 构建时间、Android Studio 速度等，许多开发人员尚未解决这些问题。

<figure>[![](img/f8b6518f78335a290b7750a1a1f87a31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KjUOrik_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/700/0%2ANdbxYnTZYJX539tY.png)

<figcaption>grad le——每个安卓开发者最大的噩梦</figcaption>

</figure>

关于如何提速，已经有[很多](https://hackernoon.com/how-to-speed-up-your-slow-gradle-builds-5d9a9545f91a) [文章](https://hackernoon.com/speed-up-gradle-build-in-android-studio-80a5f74ac9ed)包括[官方文件](https://developer.android.com/studio/build/optimize-your-build)。有些行得通，有些行不通。但是本文不是关于 gradle 构建时间的。相反，这是一个更常见但令人沮丧的问题:缓慢的 Android Studio。让我们看看如何加速它，用一些非常简单的方法让它变得更快。

* * *

### ⚙️安卓工作室要求

根据 Android Studio 的官方系统要求，流畅运行至少需要 3 GB 内存。老实说，这太多了，我相信这是一直太慢的最大原因。

<figure>[![](img/6b8c8aa53b92ee5d2c6917dbc7386e13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3t8wL0fK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHBVyVxcwKupAX_k7XTsYfQ.png) 

<figcaption>安卓工作室的系统需求来自他们的[官网](https://developer.android.com/studio/)</figcaption>

</figure>

安卓开发者总是[抱怨](https://stackoverflow.com/questions/30817871/android-studio-is-slow-how-to-speed-up) [抱怨](https://stackoverflow.com/questions/51297025/why-does-android-studio-run-so-slow) [安卓工作室](https://www.quora.com/Why-is-Android-Studio-so-slow-and-complicated) [的速度](https://www.reddit.com/r/androiddev/comments/a4voor/android_studio_very_slow/) [和它的慢。这使得整体开发缓慢且令人沮丧。这对生产力有负面影响。](https://www.reddit.com/r/androiddev/comments/7s1w23/android_studio_is_slow_compared_to_visual_studio/)

[![](img/c24c2f97bcb620d45a9f11c595e61c70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rZgac_nh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/799/1%2Ae6fzpSeTIf5appfiaekKZA.jpeg)

<figure>[![](img/7dcf532895e3d7eb6438d9c605d82fcb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wivNNNLK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/712/1%2Aw5kbt6MMyMDZdv5AaoMuaw.jpeg) 

<figcaption>一些模因来自 DevRant([【https://devrant.com/search?term=android+studio】](https://devrant.com/search?term=android+studio))</figcaption>

</figure>

闲聊到此为止。让我们看看如何用一种非常简单的方法让它快很多。

* * *

### 🚀加速 Android Studio

你可能读到过不同的技巧，比如离线 Gradle，在 gradle.properties 文件中增加内存，禁用即时运行等等。让我告诉你一个非常简单的方法，不是别人，正是杰克·沃顿 在 Reddit 上分享了大约一年**。**

<figure>[![](img/774456df2af0057c508d9261d53486a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B3UCnaqS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Azqq0TIgl9zZ3hJW2qcdFNw.png) 

<figcaption>杰克·沃顿在 Reddit</figcaption>

</figure>

上的一篇[帖子上的评论](https://www.reddit.com/r/androiddev/comments/7sxhig/android_studio_slower_when_using_kotlin/)

所以，让我告诉你怎么做。

首先，点击文件->设置->插件打开这样的对话框。

<figure>[![](img/78671d25f8f118159a5dd686f60ec870.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pDqv1MB_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Auh2nfRBXYrVRFGReckw_1Q.png) 

<figcaption>安卓</figcaption>

</figure>

中设置的插件部分

现在，禁用或检查所有对你无用的插件。我已禁用以下功能:

*   安卓 APK 支持
*   安卓游戏
*   安卓 NDK
*   应用链接助手
*   版权
*   新闻报道
*   cvs 集成
*   编辑器配置
*   Android Studio 的 Fabric
*   Firebase(应用程序索引、服务、测试)
*   开源代码库
*   Google(云工具核心、Android 云工具、开发人员示例、登录、服务)
*   降价支持
*   水银集成
*   hg4idea
*   设置存储库
*   颠覆集成
*   任务管理
*   测试记录器
*   考试网-J
*   亚姆

现在，你的对话框会是这样的:

<figure>[![](img/c3af5a6a0bd9152303fc87ce9ade0c85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6lPGmXKa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ACQTnsDwIqLmU4nnb9FTEpw.png) 

<figcaption>安卓</figcaption>

</figure>

设置中禁用部分插件后的插件部分

现在，点击应用->确定，重启 Android Studio。我打赌你会立刻感觉到不同。

* * *

### 🤔如果我以后需要任何插件怎么办？

例如，在某些时候，你的应用程序需要 Firebase 应用程序链接，而你想使用这个插件。然后暂时启用它。做你的工作。然后您可以再次禁用它。

我希望这将修复您的问题，并使您的 Android Studio 更快，您的应用程序开发更快。😃快乐 App 编码

[![](img/0fa4b48a47b7f9c5ef5272d0e1952062.png)](https://i.giphy.com/media/g9582DNuQppxC/giphy.gif)

* * *

**如果你喜欢这篇文章，你可以在下面的 2018 年回顾中阅读我的新文章:**

[对 2018 年的反思——我的回顾之年](https://dev.to/wajahatkarim/reflections-on-2018my-year-in-review-144)

* * *

**Wajahat Karim** 毕业于[NUST](http://nust.edu.pk)，是一名经验丰富的移动开发人员，一名活跃的开源贡献者，也是两本书[学习 Android 意图](https://www.amazon.com/Learning-Android-Intents-Muhammad-Usama/dp/1783289635)和[用 Unity 掌握 Android 游戏开发](https://www.amazon.com/Mastering-Android-Game-Development-Unity/dp/1783550775/)的合著者。在业余时间，他喜欢花时间和家人在一起，做编码实验，喜欢写很多东西(主要是在博客和媒体上)，并且是开源的热情贡献者。2018 年 6 月，他的一个库成为了 [Github 趋势](https://github.com/trending)上的第一名。[他的库](http://github.com/wajahatkarim3)在 Github 上有大约 2000 颗星星，并被全球各地的开发者用于各种应用。在 Twitter 和 [Medium](https://medium.com/@wajahatkarim3) 上关注他，获取更多关于他的写作、Android 和开源工作的更新。

[Wajahat Karim (@WajahatKarim) | Twitter](https://twitter.com/WajahatKarim)

此外，如果你有任何想让他回答的问题，可以通过他的网站联系他，地址是[wajahatkarim.com](http://wajahatkarim.com)，主题栏是亲爱的瓦贾哈特。

* * *