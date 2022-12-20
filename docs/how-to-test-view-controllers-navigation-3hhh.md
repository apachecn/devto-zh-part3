# 如何测试视图控制器导航

> 原文：<https://dev.to/mokagio/how-to-test-view-controllers-navigation-3hhh>

这里有一个在测试 iOS 应用时经常出现的问题:“当*发生*事情时，我如何测试*一个视图控制器*呈现*另一个视图控制器*”

测试视图控制器之间的导航可能很棘手。
如果你想测试推送导航，你需要将你的视图控制器包装在一个`UINavigationController`中，或者如果是模态展示，你需要将它添加到一个`UIWindow`中。通常会涉及到动画，这反过来要求要么你的测试断言是异步的，要么你的代码允许从测试中注入`animated`标志作为`false`。

这些和其他一些小问题使得测试更难编写和推理，但事实并非如此。

您可以使用一个简单而廉价的模式来轻松测试您的视图控制器如何触发导航，使您的代码在这个过程中更容易使用。

## `NavigationDelegate`

使视图控制器之间的导航易于测试的关键在于认识到**视图控制器负责呈现其他视图控制器**。

视图控制器应该只触发导航，然后*将执行导航的动作委托给*给另一个对象。
我喜欢称这类代表`NavigationDelegate` s [<sup id="pattern-origin">*</sup>](#fn1) 。

有了`NavigationDelegate`之后，测试视图控制器如何导航现在就是测试它如何与其委托交互的问题了。这可以通过使用一个[间谍](https://www.mokacoding.com/blog/swift-test-doubles/#spy)来完成。

### `NavigationDelegate`在行动

为了测试视图控制器是否触发了预期的导航，您可以验证它是否调用了其`NavigationDelegate`的适当方法。

```
func testCallsShowRedWhenRedButtonTouched() {
    let viewController = loadViewController()
    let navigationDelegateSpy = ViewControllerNavigationDelegateSpy()
    viewController.navigationDelegate = navigationDelegateSpy

    viewController.redButton.sendActions(for: .touchUpInside)

    XCTAssertTrue(navigationDelegateSpy.showRedCalled)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
protocol ViewControllerNavigationDelegate: class {
    func showRedViewController()
}

class ViewController: UIViewController {

    @IBOutlet weak var redButton: UIButton!

    weak var navigationDelegate: ViewControllerNavigationDelegate?

    override func viewDidLoad() {
        super.viewDidLoad()

        redButton.addTarget(self, action: #selector(redButtonTouched), for: .primaryActionTriggered)
    }

    @objc func redButtonTouched() {
        navigationDelegate?.showRedViewController()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
class ViewControllerNavigationDelegateSpy: ViewControllerNavigationDelegate {

    private(set) var showRedCalled = false

    func showRedViewController() {
        showRedCalled = true
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*本代码全部可在[本 GitHub 回购](https://github.com/mokagio/Navigation-Delegate-Example)T3】中获得。*

上面的测试看起来很简单，不是吗？
通过使用这种模式，您不再需要设置包含被测视图控制器的整个导航栈来验证它的行为。
你也不需要把视图控制器放在`UIWindow`或者`UINavigationController`里面，简化了测试设置和拆卸。

唯一的缺点是你必须为`NavigationDelegate`造一个间谍测试替身。鉴于这涉及的代码如此之少，我认为这是一个很好的权衡。

如果你想测试符合`NavigationDelegate`的类型如何执行导航——这是一个明智的做法——你可以独立完成。

```
func testPushesRedViewController() {
    let navigator = Navigator(navigationController: UINavigationController(rootViewController: UIViewController()))

    navigator.showRedViewController()

    _ = expectation(
        for: NSPredicate { input, _ in
            return (input as? UINavigationController)?.topViewController is RedViewController
        },
        evaluatedWith: navigator.navigationController,
        handler: .none
    )
    waitForEnavigatorpectations(timeout: 1, handler: .none)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 今天你可以采用`NavigationDelegate` s

我喜欢这个想法的原因是它既便宜又便携。你不必改变你的应用架构来采用它。
事实上，它非常适合大多数架构。

你可以从今天开始使用`NavigationDelegate` s。

为了开始并使您的代码可测试，您甚至可以让视图控制器成为它自己的导航委托。从长远来看，这肯定不是我赞同的事情，但它可能会帮助你开始。
一旦委托在代码库中，您可以稍后再回到它并在专用对象中提取实现。

## 好处

以这种方式使视图控制器的导航端更容易测试还有其他好处。

因为您的视图控制器现在不知道如何呈现新的视图控制器，所以当更新导航工作方式时，它们不需要改变(参见示例项目中的[PR](https://github.com/mokagio/Navigation-Delegate-Example/pull/1))。

还记得[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)的定义吗？一个对象应该只有一个改变的理由。
视图控制器是 iOS 应用的胶水代码；他们最终做了很多事情。有了这个，他们又少了一件事要做。😉

单一责任也是为什么我喜欢使用一个专用的仅用于导航的委托，即使视图控制器已经有一个委托。是的，你最终会有更多的定义和对象，但是我宁愿在一个有很多小的、容易推理的代码库中工作，而不是一个有几个类做所有工作的代码库。

* * *

如果你知道正确的方法，测试`UIViewController`如何触发导航就像验证任何其他副作用代码一样简单。

`NavigationDelegate`模式遵循*T2 法则；分而治之。
将导航的实现分成两部分，启动它的代码和执行它的代码。
单独测试两者比一起测试更容易。*

使用`NavigationDelegate` s 不仅会使你的应用程序的关键区域更容易测试，比如屏幕是如何链接在一起的，而且还会使在应用程序的不同区域重用视图控制器更容易。
一个恰当的例子说明了如何使测试变得容易也使它们变得容易使用。

该模式也可以很好地扩展。
您可以想象一个视图控制器基于某种内部状态触发不同屏幕的呈现。
例如，如果用户没有订阅，一个按钮可以显示一个屏幕向用户追加销售订阅，或者如果用户有订阅，可以推送到功能屏幕。
这两个不同的流程由`NavigationDelegate`中的两种不同方法触发。
在测试中，您可以在视图控制器中设置状态，合成应该触发表示的用户动作，并通过 spy 验证调用了正确的导航方法。

你认为这种方法怎么样？如果你使用它，我很想听听你的想法，以及你对它的看法。
在 Twitter 上通过 [@mokagio](https://twitter.com/mokagio) 联系或者在下面留言。

这是我的博客[mokacoding.com](https://www.mokacoding.com/blog/navigation-delegate-pattern/)的一篇交叉文章。

### 相关岗位

[![mokagio image](img/2732dd67ac527aa93174267b602adff5.png)](/mokagio) [## 如何在 Swift 中进行 TDD，分步指南

### 吉奥罗提 2 月 13 日 1914 分钟阅读

#tdd #swift #testing](/mokagio/how-to-tdd-in-swift-a-step-by-step-guide-8jg)[![mokagio image](img/2732dd67ac527aa93174267b602adff5.png)](/mokagio) [## 迅捷中的测试替身:假人、假货、树桩和间谍。

### 吉奥罗提 2 月 15 日 196 分钟阅读

#swift #testing #tdd](/mokagio/test-doubles-in-swift-dummies-fakes-stubs-and-spies-2e9e)[![mokagio image](img/2732dd67ac527aa93174267b602adff5.png)](/mokagio) [## 如何在 Xcode 中运行单个测试

### 吉奥罗提 1919 年 4 月 24 日 4 分钟阅读

#xcode #testing #productivity](/mokagio/how-to-run-a-single-test-in-xcode-5h72)

### 脚注

[*](#pattern-origin) 。委派从视图控制器执行导航的责任的想法并不新颖。这个概念使得像[协调器](http://khanlou.com/2015/10/coordinators-redux/)、[导航器](https://www.swiftbysundell.com/posts/navigation-in-swift)和[路由器](https://clean-swift.com/routing-in-clean-swift/)这样的模式成为可能。
我第一次看到这种形式化是在 Alberto De Bortoli 2014 年的帖子“iOS 上的[流量控制器，以实现更好的导航控制](https://albertodebortoli.com/2014/09/03/flow-controllers-on-ios-for-a-better-navigation-control/)”中。
尽管他在视图控制器中注入了负责导航的对象，但结果几乎是一样的。