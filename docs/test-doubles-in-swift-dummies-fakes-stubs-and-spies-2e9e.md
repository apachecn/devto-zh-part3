# 迅捷中的测试替身:假人、假货、树桩和间谍。

> 原文：<https://dev.to/mokagio/test-doubles-in-swift-dummies-fakes-stubs-and-spies-2e9e>

*这是我的博客[mokacoding.com](https://www.mokacoding.com/blog/swift-test-doubles/)T3】的交叉帖子*

爱斯基摩人有很多表示雪的词语。 *Aqilokoq* 是轻柔飘落的雪 *piegnartoq* 是适合驾驶雪橇的雪。

语言进化它们的词汇来满足使用者的需求。能够用一个词来区分新鲜柔软的雪和适合驾驶雪橇的雪，可以让你更有效地与你的爱斯基摩朋友交流。

用词语来称呼事物和概念为我们理解它们提供了基础。它允许我们在更深更具体的层次上思考它们。

当编写测试时，我们经常不得不达到*测试加倍*。这些是我们的测试系统输入的副本，以这种方式编写，以便更容易验证某些行为。像爱斯基摩人一样，我们软件开发人员需要丰富的词汇来区分基于他们做什么的测试替身，以便更好地讨论他们。

我喜欢参考马丁·福勒和 T2 的测试双打目录。以下是这些概念在 Swift 中的样子。

## 哑弹

假人是指**不做任何事情**的测试替身。

当它不影响你正在测试的行为时，你可以用它作为被测系统的输入参数的占位符。

```
struct DummyPizzaGetter: PizzaGetter {

    func getPizza(_ completion: (Result<[Pizza], PizzaAPIError>) -> Void) {
      // does nothing
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 假的

假的是测试 double **返回相同的值或者一直执行相同的行为**。

无论结果如何，当被测系统的行为以您的组件所做的事情为先决条件时，您可能希望使用这种方法。

例如，当编写集成测试时，您可能希望提供存储层的不同实现，比如说内存中的实现，而不是写入磁盘的实现。您的集成测试依赖于存储正常且一致的运行，但您永远不需要将 it 作为测试的一部分。

## 存根

stub 是一个测试 double，您可以使用它来**控制提供给被测系统**的输入。

当你测试的行为依赖于一个输入时，你应该在测试中为这个输入使用一个存根。

存根的一个常见用例是允许根据操作的成功或失败来测试对象的行为。

我最喜欢的存根用例是测试使用服务的对象的行为，这些对象发出的请求可能成功也可能失败。我们可以编写一个存根控件，在其中控制请求是成功还是失败，这允许我们在两种场景中测试组件的行为。

假设我们有一个`PizzaPresenter`负责为视图控制器提供逻辑，从服务器获取比萨饼，并将它们转换成可以显示的对象。我们可以使用存根测试请求成功或失败时它的行为。

```
func testSuccessfulLoad() {
    let presenter = PizzaPresenter(
        pizzaGetter: PizzaGetterStub(pizzas: [.margherita, .pepperoni])
    )

    waitUntil { done in
        presenter.load { result in
            switch result {
            case .success(let displayablePizzas):
                // expectations on the received displayable pizzas
            case .failure(let error):
                fail("Expected to succeed, failed with \(error)")
            }
        }
    }
}

func testFailedLoad() {
    let presenter = PizzaPresenter(
        pizzaGetter: PizzaGetterStub(error: .offline)
    )

    waitUntil { done in
        presenter.load { result in
            switch result {
            case .success(let displayablePizzas):
                fail("Expected to fail, succeeded with \(dispayablePizzas)")
            case .failure(let error):
                // expectation on the received error
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

存根看起来是这样的:

```
class PizzaGetterStub: PizzaGetter {

    private let result: Result<[Pizza], PizzaAPIError>

    init(pizzas: [Pizza]) {
        self.result = .success(pizzas)
    }

    init(error: PizzaAPIError) {
        self.result = .failure(error)
    }

    func getPizzas(_ completion: (Result<[Pizza], PizzaAPIError>) -> Void) {
        completion(result)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 间谍

spy 是一个测试替身，您可以用它来**检查被测系统**产生的输出。

间谍是树桩的反义词。当被测系统对一个依赖项产生副作用时，你可以使用一个间谍来记录这个效果，然后验证它是否符合预期的行为。

例如，现在每个人都在他们的 iOS 应用程序中实现黑暗模式。假设您想要测试`SettingsController`，您的设置视图控制器使用这个组件来传递用户与 UI 的交互。

```
class SettingsController {

    // ...

    func set(darkModeEnabled enabled: Bool) {
        if enabled {
            layoutManager.paintDarkMode()
        } else {
            layoutManager.paintLightMode()
        }

        settingsStorage.set(darkModeEnabled: enabled)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您想要测试的行为是`SettingsController`正确存储黑暗模式偏好。您可以为存储层提供一个 spy，并确保将正确的值放在那里。

```
class SettingsStorageSpy: SettingsStorage {

    private(set) var darkModeEnabled: Bool?

    func set(darkModeEnabled enabled: Bool) {
        self.darkModeEnabled = enabled
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意`darkModeEnabled`是一个`Optional` `Bool`。这将允许您测试该值是否已设置。如果您的测试将为`darkModeEnabled`读取一个`.none`，您将知道您的代码没有调用`set(darkModeEnabled:)`。

这是如何使用间谍:

```
func testSettingDarkMode() {
    let spy = SettingsStorageSpy()
    let controller = SettingsController(
        layoutManager: LayoutManagerDummy(),
        settingsStorage: spy
    )

    controller.set(darkModeEnabled: true)

    expect(spy.darkModeEnabled) == true
} 
```

Enter fullscreen mode Exit fullscreen mode

注意我们是如何为输入参数`LayoutManager`传递一个哑元的。`SettingsController`需要一个`LayoutManager`值来初始化，但是我们的测试并不关心它如何与之交互，所以我们可以传递一个哑元。

## 那 mocks 呢？

如果你正在阅读单元测试，迟早你会遇到“模拟”这个词。在测试词汇中，Mock 是一个含义丰富的词。

Martin Fowler [将 mock](https://martinfowler.com/bliki/TestDouble.html)称为“预先编程好的期望的替身，这些期望形成了它们被期望接收的呼叫的规范”。如果他们收到一个他们不期望的调用，他们可以抛出一个异常，并在验证过程中进行检查，以确保他们得到了他们期望的所有调用。”

如果你正在开发一种动态语言，很可能会有一个模仿框架。Objective-C 有 [OCMock](http://ocmock.org/) ，JavaScript 有 [Mocha](https://mochajs.org/) 和 [Sinon](https://sinonjs.org/) ，Ruby 有 [rspec-mock](https://github.com/rspec/rspec-mocks) ，不一而足。所有这些框架都是强大而灵活的，它们不仅提供了构建模拟的方法，还提供了构建存根、假货和间谍的方法。

我会**劝阻你使用模拟**，这就是为什么我不会为他们提供代码示例。

通过模仿，你可以测试你的对象是否调用了被模仿对象的某些方法。这种测试侧重于实现而不是行为。在添加某些组件时，或者在处理大量尚不能重构的遗留代码时，这可能很方便，但从长远来看，这将使您的测试变得僵化。每次你改变方法签名时，你都必须更新所有的模拟用法，更不用说如果你移除方法，你必须重写测试。

我希望你不要认为我是一个势利小人，用仿制品在人们的工作上扔大便。它们是很好的工具，我只是认为在有限的情况下它们是最好的工具。

## 备忘单

总结一下:

*   当你需要一个占位符时，使用一个**假人**。
*   当你需要它为测试运行提供的行为时，使用一个**假的**，但是它不影响你正在测试的特定行为。
*   使用一个**存根**来控制被测系统的输入，这样你就可以测试行为是如何根据它而改变的。
*   使用一个**间谍**来记录被测系统产生的输出或效果，这样你就可以验证它的行为是否符合你的预期。
*   尽量不要使用模拟。

能够清楚地说出事物的名称给了我们更有效地谈论它们的力量。为您的测试 doubles 使用合适的名称将使您的代码读者更容易理解测试的内容。

* * *

👉📬如果你喜欢阅读此文[,请点击这里](http://eepurl.com/dEr0DH),在你的收件箱里收到类似这样的新帖子。