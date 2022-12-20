# 在颤动中创造环境

> 原文：<https://dev.to/fallenstedt/creating-environments-in-flutter-36h>

[![environments](img/1b3f79ad04f60dc1ca1a8e841634a3d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m3JAmIr6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bwb6frvp74l2zrhwj8zj.jpg)

测试一个应用程序是否正确地将所有配置从代码中分离出来的试金石是，代码库是否可以在任何时候开源，而不损害任何凭证。

我开始玩 Flutter，我惊讶地发现没有太多的资源来创建[配置](https://12factor.net/config)，所有可能在部署之间变化的东西(登台、生产、开发者环境等)。这里有一个我发现非常有用的方法。

你总是可以跳过去看一个真实的[颤动的例子](https://github.com/Fallenstedt/coffee-bot)，或者跟随 dart pad。

这是我们的基本应用程序。

```
class App {
  String final someConfigMember = 'secret';
  String final someOtherConfigMember = 'some other secret';

  App() {}

  runApp() {
    print("Hello World. I am running");
    print(this.someConfigMember);
    print(this.someOtherConfigMember);
  }
}

void main() {
  new App().runApp();
} 
```

我们的 main 应该始终启动一个本地开发环境。让我们改变这个函数的内容。

```
//snip

void main() {
  Development();
} 
```

这是一个假想的开发环境。让我们创建这个开发环境作为一个新的 Dart 类。我们的应用程序还应该通过一个参数来了解开发环境。

```
 class App {
  final Development env;

  App(this.env) {}

  runApp() {
    print("Hello World. I am running on $env");
    print(this.env.someConfigMember);
    print(this.env.someOtherConfigMember);
  }
}

class Development {
  static Development d; 
  String someConfigMember = 'sup';
  String someOtherConfigMember = 'some private thing';

  Development() {
    // obtain snapshot of this.
    d = this;

    //shove it into our app.
    // call 'runApp'. 
    new App(d).runApp();
  }
}

void main() {
  Development();
} 
```

现在我们需要一个测试环境，所以我们不在生产环境中进行测试。Dart 允许我们继承(扩展一个类)。我们的开发、测试和生产环境应该从一个基本的 Env 类扩展而来。我们可以通过创建一个类作为许多环境的真实来源来使自己免于头痛。您的工作现在应该看起来像这样。

```
class Env {
  static Env d;
  String someConfigMember;
  String someOtherConfigMember;

  Env() {
    // obtain snapshot of this.
    d = this;

    //shove it into our app.
    new App(d).runApp();
  }
}

class App {
  final Env env;

  App(this.env) {}

  runApp() {
    print("Hello World. I am running on $env");
    print(this.env.someConfigMember);
    print(this.env.someOtherConfigMember);
  }
}

class Development extends Env {
  String someConfigMember = 'sup';
  String someOtherConfigMember = 'some private thing';
}

void main() => Development(); 
```

仅此而已。您可以根据需要创建任意多的环境，并告诉 flutter 在构建时运行特定的文件。当你决定在 flutter 中使用它时，你需要让你的应用程序在你的 Env 类中以`runApp(App(this))`开始。[你可以在这里看到一个例子。](https://github.com/Fallenstedt/coffee-bot/blob/master/lib/env.dart#L8-L11)

玩得开心