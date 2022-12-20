# 如何不从颤振开始

> 原文：<https://dev.to/rubensdemelo/how-to-not-start-with-flutter-1dcl>

# 如何不从颤振开始

几天前，我在 twitter 上发布了我学习 Flutter 的最大错误:

[![](img/da52341225c8a3971dd27eebf34985c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dsdiQomn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v70ovz2syg4r2u2pfooq.png)

*(现在，供应商包是 Flutter docs 的新建议)*

受到大量关于“BLoC”模式的堆栈溢出问题的激励，我决定在 Twitter 上分享(并意外地获得了一些不错的反馈)，现在写这篇文章来帮助未来的 flutter 开发者。

几乎每一天，flutter 上的新成员都会发布关于“阻塞”甚至更糟的问题，关于让实现这种模式变得“简单”的包。

一开始你还有其他真正重要的话题:

*   镖语(即使不难)
*   小部件树
*   框约束
*   基本部件
*   无状态和有状态小部件
*   航行
*   小部件生命周期
*   材料和 Cupertino 包装
*   键

当您需要在小部件之间共享值或在小部件树中传递值时，使用 InheritedWidget，这很容易，而且可能满足您最初的需求。如果您已经使用 MediaQuery.of(context)或 Theme.of(context)，则您已经使用了 [InheritedWidget](https://api.flutter.dev/flutter/widgets/InheritedWidget-class.html) 。

即使在对上述主题有了很好的了解之后，当您开始使用状态管理时，还有一些优秀的包可以开始使用: [scoped_model](https://pub.dev/packages/scoped_model) 和最近最强大的[提供者](https://pub.dev/packages/provider)。强烈推荐 [scoped_model](https://pub.dev/packages/scoped_model) 。它易于理解，记录良好，适用于中小型应用程序(无论应用程序有多复杂)，并且是 Flutter docs 推荐的第一个关于状态管理的包(现在[提供者](https://pub.dev/packages/provider)是新的 black)。

请记住，状态管理不是一个简单的话题，没有更好的方法和 Flutter 让您选择您想要的任何解决方案。在您的旅程中，最重要的是发现哪种解决方案符合您的需求，以及在您的应用程序中使用哪种解决方案更舒适、更有信心。

总之，我想指出，这篇文章并不反对 Bloc 模式(绝对不是)。这只是给那些刚到达 Dart & Flutter 端的人的一个建议，当他们不需要时，他们可能会在尝试实现 Bloc 时受挫。