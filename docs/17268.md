# react . js–快速入门

> 原文：<https://dev.to/graphicbeacon/reactjsget-started-in-dart-1-37ko>

新年快乐！

在这个由三部分组成的系列中，我们将浏览 React.js 主页示例，并学习如何使用 **js** interop 包在 Dart 中重现这些示例。

[https://www.youtube.com/embed/DCgMfj-eFuE](https://www.youtube.com/embed/DCgMfj-eFuE)
→ [**在 YouTube 上观看**](https://youtu.be/DCgMfj-eFuE)
→ [**获取源代码**](https://github.com/graphicbeacon/reactjs-get-started-in-dart/tree/part-1)

* * *

# 在我们开始之前:

## 1。设置您的项目

使用 Stagehand 快速设置您的 web 项目:

```
$ mkdir getting_started_react && cd getting_started_react
$ stagehand web-simple 
```

Enter fullscreen mode Exit fullscreen mode

## 2。安装 js 互操作包

确保`js`依赖项被添加到您的`pubspec.yaml`文件:

```
dependencies:
  js: ^0.6.0 
```

Enter fullscreen mode Exit fullscreen mode

保存并运行`pub get`以更新您的依赖关系。

## 3。导入 React.js 库

在`web/index.html`中的`<head>`之前`<script defer src="main.dart.js"></script>`导入 dev 版本的库:

```
<!-- Note: when deploying, replace "development.js" with "production.min.js". -->
<script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script> 
```

Enter fullscreen mode Exit fullscreen mode

→ [**观看完整视频**](https://youtu.be/DCgMfj-eFuE)
→ [**获取源代码**](https://github.com/graphicbeacon/reactjs-get-started-in-dart/tree/part-1)

* * *

## 结论

我希望这是有见地的，你今天学到了一些新东西。

**订阅[我的 YouTube 频道](https://www.youtube.com/channel/UCHSRZk4k6e-hqIXBBM4b2iA?view_as=subscriber)，当第二部分准备就绪时会得到通知**。谢谢！

**喜欢、分享和[关注我](https://twitter.com/creativ_bracket)T3】😍有关 Dart 的更多内容。**

## 进一步阅读

1.  [js 包](https://pub.dartlang.org/packages/js)
2.  [如何在您的 Dart 应用程序中使用 JavaScript 库](https://dev.to/graphicbeacon/how-to-use-javascript-libraries-in-your-dart-applications--4mc6)
3.  [**用 Dart 进行全栈 web 开发**](http://bit.ly/2P2N1jC)