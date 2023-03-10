# 为什么我明年会爱上 Flutter

> 原文：<https://dev.to/nichartley/why-ill-love-flutter-next-year-3fg2>

如果你稍微用过 [Flutter](https://flutter.io/) ，你可能会对它欣喜若狂。一个跨平台的移动开发套件？哪个*实际上跨平台工作*，没有特定于平台的配置？热重启和有状态热重装，因此您可以在几秒钟内测试更改？不需要弄乱特定于平台的配置文件？你所有的布局都是用语言本身完成的？！

总而言之，这似乎是一件完美的事情。尤其是热重装和热重启，更是让人爱不释手。我真的无法用语言来表达等待*秒*而不是几分钟，在你的设备上看到你的改变有多有用。

不过，你越是使用 Flutter，最初的温暖就流失得越多，取而代之的是冷酷地意识到它有其局限性，就像其他任何东西一样。

其中之一就是发送通知。如果你正在开发一个完全离线的应用程序——在我的例子中，是一个手机游戏——那么让通知工作就不容易了。当然，你可以通过 [FCM](https://firebase.google.com/docs/cloud-messaging/) (因为 GCM 在今年四月[逐步淘汰](https://developers.google.com/cloud-messaging/)取而代之)，但那将需要我在服务器之间往返一次，只是为了从设备上的应用程序向设备发送通知，或者至少在我不需要或不想要它的时候捆绑该功能。

那么如何在 Flutter 中做到离线通知呢？

简单的回答就是不能。当然，你可以调用本机代码，但是 Flutter 的目的是*避免*自己编写本机代码，让 Flutter 自动处理它。取而代之的是，你必须寻找第三方库，除了你不能安排或分组通知之外，[排序工作](https://pub.dartlang.org/packages/flutter_local_notifications)。(也许你可以，但我无法让它工作。)

那种小事到处都是。例如，到了发布你的应用程序的时候——甚至给它一个漂亮的图标——你需要做[平台](https://flutter.io/docs/deployment/android)特定的[事情](https://flutter.io/docs/deployment/ios)(公平地说，可以是[自动化](https://flutter.io/docs/deployment/fastlane-cd))来完成。很容易从单个高分辨率文件中生成许多平台无关的静态图标，就像 [`flutter_launcher_icons`](https://pub.dartlang.org/packages/flutter_launcher_icons) 所做的那样；权限可能来自于`pubspec.yaml`中的列表，甚至是代码分析。

这并不是说颤动不好。它是新的。它只有两年的历史，当你正在构建像开发跨平台移动应用程序的全新方式这样复杂的东西时，一些早期的磕磕绊绊是可以预料的。它有很多承诺，我认为他们很快就会达到他们应该达到的目标，但他们还没有达到。

简而言之，明年我会更喜欢 Flutter。