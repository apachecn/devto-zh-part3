# 如何在 iOS 中使用 MainController

> 原文：<https://dev.to/onmyway133/how-to-use-maincontroller-in-ios-5fh4>

通常在一个应用程序中，我们有这些流程:onboarding，login，main。并且我们通常根据状态分别设置`OnboardingController`、`LoginController`和`MainController`为根视图控制器。

我发现将`MainController`作为主流程的容器非常有用。它可以是选项卡控制器、滑动菜单控制器或只包含一个子视图控制器。屏幕由子视图控制器提供，但是`MainController`做以下工作

*   状态栏样式

我们通常需要在父控制器上调用`preferredStatusBarStyle`。参见[https://stack overflow . com/questions/19022210/preferredstatusbarstyle-not-called](https://stackoverflow.com/questions/19022210/preferredstatusbarstyle-isnt-called)

*   App 确实变得活跃了

通常当应用程序被带到前台时，我们需要获取登录的用户配置文件来查看是否有变化。我们通过听`MainController`中的`app did become active`来做到这一点。

*   模拟打开

这可能是反模式的。但是在 UI 测试中，出于惰性，我们可以只使用一些[启动参数](http://nshipster.com/launch-arguments-and-environment-variables/)并检查呈现一些特定的屏幕来测试，因为`MainController`是主流程的根。

*   注销

因为事物起源于`MainController`，事物可以终止于`MainController`。我们可以处理`logout`，清除状态，并告诉`MainController`告诉`AppDelegate`切换到另一个根控制器

原帖[https://github.com/onmyway133/blog/issues/36](https://github.com/onmyway133/blog/issues/36)