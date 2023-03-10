# 用 Koin 重新思考 Android 依赖管理

> 原文：<https://dev.to/filbabic/rethinking-android-dependency-management-with-koin-dd4>

每一个严肃的项目都应该专注地考虑它的结构、架构，以及项目特性和模块中的依赖关系的管理。在 Android 的世界里，Dagger 绝对是每个开发人员为此使用的最流行的框架和工具。但是 Dagger 虽然有很多优点，但也有很多缺点，有时会使工作变得更加复杂和困难，而且它还会引入一堆样板代码。

不仅如此，Dagger 设置往往需要很多精细的知识。文档本身长达数英里，除非你有丰富的经验，否则当 Gradle build 错误弹出时，你可能会发现自己有麻烦，它并没有告诉你太多。此外，由于 Dagger 的工作原理是注释处理和代码生成，所以项目构建明显要慢一些。

因此，开发人员一直在寻找一种新的工具来帮助他们管理项目中的依赖关系。随着 Kotlin 的出现，以及大量新的有用的语言特性，出现了像 Koin 这样的框架。

## 我们的救世主，Koin！

Koin 是一个基于 Kotlin 的依赖管理框架，它严重依赖于它的语言特性，如扩展函数、委托、lambdas 和泛型。它寻求为您提供与匕首一样的工具集，但不需要学习、设置或维护它的所有高级要求。此外，它极大地减少了建立依赖图所需的代码量，同时保持了可读性和简单性作为其主要特征。

Koin 和 Dagger 之间的一个关键区别是 Koin 在应用程序的运行时范围内工作。这意味着它不生成任何代码，首先，它不需要做任何注释处理，或者遍历整个项目文件，与 Dagger 相比，这反过来大大减少了构建时间。

要将 Koin 添加到您的项目中，您必须将这些依赖项添加到您的 gradle 文件中:

```
compile “org.koin:koin-core:$version”
compile "org.koin:koin-android:$version"
compile "org.koin:koin-android-scope:$version"
compile "org.koin:koin-android-viewmodel:$version" //Architecture components 
```

之后，您就可以开始构建您的 DI 了！一个示例模块定义应该是这样的:

```
val userModule = module {     
     single { get().create(UserApiService::class.java) }      
     single { UserRepositoryImpl(userApiService = get()) as UserRepository }      
     factory { UserPresenter(userRepository = get()) as UserContract.Presenter } } 
```

为了创建一个模块，使用我们的提供者函数，我们所要做的就是从 Koin 调用模块全局函数。在 lambda 块中，我们可以用各种方式定义任意数量的提供者。例如，如果我们声明我们的 ApiService 是在单个函数中构建的，那么它将被创建为一个懒惰的单例对象，这样我们就可以在不使用依赖项的情况下进行内存优化(因此也可以进行懒惰分配)，而且还可以在必要时进行重用。

另一方面，如果我们在一个标准的提供者——工厂函数中声明 Presenter，那么每次我们想要接收一个实例时，我们都会得到一个全新的实例。跟正规工厂一样。您可能还注意到了 get 函数调用。任何时候你需要一个依赖项，你所要做的就是调用 get()，Koin，通过使用具体化的泛型参数，将在图中找到它的声明。通过使用 get，您可以预先接收依赖项，并在注入之前设置它，如果您需要某种初始化的话。

### 注入依赖关系

要在代码中接收值，即注入依赖项，您只需做以下事情:

私有 val userPresenter: UserContract。Presenter by inject()
您只需用您需要注入的类型声明字段，并让它由委托注入。科特林就是牛逼，不是吗！

为了解释这一切是如何工作的，您必须想象在应用程序中存储提供者函数的最简单的方式——地图。Koin 有效地接收所有的 ModuleDefinition 函数，并查看它们返回的类型。完成后，它将所有的提供者存储在一个映射中，其中每种类型都是一个映射键，它返回您定义的提供者函数。

整个 API 和内部实现比这稍微复杂一点，但这是它的要点。因此，当您在应用程序中的某个地方调用 get 函数时，它会查找您尝试接收的类型，如果该类型已定义，它会使用 provider 函数返回实例。如果没有，Koin 抛出一个异常，说找不到定义。

因为 Koin 在运行时工作，所以它不能像 Dagger 对其注释那样进行静态分析，以查看是否所有需要/使用的定义都可用。正因为如此，它只能在你启动应用程序时进行检查，最终在出错时抛出异常。

我们相当确定你已经在五分钟内学会了 Koin DSL。Koin 本质上非常简单，不需要花太多时间学习或编写，这很酷，但这不是它最大的好处。Koin 最有用的一点是，你可以设置复杂的机制，而不必过多考虑实现本身。其中一些特性是提供者函数中的作用域机制和动态参数。

让我们开始实现这些核心特性吧！:]

## Android 中的作用域

如果您要在 Dagger 中构建作用域，您必须提供特定的注释来指定每个组件所附加的作用域。在那之后，你将不得不创建和销毁组件，在某些 Android 生命周期方法中，在图中清除或填充依赖关系。

当使用 Koin 时，您必须做的第一件事是声明一个依赖项是使用 scopemodule 定义函数构建的:

```
scope("USER_SCOPE") { 
     UserPresenter(userRepository = get()) as UserContract.Presenter }
Once you’ve done that, Koin knows that it needs to differentiate this provider from the others. When using the provided dependency, as long as your Android component (e.g. Fragment) is alive, the Presenter will be reused. Next, you have to initialize that scope within the Android component, by calling the `bindScope` function, and passing in the scope you want to bind:

override fun onCreate(savedInstanceState: Bundle?) {     
     super.onCreate(savedInstanceState)    
     bindScope(getOrCreateScope("USER_SCOPE"))
} 
```

你要做的最后一件事…嗯，没有最后一件事，差不多就是这样！:]

你不必担心 Android 组件、模块或作用域本身。一旦你把这两部分连接起来，它们就会自己照顾自己。Android 组件被销毁后——`onDestroy`被调用，模块被清理，依赖关系被释放。您可以在这种行为的基础上，创建自己的作用域保持器，即使在配置发生变化的情况下，它也会保持不变，从而有效地优化您在这些情况下重建依赖项和片段的方式。

所有这些都行得通，因为 Koin 采用了 Android 的`LifecycleOwner`和`LifecycleObserver` API。它监听某些事件——比如使用观察者界面的`onDestroy`或`onCreate`,因此可以在适当的时候对变化做出反应。

## 动态运行时参数

当您请求某个依赖项时，在运行时动态地发送参数也相当简单。在 Dagger 中，您必须将它存储在某种工厂中，将依赖关系传递给您正在构建的组件，或者您必须自己构建的类似模式。

在 Koin 中，您不必重新创建模块或提供程序。知道它们在运行时工作，您可以简单地将一个参数列表传递给从中检索依赖项的函数，就像您之前看到的 inject 函数一样。假设我们的一个依赖项，比如 Presenter，需要一个 userId: String 参数来正确设置。您可以通过以下方式将该用户 Id 作为参数传递:

```
private val userPresenter: UserContract.Presenter
     by inject(parameters = { parametersOf("dj80a2k") }) 
```

一旦你发送了一个参数，你就可以在提供者中得到它，作为 lambda 参数。ParameterList 是一个以变量 arguments 作为其唯一属性的类，因此，您可以发送任意数量的参数，最终按顺序检索它们:

```
scope("USER_SCOPE") {
     val userId: String = parameters [0]  
     UserPresenter(userRepository = get(), userId = userId) as UserContract.Presenter 
} 
```

这使得创建和提供需要一些 Android 组件或依赖项的依赖项变得非常简单，如 Context、FragmentManager 等。

## 总结一下

Koin 提供并允许我们做的事情太多了。这是一种很好用的东西，可以做你需要的任何事情，而且似乎没有任何缺点。这听起来好得令人难以置信。但事实并非如此。

Koin 是以一种非常简洁的方式构建的，背后的代码被简化到了极致，依赖于久经考验的概念和功能。Koin 的开发者也有很多想法，他们希望在未来实现，改进或优化，这无疑是你应该尝试使用的东西，如果你想摆脱匕首的困难。

### P.S

感谢您阅读我的帖子！有什么问题可以随便问，和我一起讨论 Koin！:]另外，如果你正在寻找一个 Android 的机会， [Five 正在招聘](https://five.talentlyft.com/jobs/senior-android-developer-mf-chu?utm_source=devto&utm_medium=banner&utm_campaign=fdb-e06-blogpost))！你可以加入我们出色的 Android 团队，亲自参与这些创意活动。 <sup>^</sup>