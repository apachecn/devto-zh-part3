# 使用 React 上的错误边界保护应用程序的完整性

> 原文：<https://dev.to/duranenmanuel/protect-the-integrity-of-your-applications-with-error-boundaries-on-react-814>

*最初发表于[EnmaScript.com](https://enmascript.com/articles/2018/07/19/protect-the-integrity-of-your-applications-with-error-boundaries)，为了更好的阅读体验去那里吧！*

自 16.0.0 版本*以来，错误边界*一直是 React 核心的一部分，但没有足够多的人关注它。我认为这个功能非常强大和有用，如果你不知道为什么，你可能没有正确使用它，如果你对它真正的功能没有信心，或者你想知道为什么我认为它很棒，那么继续阅读。

首先是定义，**错误边界是一个组件**，它允许我们捕捉发生在它所包装的组件内部的错误，这允许我们在错误发生时做“一些事情”，它的工作方式非常类似于经典的 javascript `try { ... } catch() { ... }`(是的，与您在解析 JSON 时用来捕捉错误的方式相同)，如果使用正确，这个特性可以为您的站点增加很多稳定性，并为您的访问者带来很大的 UX。

基本上，当一个组件实现了生命周期方法`componentDidCatch`时，它就变成了一个错误边界，实现的例子可以是:

```
class ErrorBoundary extends React.Component {
    constructor(props) {
        super(props);
        this.state = { error: null, errorMsg: null };
    }

    componentDidCatch(error, info) {
        this.setState({
            error: error,
            errorMsg: info
        })
    }

    render() {
        if (this.state.errorMsg) {
            return (
                <div>
                    An error ocurred but you can click other boxes!
                </div>
            );
        }

        return this.props.children;
    }
} 
```

要使用 ErrorBoundary 组件，您可以这样做:

```
<ErrorBoundary>
    <ChildComponent />
    <ChildComponentTwo />
</ErrorBoundary> 
```

上面的代码所做的是验证`ErrorBoundary`组件不包含已定义的`this.state.errorMsg`,如果包含，则意味着由错误边界包装的组件之一失败，应该执行回退行为。

假设你有一个*单页应用*，这个应用由不同的组件组成**并不是所有的组件都互连** *。*在这个应用中，如果我们在其中一个组件由于意外的 javascript 错误或类似原因而失败时不使用错误边界，这将导致整个应用崩溃，几毫秒后，我们将看到经典的白色页面，表明您漂亮的应用刚刚遭受了心脏病发作

现在，如果您使用错误边界战略性地包装您的组件，并且其中一个包装的组件出现故障，这不会扩展和破坏整个应用程序，相反，错误将会上升，直到它到达其父错误边界组件，此时我们将能够显示个性化的 UI，显示替代组件或更好的东西…发送错误数据进行跟踪，嘣！

最后，让我们用一个用例场景(耶！):

### 举例

[我已经创建了一个 CodePen](https://codepen.io/enmanuelduran/pen/aKgMZx/) ，在这个例子中我创建了 4 个 box 组件，其中 2 个是“坏的”(红色)盒子，当点击它们时会故意抛出一个错误，这些组件用`<ErrorBoundary>`组件包装，其中 1 个是“好的”(蓝色)盒子，当点击它时会简单地向控制台记录一条消息，最后一个盒子是“最终死亡盒子”(黑色)，会导致整个应用程序崩溃，因为它没有被错误边界包装。

哇哦，太棒了吧？是的，我知道……但是你还需要知道一些其他的事情，并不是所有的错误都被错误边界所捕获，这里有一些例外:

*   事件处理程序
*   异步代码
*   服务器端渲染
*   在错误边界本身中抛出的错误(请保持错误边界的逻辑安全和一致)

好了，伙计们，这就是这篇文章，希望你们喜欢这个话题，就像我喜欢写它一样，如果你喜欢，请分享它，如果你想分享你的想法，你可以在下面的评论区，下次见！