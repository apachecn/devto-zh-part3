# react . js–从 Dart #2 开始

> 原文：<https://dev.to/graphicbeacon/reactjsget-started-in-dart-2-21o2>

在本系列的第 1 部分中，我们构建了带有名称参数的`Greeting`组件，并将其呈现在屏幕上。下面是这个代码片段的样子:

```
var Greeting = createReactClass({
  "render": allowInteropCaptureThis(
    (ReactClassInterface self) => React.createElement(
      'h1', null, ['Hello, ${getProperty(self.props, "name")}']),
  )
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们通过这样做来使用它:

```
void main() {
  ReactDOM.render(
    React.createElement(
      Greeting,
      makeJsObject({
        'name': 'John'
      }),
      null,
    ),
    querySelector('#output'),
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一部分，我们将重构我们的解决方案，并继续构建有状态组件示例。这是视频:

[https://www.youtube.com/embed/bdrqQXg2Gjs](https://www.youtube.com/embed/bdrqQXg2Gjs)

→ [**在 YouTube 上观看**](https://youtu.be/bdrqQXg2Gjs)
→ [**获取源代码**](https://github.com/graphicbeacon/reactjs-get-started-in-dart/tree/part-2)

* * *

## 结论

我希望这是有见地的，你今天学到了一些新东西。

**订阅[我的 YouTube 频道](https://www.youtube.com/channel/UCHSRZk4k6e-hqIXBBM4b2iA?view_as=subscriber)，当第 3 部分准备就绪时会得到通知**。谢谢！

**喜欢、分享和[关注我](https://twitter.com/creativ_bracket)T3】😍有关 Dart 的更多内容。**

## 进一步阅读

1.  [js 包](https://pub.dartlang.org/packages/js)
2.  [如何在您的 Dart 应用程序中使用 JavaScript 库](https://dev.to/graphicbeacon/how-to-use-javascript-libraries-in-your-dart-applications--4mc6)
3.  [**用 Dart 进行全栈 web 开发**](https://bit.ly/fullstackdart)