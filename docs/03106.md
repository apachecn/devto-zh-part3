# 颤振中的导航

> 原文：<https://dev.to/newtonmunene_yg/navigation-in-flutter-2eam>

## 先决条件

所有的应用程序，复杂的和简单的，都需要某种形式的导航。它改善了总体用户体验。导航有不同的方法，其中最受欢迎的移动应用是 stacks。Flutter 也使用了弹出页面和将页面推到堆栈上的概念。

您需要具备以下基本知识:

1.  镖
2.  小部件(有状态和无状态)

#### 命名路线

如果你是一个来自 Web 开发的开发人员，主要是 angular 和 react，你将熟悉路线导航。Flutter 也提供了这个选项，我发现它更容易使用，因为语法更清晰。

您需要在`MaterialApp widget`中定义您的路线。routes 参数将一个`String`的`Map`作为键，将一个`WidgetBuilder`作为值

```
import 'package:flutter/material.dart';

main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      routes: {
        "/": (BuildContext context) => StartPage(),
        "/login": (BuildContext context) => LoginPage(),
        "/register": (BuildContext context) => RegisterPage(),
        "/home": (BuildContext context) => HomePage(),
      },
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 警告:当您指定根路径("`/`")时，您不应该指定 home 小部件。

然后你可以调用`Navigator.pushNamed(BuildContext context, String routeName)`或`Navigator.of(BuildContext context).pushNamed(String routeName)`
这将添加一个新的页面到栈顶。

```
import 'package:flutter/material.dart';

class StartPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        child: Center(
          child: RaisedButton(
            child: Text(
              'Go To Home Page',
            ),
            onPressed: () {
              Navigator.pushNamed(context, "/home");
            },
          ),
        ),
      ),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

要返回上一页，你可以调用`Navigator.pop(BuildContext context)`或`Navigator.of(BuildContext context).pop()`或
，你也可以使用`Navigator.pushReplacementNamed(BuildContext context, String routeName)`来清除堆栈，并在上面推一个新的页面。这意味着你回不去了。
这里还有一些代码示例:

```
Navigator.pushNamed(BuildContext context, String "/home");
Navigator.of(BuildContext context).pushNamed(String routeName);

Navigator.pushReplacementNamed(BuildContext context, String routeName);
Navigator.of(BuildContext context).pushpushReplacementNamedNamed(String routeName);

Navigator.pop(BuildContext context);
Navigator.of(BuildContext context).pop(); 
```

Enter fullscreen mode Exit fullscreen mode

#### 长征

MaterialApp 小部件中有一个 routes 参数的替代方法是`onGenerateRoute`。这将动态生成您的路线。

```
import 'package:flutter/material.dart';

main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      onGenerateRoute: (RouteSettings settings) {
        switch (settings.name) {
          case '/':
            return MaterialPageRoute(builder: (context) => StartPage());
            break;
          case '/home':
            return MaterialPageRoute(builder: (context) => HomePage());
            break;
        }
      },
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以像导航命名路线一样进行导航。

```
Navigator.pushNamed(BuildContext context, String "/home"); 
```

Enter fullscreen mode Exit fullscreen mode

#### MaterialPageRoute

使用这种方法不需要定义路线。因为我有网络背景，所以我更喜欢前一种方法，但这种方法也很重要。

```
import 'package:flutter/material.dart';

class StartPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        child: Center(
          child: RaisedButton(
            child: Text(
              'Go To Home Page',
            ),
            onPressed: () {
              Navigator.push(
                  BuildContext context,
                   MaterialPageRoute(
                      builder: (context) =>  HomePage()));
            },
          ),
        ),
      ),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

就像命名的路径一样，你也可以调用`pop`返回堆栈，调用`pushReplacement`清空堆栈并添加一个页面作为堆栈的顶部。

```
Navigator.push(BuildContext context,MaterialPageRoute(builder: (context) =>  
HomePage(),),);
Navigator.pushReplacement(BuildContext context,  MaterialPageRoute(builder: (context) =>  
HomePage(),),);
Navigator.pop(BuildContext context); 
```

Enter fullscreen mode Exit fullscreen mode

基本情况就是这样。Flutter 负责 Android 和 IOS 的页面过渡动画，这样您就可以专注于重要的部分。在以后的帖子中，我将介绍:

1.  自定义页面过渡
2.  来回传递数据

谢谢你。