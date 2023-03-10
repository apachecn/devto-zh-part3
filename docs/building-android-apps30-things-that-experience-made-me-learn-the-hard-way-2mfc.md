# 构建安卓应用——30 件让我痛苦的事情

> 原文：<https://dev.to/heshanfu/building-android-apps30-things-that-experience-made-me-learn-the-hard-way-2mfc>

有两种人——一种是通过艰难的方式学习的，另一种是通过接受别人的建议来学习的。这里是我一路走来学到的一些东西，我想与你分享

*   在添加任何第三方库之前三思，这是一个**真的** **认真的**承诺；

*   如果用户看不到， [**不要画**](http://riggaroo.co.za/optimizing-layouts-in-android-reducing-overdraw/) ！；

*   除非你真的需要，否则不要使用数据库；

*   达到 65k 方法计数标志会很快发生，我的意思是真的很快！而 [**多指**可以救你](https://medium.com/@rotxed/dex-skys-the-limit-no-65k-methods-is-28e6cb40cf71)；

*   RxJava 是**对 [AsyncTasks 的最佳](https://medium.com/swlh/party-tricks-with-rxjava-rxandroid-retrolambda-1b06ed7cd29c)**替代方案，等等；

*   [改型](http://square.github.io/retrofit/)是**最好的** **联网** **库**是有的；

*   用 [**Retrolambda**](https://medium.com/android-news/retrolambda-on-android-191cc8151f85) 缩短你的代码；

*   将 [**RxJava 与**retro lambda](https://medium.com/swlh/party-tricks-with-rxjava-rxandroid-retrolambda-1b06ed7cd29c)结合起来，最大限度的牛逼！；

*   我用 [EventBus](https://github.com/greenrobot/EventBus) 并且它很棒，但是我**不太用**因为代码库会变得很乱；

*   [按特征包，不按层包](https://medium.com/the-engineering-team/package-by-features-not-layers-2d076df1964d)；

*   将*的所有内容*移出应用程序线程；

*   [lint](http://developer.android.com/tools/help/layoutopt.html) 您的视图，帮助您优化布局和布局层次结构，以便您可以识别可能需要删除的冗余视图；

*   如果你正在使用 *gradle* ，无论如何加速它，只要你[能](https://medium.com/the-engineering-team/speeding-up-gradle-builds-619c442113cb)；

*   Do [profile 报告](https://medium.com/the-engineering-team/speeding-up-gradle-builds-619c442113cb)您的构建，看看什么花费了构建时间；

*   使用众所周知的架构；

*   测试需要时间，但是一旦你掌握了窍门，它会比没有测试的编码更快更健壮。

*   使用[依赖注入](http://fernandocejas.com/2015/04/11/tasting-dagger-2-on-android/)让你的应用更加模块化，因此更容易测试；

*   听[片段播客](http://fragmentedpodcast.com/)对你有好处；

*   [**永远不要**使用你的个人邮箱作为你的 android market publisher 账户](https://www.reddit.com/r/Android/comments/2hywu9/google_play_only_one_strike_is_needed_to_ruin_you/)；

*   **总是**使用[合适的](http://developer.android.com/training/keyboard-input/style.html)输入类型；

*   使用**分析**找到使用模式并隔离 bugs

*   保持在新的[库](https://androidexample365.com/)之上(使用 [dryrun](https://github.com/cesarferreira/dryrun) 来更快地测试它们)；

*   你的服务应该做他们需要做的事情，并且尽快结束；

*   使用[账户管理器](http://developer.android.com/reference/android/accounts/AccountManager.html)建议登录用户名和电子邮件地址；

*   使用 **CI** (持续集成)来构建和发布你的测试版和产品。apk 的；

*   不要运行自己的 **CI** 服务器，维护服务器很耗时，因为磁盘空间/安全问题/更新服务器以防止 SSL 攻击等。使用 circleci、travis 或 shippable，它们很便宜，而且又少了一件需要担心的事情；

*   将您的部署自动化到 playstore

*   如果一个库很大，而你只使用了它的一小部分功能，你应该找一个替代的**更小的**选项(比如依靠[proguard](http://developer.android.com/tools/help/proguard.html))；

*   不要使用超过实际需要的模块。如果*的*模块没有不断地修改，重要的是要考虑到从头编译它们所需的时间( **CI** 构建就是一个很好的例子)，或者甚至检查之前的单个模块构建是否是最新的，可能比简单地将依赖项作为二进制文件加载多 4 倍。jar/.aar。

*   开始[考虑抛弃 png 转投 SVG](http://developer.android.com/tools/help/vector-asset-studio.html)；

*   制作库抽象类，如果你只需要在一个地方切换，切换到一个新的库会容易得多(例如，***【applogger . d(" message ")***可以包含***【log . d(TAG，message)*** ，后来意识到[***timber . d(message)***](https://github.com/JakeWharton/timber)是更好的选择)；

*   在使用 **wifi** 时，监控连接和连接类型(**更多** **数据** **更新**？);

*   监控电源和电池(**更多数据更新**同时**充电**？**当**电量低**时暂停更新**？);

*   用户界面就像一个笑话。如果非要解释，也没那么好；

*   测试对性能很重要:写慢(但正确)的实现，然后用测试验证优化没有破坏任何东西。