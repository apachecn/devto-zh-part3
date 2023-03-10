# JavaScript 101 中的依赖注入

> 原文：<https://dev.to/azure/dependency-injection-in-javascript-101-2b1e>

在我的文章和演讲“现代 Web 开发的 3 D”中，我解释了我认为现代 JavaScript 框架成功的关键因素。

> 等等，你没看我的介绍？没事...当你只有不到一个小时的投资时间时，我相信你会通过观看[这里](https://www.recallact.com/presentation/three-ds-modern-web-development)而获得价值。

[![What/Why/How Dependency Injection?](img/c6a8d423d92c23585bd85780b3882761.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WpAfhdYe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lawjgqxeu4o1t32xb4qm.png)

依赖注入就是其中之一。我发现开发人员常常很难理解它是什么，它是如何工作的，以及为什么它是必要的。

我边做边学，希望一个简单的代码示例能有助于解释。首先，我编写了一个非常小的应用程序来组装和运行一辆汽车。依赖关系如下所示:

```
Car
|
|--Engine
|  |  
|  |--Pistons
|
|--Wheels 
```

将这些部分视为组件之间的依赖关系。你可以在这里看到代码并交互运行:[https://jsfiddle.net/jeremylikness/gzt6o1L5/](https://jsfiddle.net/jeremylikness/gzt6o1L5/)。