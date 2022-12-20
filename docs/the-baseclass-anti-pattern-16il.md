# 基类反模式

> 原文：<https://dev.to/antonholmberg/the-baseclass-anti-pattern-16il>

作为一名顾问，我看到了很多代码库，到目前为止，在 Android 上，我发现的最常见的反模式是我称之为基类的反模式。

在这篇文章中，我将解释它是什么，为什么它不好，为什么它如此诱人。

### 什么事？

那么什么是基类反模式，我如何检测它？

1.  首先打开 android 项目。
2.  定位一个活动或片段类。
3.  它扩展了叫做 *BaseActivity* 或者 *BaseFragment* 的东西吗？

如果第三个问题的答案是肯定的，那么我的朋友你已经成为了基类反模式的受害者。如果你打开了 *BaseActivity* 或者 *BaseFragment* 类，并且这个类包含了超过 50 行的代码，那么你可能会有大麻烦。

### 那么为什么这样不好呢？

我就通过提一个问题来回答为什么不好。*基础活动*的职责是什么？除了是一个*活动*之外，这个班级名称还告诉了你什么？
**没什么**没错...

所以命名不好，让我们给它一个更好的名字！如果你打开这个类
，你可能会发现类似这样的东西:

```
class BaseActivity extends AppCompatActivity {
  @Inject
  Analytics analytics;

  @Override
  public void onCreate() {
    AppInjector.inject();
    super.onCreate();
  }

  @Override
  public void onStart() {
    super.onStart();
    analytics.trackScreen(this);
  }
} 
```

现在试着给这个类起个名字，它负责什么？
*ActivityWithInjectionAndAnalytics*不是一个很好的名字，*“和”*部分表示该类有不止一个责任，因此违反了*单一责任原则*。所以人们求助于像 *BaseActivity* 这样的东西来称呼它，这就产生了一个非常*不明确的*抽象。扩展 *BaseActivity* 不会告诉您将什么代码引入到新创建的活动中。

所以现在你可能会对自己说“这看起来不太糟”，我承认不，两种方法都不是世界末日。然而，这通常不是故事的结尾。我发现这样一个*基类*的大小通常与时间成线性相关。它开始积累一些东西，比如创建一个*视图模型* / *演示者*，添加依赖于*上下文*的实用功能，有时甚至实现业务逻辑。

随着时间的推移，人们开始扩展 *BaseActivity* 几乎是一种仪式，而不是出于对 *BaseActivity* 提供的某些功能的需求。也许您只关心在 *BaseActivity* 中被覆盖的 150 个方法中的一个。一旦你到了那一步，你就有效地创建了一个对代码的*静态*依赖，你不必依赖它。你运行不需要运行的代码。也许额外的代码被执行的事实不会伤害你(现在)，但它仍然会执行，我绝对向你保证，总有一天，有人(也许是你)会修改 *BaseActivity* 中的一些代码，以满足一个完全不相关的类的需要，你首先编写的类将有一个 *RuntimeException* 。你或你的同事会被指责，
被解雇，孤独终老。

### 那么为什么要使用基类呢？

我只能猜测其他人为什么这么做，但是作为一个在过去创建了大量基类的人，我可以说，对我来说，这主要是由于两件事。

*   这是编写枯燥(不要重复)代码的最简单的方法。
*   Android 特定的类不太适合[装饰模式](https://en.wikipedia.org/wiki/Decorator_pattern)。

我可能有一些代码，比如注入依赖项，我想:“嘿，我在 *onCreate* 中注入了我所有的活动！这是重复的代码...让我们摆脱它！”。因此，我创建了我的初始 *BaseActivity* 来处理注入，并开始破解。后来我会找到一些其他的代码，我会在很多活动中运行，例如从 *dp* 转换到 *px* 。为此，我需要访问一个*上下文*。因为我在学校学到了 OOP 是关于 dat 继承的，所以我只是在基类*中添加了一个方法来完成这个转换。当然，我应该做的是在一个实用程序类中创建一个静态方法:* 

```
public final class Units {
  public static int dpToPx(Context context, float dp) {
    Resources resources = context.getResources();
    return (int) TypedValue.applyDimension(
        TypedValue.COMPLEX_UNIT_DIP,
        dp,
        resources.getDisplayMetrics()
    );
  }
} 
```

现在任何需要它的类都可以调用它。根本不需要继承任何*基类*，所以方法的调用者可以放心地睡觉，知道在 *onStart* 、 *onCreate* 或属于*活动*的任何其他回调方法中没有奇怪的东西运行。

如果你不能使用静态助手方法，那么创建一个类来处理你的用例，并在*活动*中实例化它。更好的是，用您选择的依赖注入器来注入它！

但是，如果我们需要运行依赖于回调方法的东西，比如订阅一些可观察的数据，然后再取消订阅，那该怎么办呢？好吧，在这种情况下，我仍然更喜欢用*基类*以*隐式*的方式实现它。幸运的是，今天我们有了更好的选择，有了[生命周期观察器](https://developer.android.com/topic/libraries/architecture/lifecycle)。

### 结论

不要为你所有的活动和片段创建一个基类！

*   它在你的代码和你可能根本不关心的代码之间创建了隐含的，但是非常强的耦合。
*   很难全面了解一个类在做什么。
*   它会变大，变成一团乱麻。
*   在*基类*中可能有很多代码，如果在它自己的类中的话，单元测试会非常容易，但是它在*基类*中的事实让你不愿意或者不能测试它。

这是一个很大的咆哮和意见，我想听听你对此事的看法！