# 有趣的爪哇与科特林之战

> 原文：<https://dev.to/lemuelogbunude/the-funny-java-vs-kotlin-battle-492m>

浏览 Twitter 时，我发现了这条我有点同意的推文:

> ![Vasiliy Zukanov profile image](img/c93a6d4ddf7d6a134eb567f2051a718b.png)瓦西里·祖卡诺夫@瓦西里·祖卡诺夫![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)这个线程反映了 Android 发展的真实状态。虽然 Kotlin 的粉丝们非常直言不讳(在某些情况下甚至咄咄逼人)，但仍有许多 Android 开发人员更喜欢使用 Java，不明白这有什么大惊小怪的。
> 2019 年 5 月 13 日下午 19 点 59 分[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1128026975694536704)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1128026975694536704)2[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1128026975694536704)3

我接着查看了前面提到的 Reddit 帖子。

以下是我对所见所闻的看法。

在 Android 社区中，Java 和 Kotlin 之间不断的争斗，在我看来是非常不必要的。

我同时使用 Java 和 Kotlin，可以说 Kotlin 是一种很棒的语言。

但问题是，要提升一种语言，你不需要贬低另一种语言。如果一个工具是坚固的，经得起时间的考验，人们会采用它。

我看到的另一个错误是，当人们将 Kotlin 当前的特性与 Java 过去的问题进行比较时。

我这么说是什么意思？

当人们比较 Kotlin 和 Java 时，他们有时会不知不觉地比较 Kotlin 和 T2 Java。

如果你不知道，最新的 Java 版本是 Java 12。

安卓上用的 Java 主要是 Java 7 和一点 Java 8。Java 8 的特性正在被添加到 Android 中，供开发者使用。

如果你熟悉 Android 开发，你会知道为了使用 *Java Streams* ，这是 **Java 8 (2014)** 中的一个功能，你需要在 **API 级别 24** 或更高才能在 Android 上使用它。

为了清楚起见，API 等级 24 是*安卓牛轧糖*。

Java 8 的另一个特性([，你现在可以在 Android](https://developer.android.com/reference/java/util/concurrent/CompletableFuture) 上访问)是 CompletableFutures，它可以用几行代码替换 AsyncTask 类的所有行。

如果你注意到了，我还没有谈到当前的 Java 12 特性。有可能看到 Java 12 代码误以为是 Kotlin，Java 就是这么进步的。

我也是科特林的粉丝。如果你想了解更多关于 Kotlin 的信息，请查阅 [Kotlin in Action](https://www.manning.com/books/kotlin-in-action) 电子书。

但是作为一种语言的爱好者不应该成为另一种语言的敌人。

我听到的常见论点是可读性和生产力。如果你想读一篇关于为什么 Kotlin 并不意味着可读性和生产力的文章，请阅读这篇文章。

采用 Kotlin 并不能保证你会有更快乐的开发人员，更少的错误和更高的利润。最终，用户/客户可能甚至不知道或不关心 If 语句的含义。

如果你在 Android 上用的是 Java，牛逼！
也可以看看安卓开发者的 [RxJava。](https://www.manning.com/books/rxjava-for-android-developers)

如果你正在使用 Kotlin，请保持下去！

如果你用 C++编写了这个应用程序，并且它像预期的那样运行良好，那就太好了！如果你是用 React native 写的，那就太好了！如果你是用 Flutter 写的，那就太棒了！

我们喜欢闪亮的新工具。我们还应该专注于工作的完成和维护。

较短的代码行(尤其是不重要的代码行)并不能保证项目能够更快地交付。可读性是主观的，任何关于生产率的统计都应该基于事实。

编码快乐！