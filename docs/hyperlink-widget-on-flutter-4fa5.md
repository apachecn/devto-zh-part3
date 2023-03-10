# Flutter 上的超链接小部件

> 原文：<https://dev.to/guimg/hyperlink-widget-on-flutter-4fa5>

本教程是关于如何从我们的应用程序打开一个网络浏览器。就像 HTML 超链接一样。为此，我们将使用包`url_launcher`，你可以在这里找到[。](https://pub.dartlang.org/packages/url_launcher)

## 将包添加到您的项目中

要将`url_launcher`包添加到我们的项目中，我们必须将它包含到我们的`pubspec.yaml`文件依赖项中，就像这样:

```
dependencies:
  flutter:
    sdk: flutter

  url_launcher: ^5.0.2 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须告诉 Flutter 下载所有的依赖项。打开终端，进入 app 文件夹，输入:

```
flutter packages get 
```

Enter fullscreen mode Exit fullscreen mode

## 创建超链接小工具

在文件夹`lib`中，创建一个名为`widgets`的新文件夹。在这个`widgets`文件夹中，创建一个名为`Hyperlink.dart`的新文件。这个文件看起来像这样:

```
import 'package:flutter/material.dart';

import 'package:url_launcher/url_launcher.dart';

class Hyperlink extends StatelessWidget {
  final String _url;
  final String _text;

  Hyperlink(this._url, this._text);

  _launchURL() async {
    if (await canLaunch(_url)) {
      await launch(_url);
    } else {
      throw 'Could not launch $_url';
    }
  }

  @override
  Widget build(BuildContext context) {
    return InkWell(
      child: Text(
        _text,
        style: TextStyle(decoration: TextDecoration.underline),
      ),
      onTap: _launchURL,
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

记得导入`url_launcher`包。这个小部件获得两个字符串，url 和要显示的文本。它创建了一个`InkWell`小部件来管理`onTap`事件并调用 url 启动器。

## 使用微件

现在，在我们的`main.dart`文件中，我们可以调用这个小部件。记得从`widgets`文件夹导入文件。

```
import 'package:flutter/material.dart';

import './widgets/Hyperlink.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Hyperlink tutorial',
      theme: ThemeData(
          brightness: Brightness.dark
          ),
      home: Scaffold(
        body: Center(
          child: Hyperlink('https://guimgonzalez.business/', 'Awesome website!'),
        ),
      ),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这个！如果您有任何疑问或代码建议，请在下面的评论中留下。谢了。