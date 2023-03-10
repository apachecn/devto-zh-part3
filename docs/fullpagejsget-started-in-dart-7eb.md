# full page . js–Dart 入门

> 原文：<https://dev.to/graphicbeacon/fullpagejsget-started-in-dart-7eb>

在今天的教程中，我们将学习如何在 Dart web 项目中使用 fullPage.js 演示库。我们将使用 Dart 团队的 **js** 包来实现这一点。

## 设置

安装 [**stagehand**](https://pub.dartlang.org/packages/stagehand) 并生成一个 web 项目:

```
$ pub global activate stagehand
$ mkdir my_project && cd my_project
$ stagehand web-simple 
```

Enter fullscreen mode Exit fullscreen mode

修改 **pubspec.yaml** 文件的`dependencies`部分:

```
dependencies:
  js: ^0.6.0 
```

Enter fullscreen mode Exit fullscreen mode

并更新您的依赖关系:

```
$ pub get 
```

Enter fullscreen mode Exit fullscreen mode

以下是完整的视频教程，请按照设置步骤操作:

[https://www.youtube.com/embed/qwpAEcSl43o](https://www.youtube.com/embed/qwpAEcSl43o)

→ [**在 YouTube 上观看**](https://youtu.be/qwpAEcSl43o)
→ [**获取源代码**](https://github.com/graphicbeacon/js-dart-interop-samples/tree/master/fullpage)

* * *

## 结论

我希望这是有见地的，你今天学到了一些新东西。

**订阅[我的 YouTube 频道](https://www.youtube.com/channel/UCHSRZk4k6e-hqIXBBM4b2iA?view_as=subscriber)用 Dart** 学习全栈 web 开发。

**喜欢、分享和[关注我](https://twitter.com/creativ_bracket)T3】😍有关 Dart 的更多内容。**

## 进一步阅读

1.  [js 包](https://pub.dartlang.org/packages/js)
2.  [如何在您的 Dart 应用程序中使用 JavaScript 库](https://dev.to/graphicbeacon/how-to-use-javascript-libraries-in-your-dart-applications--4mc6)
3.  [**fullPage.js 库**](https://alvarotrigo.com/fullPage/)