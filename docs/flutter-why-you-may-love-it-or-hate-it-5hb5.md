# 颤动——为什么你会喜欢或讨厌它

> 原文：<https://dev.to/brightdevs/flutter-why-you-may-love-it-or-hate-it-5hb5>

Flutter 是 Google 的一个新框架，用于创建跨平台的应用程序。到目前为止，我还没有尝试过创建任何跨平台的项目，所以我想尝试一下。这不会是下一个用 Flutter 创建应用的基础教程。我将只呈现我对那些让我又爱又恨的东西的第一印象
在玩了几个小时后飘起。

**镖**

在我开始实现我的第一个应用程序之前，我认为让自己为新的语言语法做好准备是明智的。
Flutter 只是一个框架。它使用的语言叫做飞镖。Dart 是 Google 创建的面向对象、类定义、垃圾收集的语言。它与 Kotlin、C#或 Swift 非常相似。你需要知道的一切都可以在 Flutter 的[入门](https://www.dartlang.org/guides/language)页面上找到。

# 飘起

值得一提的是，Flutter 的应用程序不像 React Native 或 NativeScript 代码那样编译。不仅仅是 UI 元素被编译，整个都是。Flutter 不使用特殊的桥梁在我们的代码和本机代码之间进行通信，它使用 C/C++库进行编译，因此它更接近于机器语言，并为我们提供了更好的性能。

**首次申请**

我决定用 Flutter 的“入门”文档继续我的旅程。我创建的第一个应用程序是从[这个](https://flutter.io/docs/get-started/codelab)教程一步步开发出来的
。
你可以在下面看到一个应用的完整代码:

```
import 'package:flutter/material.dart';
import 'package:english_words/english_words.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Startup Name Generator',
      home: RandomWords(),
    );
  }
}

class RandomWords extends StatefulWidget {
  @override
  RandomWordsState createState() => new RandomWordsState();
}

class RandomWordsState extends State<RandomWords> {
  final _suggestions = <WordPair>[];
  final _biggerFont = const TextStyle(fontSize: 18.0);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Startup Name Generator'),
      ),
      body: _buildSuggestions(),
    );
  }

  Widget _buildSuggestions() {
    return ListView.builder(
        padding: const EdgeInsets.all(16.0),
        itemBuilder: (context, i) {
          if (i.isOdd) return Divider();
          final index = i ~/ 2;
          if (index >= _suggestions.length) {
            _suggestions.addAll(generateWordPairs().take(10));
          }
          return _buildRow(_suggestions[index]);
        });
  }

  Widget _buildRow(WordPair pair) {
    return ListTile(
      title: Text(
        pair.asPascalCase,
        style: _biggerFont,
      ),
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

你可以注意到的第一件事是:

*   它只有一个文件——我们不像我在 Android 开发中习惯的那样在一个单独的文件中创建 UI，
*   它很短-大约 50 行的应用程序，创建一个永无止境的列表？挺牛逼的！
*   一切都是小部件——整个 Flutter 应用程序都是使用小部件创建的。每个 UI 元素(甚至是主屏幕块)都是一个小部件。创建 Flutter 应用程序只是创建一个窗口部件树。

让我们一步一步地快速解释一下发生了什么:

*   我们在调用`runApp()`的`main()`函数中启动一个应用程序，
*   在`MyApp`类中，我们返回带有标题的`MaterialApp`和布局的其余部分- `RandomWords`类(当然是一个小部件)，
*   在`RandomWordsState`中，我们创建了`State`,这是我们想要在里面显示的`RandomWords`类的逻辑，
*   被覆盖的`build`方法创建了小部件支架，该小部件支架在其`body`中调用函数`buildSuggestions()`，该函数将返回给我们一个`ListView`小部件，
*   `buildSuggestions()`为奇数索引创建一个分隔线(简单的水平线),为偶数索引创建一个分隔线，使用`buildRow()`函数放大一个`ListTile`小部件，
*   最后，`buildRow()`使用保存在`suggestions`数组中的`english_words`库生成的字符串创建一个简单的文本小部件。

起初，将布局和逻辑代码结合在一个地方对我来说似乎很奇怪，但过了一段时间后，我就习惯了。

**热重装**

Flutter 支持一个很棒的开发特性。Hot Reload 允许您在
中进行一些简单的更改后立即重新运行应用程序。太棒了！我不用每次做一个简单的改动就等一分钟了吧？良好的..不一定，因为
并不是每次都管用。假设我们想在应用程序中改变文本的颜色。
我们将`_biggerFont`实施调整为:

```
final _biggerFont = const TextStyle(fontSize: 18.0, color: Colors.green); 
```

Enter fullscreen mode Exit fullscreen mode

然后保存(保存文件也应用热重装)和...什么都没发生。列表
中的文本不会改变颜色。我们必须完全重新运行应用程序才能看到变化。
现在，如果我们尝试在按下保存后将文本颜色更改为蓝色，热重新加载确实有效，但正如你所见，在大多数情况下，最好是
完全重新运行应用程序，以确定某些东西是否有效。尽管如此，重新运行 Flutter 应用程序
似乎比重新运行传统的 Android 代码要快得多。

**材料设计**

正如你可能已经注意到的那样，Flutter 应用程序中的一切都取决于材料设计。就连主应用容器
也被称为`MaterialApp`。就个人而言，我认为这很棒，因为我喜欢那里所有的用户界面设计，但是对于 iOS 用户来说,
可能会很奇怪。每个 Flutter 应用程序在每个平台上看起来都完全一样。你显然可以改变一些布局，这取决于
它运行的设备，但它不会自动调整组件的风格来匹配特定的平台。

**我爱扑扑的东西**

*   许多预定义的小部件——如果你想在工具栏上添加浮动按钮、抽屉导航器甚至一些动作按钮，只需要几行代码就可以实现。所有的基本功能(如工具栏-后退按钮或抽屉的-打开)都已经实现，
*   动画的简单实现，
*   仍在开发中——Google 一直在添加新功能，新的第三方库正在创建，你已经可以在 StackOverflow 上找到很多帮助，
*   比 JavaScript 的所有方法都更好的性能，
*   热重装-它实际上有帮助:d。

**我讨厌的东西在飘动**

*   。yaml 文件——我花了一些时间试图修复一个简单的 bug，它是由一个额外的空白引起的。当你添加图像时，你需要在`pubspec.yaml`文件中指定它，我在那里打错了。幸运的是，[在线验证者](http://www.yamllint.com/)来拯救我们，
*   用逗号结束每个小部件的参数——添加完小部件的最后一个参数后，应该用`,`结束。否则代码的格式会很糟糕/难看。这有时会导致代码最终看起来像这样:

```
 ],
      ),
    ),
  ),
); 
```

Enter fullscreen mode Exit fullscreen mode

*   以分号结束行——在习惯了 Kotlin 之后，我不得不再次添加分号，这真的很烦人(但我想这更像是一件冒险的事情)。

我应该使用它吗？

一切都取决于项目的范围。如果你想自己开发多平台应用程序，使用跨平台的方法比为每个平台编写本地代码更容易。在 Flutter 和 React Native / NativeScript 之间进行选择取决于您。得益于不断的发展，在我看来它能跑赢他的竞争对手，但它还没有做到。
你还需要记住，跨平台的方法总是缺少一些特性。对于
这个非常特殊的任务，您很可能需要添加一些本地代码。幸运的是，使用 Flutter 也不是很难。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

安卓开发者@光明发明
[电子邮件](//lukasz.reszetow@brightinventions.pl)