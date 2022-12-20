# 角度组件订阅与异步管道:尽可能使用管道

> 原文：<https://dev.to/eyassh/angular-component-subscription-vs-asyncpipe-use-pipes-when-possible-e9c>

我通常在工作中审查大量的角度代码。我通常鼓励在 Angular 组件中使用普通的`Observable` s，并使用模板 html 中的 [`AsyncPipe`](https://angular.io/api/common/AsyncPipe) ( `foo | async`)来处理订阅，而不是直接订阅组件 TS 文件中的可观察对象。

# 订阅组件

除非您知道您在组件中启动的订阅是非常有限的(例如，没有重试逻辑的 HTTP 请求等)，否则您在组件中进行的订阅必须:

1.  当退出组件时被关闭、停止或取消(例如，当导航离开页面时)，
2.  仅当组件实际加载/可见时才打开(订阅)(即在 ngOnInit 中而不是在构造函数中)。

# `AsyncPipe`可以替你照顾那

不需要手动实现组件生命周期挂钩，记住订阅和取消订阅一个可观察对象，`AsyncPipe`可以帮你做到。

在这里可以看到代码示例和完整的帖子。