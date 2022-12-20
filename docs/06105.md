# 通用块提供程序

> 原文：<https://dev.to/robertohuertasm/generic-bloc-provider-261k>

如果你正在使用[阻塞模式](https://hk.saowen.com/a/fbb6e484de022173fe85248875286060ce40d069c97420bc0be49d838e19e372)来管理你的 Flutter 应用程序的[状态，你可能已经厌倦了一遍又一遍地编写你的`BLoC providers`。我刚刚发布了一个简单的包，名为](https://flutter.io/docs/development/data-and-backend/state-mgmt) [Generic BLoC Provider](https://pub.dartlang.org/packages/generic_bloc_provider) ，可以帮助你解决这个问题。

## 感谢上帝我们有仿制药！

如果你对[集团模式](https://medium.com/flutterpub/architecting-your-flutter-project-bd04e144a8f1)不陌生，你可能也经历过与`BLoC providers`相关的样板文件。

也许你已经屈服于`copy & paste`的商品，你不关心这个，但是如果你像我一样，你讨厌有**重复的代码**，可能你已经创建了你自己的通用实现。

这很简单，不需要太多的[扑动](https://flutter.io/)知识来构建，但是同样，每当你不得不开始一个新项目时，你可能会发现你和你的老朋友`copy & paste`从一个项目到另一个项目复制你的通用实现，也许，在这个过程中改进它和改变它。

这完全是浪费时间。

这就是为什么我决定发布一个新的包来做这件事，一个被天真地称为 [generic_bloc_provider](https://pub.dartlang.org/packages/generic_bloc_provider) 的`BLoC provider`的**通用实现**。

## 学习新事物

在我构建这个包的时候，我还学习了如何使用 [Travis CI](https://travis-ci.org/) 来自动化**分析、测试和发布**一个 [Flutter](https://flutter.io/) 包的过程。

如果你想了解更多，请查看我之前在我的[博客](https://robertohuertas.com/2019/01/20/publish-flutter-package-with-travis/)或[媒体](https://medium.com/robertohuertasm/publishing-a-flutter-package-with-travis-ci-de09a166182b)上的帖子。😀

## 一些额外的功能

你可以在 [Github 的回购](https://github.com/robertohuertasm/generic_bloc_provider)或者 [Pub Dartlang](https://pub.dartlang.org/packages/generic_bloc_provider) 中看到如何使用这个包。

我尝试让软件包的用户自定义一些行为，即:

1.  [inheritFromWidgetOfExactType](https://docs.flutter.io/flutter/widgets/BuildContext/inheritFromWidgetOfExactType.html)vs[ancestorinheritelementforwidgetofexacttype](https://docs.flutter.io/flutter/widgets/BuildContext/ancestorInheritedElementForWidgetOfExactType.html)的用法，

2.  [updateShouldNotify](https://docs.flutter.io/flutter/widgets/InheritedWidget/updateShouldNotify.html) 的实现。

## 投稿

我希望你会发现它很有用，当然，你可以自由地打开一个问题，或者，更好的是，在 Github 的回购中有一些改进的 PR。

尽情享受吧！

-
最初发表于 2019 年 1 月 21 日[robertohuertas.com](https://robertohuertas.com/2019/01/21/generic_bloc_provider/)。