# 使用 hyperHTML-7 的简单应用程序，测试

> 原文：<https://dev.to/pinguxx/easy-apps-with-hyperhtml-7-5b9g>

第 7 部分由 Trevor Ganus 撰写，

[![pinguxx image](img/b3fd6923e5679952858c6b2c43518de0.png)](/pinguxx)

## [伊凡](/pinguxx)

[standard geek](/pinguxx)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)pinguxx](https://twitter.com/pinguxx)[![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)pinguxx](https://github.com/pinguxx)

[![paritho image](img/31ca3c53383636df8ffd826c26771850.png)](/paritho)

## [保罗·汤普森](/paritho)

[lover of dogs and javascript. and coffee. and writing.](/paritho)

[![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)帕里托](https://github.com/paritho)

1.  [介绍，焊线/绑定](https://dev.to/pinguxx/easy-apps-with-hyperhtml-1-31cc)
2.  [事件和组件](https://dev.to/pinguxx/easy-apps-with-hyperhtml-2-hbi)
3.  [关于组件和简单状态管理的 Moar】](https://dev.to/pinguxx/easy-apps-with-hyperhtml-3-1m3l)
4.  [电线类型和自定义定义(意图)](https://dev.to/pinguxx/easy-apps-with-hyperhtml-4-38k3)
5.  [用 hyper 定制元素](https://dev.to/pinguxx/easy-apps-with-hyperhtml-5-5hdl-temp-slug-7285698)
6.  [定制我的定制元素](https://dev.to/pinguxx/easy-apps-with-hyperhtml-6-h5b-temp-slug-4579110)
7.  **测试！**
8.  [异步加载、占位符和带超链接的提前输入](https://dev.to/pinguxx/easy-apps-with-hyperhtml-8-async-loading-5d96)
9.  [搬运路线](https://dev.to/pinguxx/easy-apps-with-hyperhtml-9-routing-59k0)
10.  [第三方库](https://dev.to/pinguxx/easy-apps-with-hyperhtml-10-3rd-party-libraries-2n7b)

[![](img/5d9401d9a3abd62cd78e0ef99d44f01c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KJln3FZk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qedmndmqtwvnww2mjr1f.jpeg)

我不太喜欢 100%的代码覆盖率，等等，等等。但是如果我们正在编写组件，它们可能需要尽可能多地被测试。我们不会深入到测试中，但是我们想展示一些你可以测试你的组件和元素的方法。

在我们开始之前，有一个小小的警告:这篇文章并不打算涵盖你需要知道的关于单元测试的一切。许多其他教程可以教你如何编写测试良好的代码的基础知识。我们的目标是向您展示如何使用特定的测试工具，前提是您对单元测试有一个基本的了解。说完了，让我们开始吧！

### 输入 Tressa

是的，YAAT——又一个安德里亚工具。Tressa 是一个非常简单的测试工具，我们将用它来测试我们的东西。它让我们能够进行同步测试和异步测试。它还带有一个基本的断言函数。Tressa 需要 consolemd，这是一个允许我们使用 markdown 在控制台上打印的模块，非常简洁。让我们看一个例子:

[https://stackblitz.com/edit/intermediate-testing-01?embed=1&&](https://stackblitz.com/edit/intermediate-testing-01?embed=1&&)

> **注意**:我们必须用 unpkg 加载 tressa 和 consolemd，因为出于某种原因，即使安装了 echomd，stackblitz 也永远找不到它。打开真正的控制台查看错误，stackblitz 的内部控制台出于某种原因没有显示错误。

让我们稍微剖析一下代码，以便理解所有的部分

*   `tressa.title`开始一个"部分"的测试，默认情况下标题会为你添加(" # ")
*   结束一段测试，并返回在这些测试上花费的时间
*   这里你可以把你的断言和一个可选的注释放在一起作为第二个参数。你可以用任何一个，两者没有区别。
*   使用 markdown 进行控制台输出，这有助于分离测试的各个部分
*   `tressa.async`需要等待异步操作吗？用这个包装。它返回一个测试这些案例的承诺。

您可以看到，tressa 使测试我们的代码变得非常容易。希望您还记得我们创建的只有`hyper.Component`的表格。如果没有，这里有一个快速刷新:

[https://stackblitz.com/edit/basics-table-7?embed=1&&](https://stackblitz.com/edit/basics-table-7?embed=1&&)

> 打开真正的控制台查看错误，stackblitz 的内部控制台出于某种原因没有显示错误。

### 组件

首先让我们加载 consolemd 和 tressa。然后，让我们为表中的页脚组件添加一个测试。这个组件实际上做不了多少事情，所以测试应该很简单。打开控制台查看测试结果。如果您正在进行异步测试，不要忘记在测试完成时调用`done()`回调。

[https://stackblitz.com/edit/intermediate-testing-02?embed=1&&](https://stackblitz.com/edit/intermediate-testing-02?embed=1&&)

很好，让我们继续看标题组件。对于这一个，让我们看看当我们传递排序的属性时，`<thead>`是否得到图标