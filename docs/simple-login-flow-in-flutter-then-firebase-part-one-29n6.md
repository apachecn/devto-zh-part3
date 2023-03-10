# 首先是 Flutter 中的简单登录流程，然后是 Firebase -第一部分

> 原文：<https://dev.to/aaronksaunders/simple-login-flow-in-flutter-then-firebase-part-one-29n6>

# 简单的 Firebase 登录流程在颤振

我们将创建一个包含以下组件的简单应用程序

*   默认主应用入口点
    *   使用 [FutureBuilder 小部件](https://api.flutter.dev/flutter/widgets/FutureBuilder-class.html)在呈现 UI 之前等待数据，这一概念贯穿整个应用程序
*   登录页面
*   主页
*   认证服务
    *   演示在 Flutter 文档中讨论的提供者的使用[简单的应用状态管理](https://flutter.dev/docs/development/data-and-backend/state-mgmt/simple#accessing-the-state)

网上有很多关于为 Flutter 设置 Firebase 的例子，所以我将直接跳到代码中，而不是从基础开始。

> 参见[Google CodeLabs Flutter for Firebase](https://codelabs.developers.google.com/codelabs/flutter-firebase/index.html?index=..%2F..index#5)了解在 iOS 或 Android 上设置项目的逐步说明

### 在 Firebase 中创建一个测试用户

由于我们只是构建应用程序，现在还没有在应用程序中创建用户的功能，请登录到您的 Firebase 控制台，并向您的项目添加一个用户。请确保在 Firebase 控制台中更新项目时启用电子邮件身份验证。

### 清理默认的颤振项目

首先让我们创建项目

```
flutter create simple_firebase_auth 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们做一些项目清理，打开项目并从文件`main.dart`中删除现有的`HomePage`和`HomePageState`小部件。

更改`MaterialApp`小部件的`home`属性，以指向我们将在下一节中创建的`LoginPage`小部件

完成后，该文件看起来应该与此类似

```
import 'package:flutter/material.dart';
import 'package:simple_firebase_auth/login_page.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: LoginPage(),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 创建 LoginPage 小部件

让我们浏览一下应用程序的`LoginPage`的创建过程。我们需要捕获一个`email`和一个`password`来传递给`AuthService`以调用登录函数。

我们将创建一个简单的页面，其中包含所需的`TextFormField`小部件和一个`RaisedButton`，单击它即可登录。

1.  打开编辑器，在`lib`目录下创建一个名为`login_page.dart`的新文件
2.  将以下内容粘贴到文件`login_page.dart`

```
import 'package:flutter/material.dart';

class LoginPage extends StatefulWidget {
  @override
  _LoginPageState createState() => _LoginPageState();
}

class _LoginPageState extends State<LoginPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Login Page Flutter Firebase"),
      ),
      body: Center(
        child: Text('Login Page Flutter Firebase  Content'),
      ),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您应该能够运行代码来查看屏幕现在的样子。当我们在 UI 中工作时，请确保将默认路线或`main.dart`小部件中的`home`属性更改为`LoginPage`,这样您就可以看到实时重新加载的变化

#### 样式和添加文本字段

让我们将页面的主体设为居中的`Column`，列的子列主要是`TextFormField`和`RaisedButton`

放置表单字段和按钮的居中容器

```
 body: Container(
      padding: EdgeInsets.all(20.0),
      child: Column()
    ) 
```

Enter fullscreen mode Exit fullscreen mode

接下来添加实际的表单域小部件和按钮，作为`Column`小部件的子部件。我们将对表单字段进行一些基本的样式设计，使其看起来更像样。关于[文本表单域](https://api.flutter.dev/flutter/material/TextFormField-class.html)T3 的更多信息，请参见 Flutter 文档

```
 body: Container(
    padding: EdgeInsets.all(20.0),
    child: Column(
      children: <Widget>[
        Text(
          'Login Information',
          style: TextStyle(fontSize: 20),
        ),
        TextFormField(
            keyboardType: TextInputType.emailAddress,
            decoration: InputDecoration(labelText: "Email Address")),
        TextFormField(
            obscureText: true,
            decoration: InputDecoration(labelText: "Password")),
        RaisedButton(child: Text("LOGIN"), onPressed: () {}),
      ],
    ),
  ), 
```

Enter fullscreen mode Exit fullscreen mode

让我们在列中的字段之间增加一些间距，这样更好看。我们将使用`SizedBox`小部件并设置`height`属性来在应用程序中获得一些间距。替换`Column`小部件的`children`属性，以获得期望的间距

```
 children: <Widget>[
    SizedBox(height: 20.0),    // <= NEW
    Text(
      'Login Information',
      style: TextStyle(fontSize: 20),
    ),
    SizedBox(height: 20.0),   // <= NEW
    TextFormField(
        keyboardType: TextInputType.emailAddress,
        decoration: InputDecoration(labelText: "Email Address")),
    TextFormField(
        obscureText: true,
        decoration: InputDecoration(labelText: "Password")),
    SizedBox(height: 20.0),  // <= NEW
    RaisedButton(child: Text("LOGIN"), onPressed: () {}),
  ], 
```

Enter fullscreen mode Exit fullscreen mode

#### 从表单字段获取文本值

我们将使用一个`Form`小部件和一个`GlobalKey`，关于这些概念的更多信息可以在 flutter cookbook 章节[中找到，构建一个带验证的表单](https://flutter.dev/docs/cookbook/forms/validation)

在`LoginPage`小部件
中添加 formKey

```
class _LoginPageState extends State<LoginPage> {
  final _formKey = GlobalKey<FormState>(); 
```

Enter fullscreen mode Exit fullscreen mode

然后添加两个新字段来保存我们需要发送到 Firebase 进行身份验证的电子邮件地址和密码值

```
class _LoginPageState extends State<LoginPage> {
  final _formKey = GlobalKey<FormState>();
  String _password;
  String _email; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将属性`onSaved`添加到电子邮件和密码的`TextFormFields`中，当在表单上调用`save`方法时，保存方法上的所有小部件都将被调用来更新本地变量。

```
 TextFormField(
      onSaved: (value) => _email = value,    // <= NEW
      keyboardType: TextInputType.emailAddress,
      decoration: InputDecoration(labelText: "Email Address")),
  TextFormField(
      onSaved: (value) => _password = value, // <= NEW
      obscureText: true,
      decoration: InputDecoration(labelText: "Password")), 
```

Enter fullscreen mode Exit fullscreen mode

用一个新的`Form`小部件包装`Column`小部件，代码应该类似于这个

```
 body: Container(
        padding: EdgeInsets.all(20.0),
        child: Form(          // <= NEW
          key: _formKey,      // <= NEW
          child: Column(
            children: <Widget>[
            ....
            ],
          ),
        ),
      ), 
```

Enter fullscreen mode Exit fullscreen mode

既然设置了字段，更新了`TextFormField`，我们不仅可以使用`_formKey`来验证提供的字段，还可以通过调用`save`方法在本地获取值。

将`RaisedButton` `onPressed`方法中的代码替换为以下代码，您将看到我们正在获取在 out 小部件中设置的电子邮件和密码的值。我们现在可以将这些值传递给包装了 Firebase 登录功能的`AuthService`。

```
 // save the fields..
    final form = _formKey.currentState;
    form.save();

    // Validate will return true if is valid, or false if invalid.
    if (form.validate()) {
      print("$_email $_password");
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 创建主页小部件

现在，我们将保持主页简单，因为我们只是试图演示流程是如何工作的。忽略注释掉的`LogoutButton`小部件，我们将在本教程的后面部分讨论。

1.  打开编辑器，在`lib`目录下创建一个名为`home_page.dart`的新文件
2.  将以下内容粘贴到文件`home_page.dart`

```
import 'package:flutter/material.dart';

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  @override
  Widget build(BuildContext context) {
    return  Scaffold(
      appBar: AppBar(
        title: Text("Home Flutter Firebase"),
        //actions: <Widget>[LogoutButton()],
      ),
      body: Center(
        child: Text('Home Page Flutter Firebase  Content'),
      ),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  打开`main.dart`并添加以下导入语句

```
import 'home_page.dart'; 
```

Enter fullscreen mode Exit fullscreen mode

1.  将`home`属性更改如下:

```
home: HomePage(title: 'Flutter Demo Home Page'), 
```

Enter fullscreen mode Exit fullscreen mode

这样您就可以验证页面是否正常工作

```
home: HomePage(), 
```

Enter fullscreen mode Exit fullscreen mode

### 为认证服务创建模板

在这里，我们将构建独立于 Firebase 的身份验证服务，验证一切正常，然后集成 Firebase。

在这个服务中，我们使用一个名为`ChangeNotifier`的 mixin 和一个方法`notifyListeners`,这将允许使用这个服务的小部件在方法被调用时被更新。当我们更新`currentUser`属性时，我们调用`notifyListeners`，因为这意味着用户已经登录或注销，我们希望应用程序根据用户状态进行更新。

> 关于`Provider`和状态管理的更多信息可以在[颤振文档](%5Bhttps://flutter.dev/docs/development/data-and-backend/state-mgmt/simple%5D(https://flutter.dev/docs/development/data-and-backend/state-mgmt/simple))中找到

我们需要的基线如下:

```
import 'dart:async';
import 'package:flutter/material.dart';

class AuthService with ChangeNotifier {
  var currentUser;

  AuthService() {
    print("new AuthService");
  }

  Future getUser() {
    return Future.value(currentUser);
  }

  // wrappinhg the firebase calls
  Future logout() {
    this.currentUser = null;
    notifyListeners();
    return Future.value(currentUser);
  }

  // wrapping the firebase calls
  Future createUser(
      {String firstName,
      String lastName,
      String email,
      String password}) async {}

  // logs in the user if password matches
  Future loginUser({String email, String password}) {
    if (password == 'password123') {
      this.currentUser = {'email': email};
      notifyListeners();
      return Future.value(currentUser);
    } else {
      this.currentUser = null;
      return Future.value(null);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将在服务调用`currentUser`中保留一个本地属性，它是存储用户的对象，当用户调用`login`方法时，如果密码匹配，我们将设置`currentUser`，用户将登录。这将在调用`getUser`方法时向用户提供。为了注销用户，我们将把`currentUser`属性设置为空，表示我们不再登录系统。

### 应用程序启动时确定用户状态

使用应用程序的第一个挑战是确定应用程序启动时打开哪个页面。这里我们想做的是确定我们是否有一个用户。我们将使用上面创建的`AuthService`并结合 flutter 的`FutureBuilder`小部件来呈现`HomePage`或`LoginPage`的正确首页

#### 使用提供者

在`main.dart`中，我们需要更新默认的`main`方法，如下所示；我们用`ChangeNotifierProvider`包装整个应用程序，以获得扫描窗口小部件树并找到类型为`AuthService`的对象的能力。

```
void main() => runApp(
      ChangeNotifierProvider<AuthService>(
        child: MyApp(),
        builder: (BuildContext context) {
          return AuthService();
        },
      ),
    ); 
```

Enter fullscreen mode Exit fullscreen mode

#### 修改 MyApp 小工具

进入`main.dart`并进行以下更改，这将允许`MyApp`小部件设置路线。这个小部件将决定当应用程序启动时是应该导航到`HomePage`小部件还是`LoginPage`小部件。

```
class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(primarySwatch: Colors.blue),
      home: FutureBuilder(
        // get the Provider, and call the getUser method
        future: Provider.of<AuthService>(context).getUser(),
        // wait for the future to resolve and render the appropriate
        // widget for HomePage or LoginPage
        builder: (context, AsyncSnapshot snapshot) {
          if (snapshot.connectionState == ConnectionState.done) {
            return snapshot.hasData ? HomePage() : LoginPage();
          } else {
            return Container(color: Colors.white);
          }
        },
      ),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 第一部分结束

此时，您应该有一个具有基本登录流的正常工作的应用程序，如果您提供密码 123，用户将成功登录

我添加了一点额外的功能，如果出现某种错误，就在对话框中显示错误消息。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[aaronksaunders](https://github.com/aaronksaunders)/[simple _ firebase _ auth](https://github.com/aaronksaunders/simple_firebase_auth)

### Flutter 中简单的 Firebase 登录流程

<article class="markdown-body entry-content container-lg" itemprop="text">

# Flutter 中简单的 Firebase 登录流程

单击此处查看“阅读我”的分步说明

*   [Flutter 中简单 Firebase 登录流程的文档-第一部分](https://github.com/aaronksaunders/simple_firebase_auth/blob/master/readme1.md)源代码在[分支](https://github.com/aaronksaunders/simple_firebase_auth/tree/completed-part-one)
*   [Flutter 中简单 Firebase 登录流程的文档-第二部分](https://github.com/aaronksaunders/simple_firebase_auth/blob/master/readme2.md)源代码在[头](https://github.com/aaronksaunders/simple_firebase_auth/)上

> 这里有一个关于登录和帐户创建的更完整的例子
> 
> *   [https://github.com/aaronksaunders/flutter_firebase_auth_app](https://github.com/aaronksaunders/flutter_firebase_auth_app)

## 入门指南

这个项目是颤振应用的起点。

如果这是你的第一个 Flutter 项目，一些资源可以帮助你开始:

*   [实验室:编写你的第一个颤振应用](https://flutter.dev/docs/get-started/codelab)
*   [食谱:有用的颤振样本](https://flutter.dev/docs/cookbook)

要获得关于 Flutter 的入门帮助，请查看我们的[在线文档](https://flutter.dev/docs)，其中提供了教程、示例、移动开发指南和完整的 API 参考。

</article>

[View on GitHub](https://github.com/aaronksaunders/simple_firebase_auth)

aaron Saunders—Clearly Innovative Inc .首席执行官、#企业家、#生态系统构建者#TheFutureIsWrittenInCode