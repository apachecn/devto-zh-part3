# 反应钩子-效果

> 原文：<https://dev.to/gugadev/react-hooks---effect-4503>

在前面的[文章](https://dev.to/gugadev/react-hooks---state-5g64)中，我们看到了*挂接* `useState`的例子，它为我们提供了一种访问组件*无状态*中的状态的方法。那么，为我们提供试剂的另一个*钩*就是`effect`。

### 使用效果

`useEffect`挂接允许我们“订阅”该组件的生命周期。你可以认为这个钩子是`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`的组合。

让我们看一个例子来更好地了解它。

[https://codepen.io/gugadev/embed/zMjKaY?height=600&default-tab=js&embed-version=2](https://codepen.io/gugadev/embed/zMjKaY?height=600&default-tab=js&embed-version=2)

在此范例中，我们使用挂接`useState`来更新输入的值，以及`useEffect`在每次重新彩现元件时执行某些动作。

每次使用在文本框中输入的新值更新状态时，都会重新渲染组件，并执行传递到“`useEffect`”的回调。你可以把`useEffect`看作是让他们从你的组成部分投降的监听器；正因为如此，据说是*状态化*元件生命周期方法的组合。

最后，每次运行*挂接*时，我们都会通过`ref`查看它是否包含有效的电子邮件，并分别显示或隐藏错误消息。请注意，我们不会在`setEffect`内再次更新状态，因为在这种情况下，我们将进入无限循环。

* * *

而这就是全部，在接下来的帖子中，我们将做一个小的应用程序来把学到的所有关于‘t0’胡克斯的知识付诸实践。