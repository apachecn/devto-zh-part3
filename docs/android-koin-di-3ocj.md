# Android - Koin DI

> 原文：<https://dev.to/brightdevs/android-koin-di-3ocj>

在 Android 注入参数中，管理组件的生命周期和创建各种范围是困难和关键的，但幸运的是，我们有 [DI(依赖注入)](https://en.wikipedia.org/wiki/Dependency_injection)技术来拯救。

[![](img/6103709bea8b8ea8dfc3430ac45a32b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1-bM-Vga--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/31rxuv7rin14a9xh7f5a.jpg)

现在最著名的 Android DI 工具是由 Google 维护的 Dagger 2。我们已经在许多项目中使用它。很好，但是最近另一个玩家出现了。来说说 Koin 吧！

现在用 Kotlin 编写 Android 应用程序越来越流行，因此新的库也出现了！Koin 是一个‘务实的轻量级依赖注入’[DSL(领域特定语言)](https://en.wikipedia.org/wiki/Domain-specific_language) Kotlin 框架。它也不是基于反射和代码生成。它的工作方式不同于 Dagger，它可以被描述为一个[服务定位器](https://en.wikipedia.org/wiki/Service_locator_pattern)。这被认为是一种反模式，但在我看来，这取决于它需要做的工作。有了这个工具，你可以轻松地创建模块，注入依赖关系和管理 Android 组件的生命周期或范围。由于不生成代码，当方法计数、构建速度和 APK 大小很重要时，这可能是有用的。

#### 核心特性:

*   创建模块，子模块由单例和工厂提供者组成
*   在代码中的任意位置创建托管的会话范围
*   创建全局命名属性
*   绑定到组件的生命周期
*   Android 架构组件支持

## 我们来看几行代码

为我们提供当前天气的类

```
 class WeatherProvider {
    fun getCurrentWeather(): String {
      return "It's sunny! As always in this beautiful city of Gdańsk!”
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

使用`WeatherProvider` :
的单个实例创建天气模块

```
 val weatherModule = {
    single { WeatherProvider() }
  } 
```

Enter fullscreen mode Exit fullscreen mode

初始化 Koin:

```
 startKoin(listOf(weatherModule)) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在任何我们想用的地方使用我们的`WeatherProvider`，通过这样注入它:

```
 val weatherProvider: WeatherProvider by inject() 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！单例已经可以使用了。

您还可以根据活动的生命周期绑定`WeatherProvider`,或者根据您的需要创建一个特殊的范围，随时打开和关闭。

欲了解更多信息，请访问官方[入门](https://insert-koin.io/docs/1.0/getting-started/introduction/)部分。

## 总结

Koin 有很好的 Kotlin 感觉，dsl 语法，没有注释，没有样板文件，并且非常容易实现。另一件重要的事情是-错误。Koin 为我们提供了清晰合理的错误日志。如果你在实现中犯了一个错误，定位和修复问题真的很容易。您甚至可以在初始化时将自己的记录器绑定到它。然而很少有人担心。由于要解决在哪里注射什么，这会给大型团队带来麻烦吗？随着依赖项数量的增加，性能会急剧下降吗？我想我们需要弄清楚！

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

软件工程师西蒙·米洛赫@光明发明
[电子邮件](//szymon.miloch@brightinventions.pl)