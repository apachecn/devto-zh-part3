# react . js–快速入门

> 原文：<https://dev.to/graphicbeacon/reactjsget-started-in-dart-3-2697>

在第 2 部分中，我们继续查看 React 文档示例，同时在 Dart 中实现它们。我们从重构`createReactClass`函数开始，使用**命名参数**，试图简化组件的编写:

```
// web/components/ticker.dart
...
...
var Ticker = createReactClass(
    getInitialState: () => makeJsObject({
          "seconds": 0,
        }),
    componentDidMount: (ReactClassInterface self) {
      self.interval = Timer.periodic(Duration(seconds: 1), (_) => self.tick());
    },
    componentWillUnmount: (ReactClassInterface self) {
      self.interval.cancel();
    },
    render: (ReactClassInterface self) => React.createElement(
          'div',
          null,
          ['Seconds ${getProperty(self.state, "seconds")}'],
        ),
    methodMap: {
      "tick": (ReactClassInterface self) {
        self.setState((dynamic state) {
          var seconds = getProperty(state, "seconds") as int;
          return makeJsObject({
            "seconds": seconds + 1,
          });
        });
      }
}); 
```

Enter fullscreen mode Exit fullscreen mode

[**查看来源**](https://github.com/graphicbeacon/reactjs-get-started-in-dart/blob/part-2/web/components/ticker.dart#L6-L30)

它的用法:

```
// web/main.dart
ReactDOM.render(
  React.createElement(
    Ticker,
    null,
    null,
  ),
  querySelector('#output2')); 
```

Enter fullscreen mode Exit fullscreen mode

→ [**在 YouTube 上观看 Part 3**](https://youtu.be/kmCJ-qkBjrc)
→[**获取源代码**](https://github.com/graphicbeacon/reactjs-get-started-in-dart)

* * *

在这最后一部分，我们将使用 [**react**](https://pub.dartlang.org/packages/react) 包来构建其他示例。 **react** 包为构建定制组件提供了更加友好的 API:

```
import 'dart:async';

import 'package:react/react.dart';

class TickerComponent extends Component {
  Timer interval;

  tick() { ... }

  @override
  Map getInitialState() => {'seconds': 0};

  @override
  componentDidMount() { ... }

  @override
  componentWillUnmount() { ... }

  @override
  render() => div({}, 'Seconds ${state["seconds"]}');
}

var Ticker = registerComponent(() => TickerComponent()); 
```

Enter fullscreen mode Exit fullscreen mode

观看此视频了解更多信息:

[https://www.youtube.com/embed/kmCJ-qkBjrc](https://www.youtube.com/embed/kmCJ-qkBjrc)

→ [**在 YouTube 上观看**](https://youtu.be/kmCJ-qkBjrc)
→ [**获取源代码**](https://github.com/graphicbeacon/reactjs-get-started-in-dart)

* * *

## 结论

我希望这是有见地的，你今天学到了一些新东西。

**订阅[我的 YouTube 频道](https://www.youtube.com/channel/UCHSRZk4k6e-hqIXBBM4b2iA?view_as=subscriber)谢谢！**

**喜欢、分享和[关注我](https://twitter.com/creativ_bracket)T3】😍有关 Dart 的更多内容。**

## 进一步阅读

1.  [**反应过来**包](https://pub.dartlang.org/packages/react)
2.  [如何在您的 Dart 应用程序中使用 JavaScript 库](https://dev.to/graphicbeacon/how-to-use-javascript-libraries-in-your-dart-applications--4mc6)
3.  [**用 Dart 进行全栈 web 开发**](https://bit.ly/fullstackdart)