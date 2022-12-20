# 调试角度应用

> 原文：<https://dev.to/nishugoel/debugging-angular-apps-5bnd>

我们一直在 Angular 中使用各种概念，但是当涉及到调试我们的 Angular 应用程序时，这就成了工作中最困难的部分之一。

解决应用程序中的错误的第一步是正确阅读和理解错误消息。大多数情况下，我们最终会发现错误是编译器实际上没有指出的。此外，有时，编译器给出不正确的错误，但指向正确的文件，然后当我们去文件并检查时，我们大多能够找到导致错误编译错误消息的语法错误。

理解错误信息
例如，下面列出的是编译器显示的错误信息。

[https://thepractical dev . S3 . Amazon AWS . com/I/9 JVC 4 botw 30 gtqx 1 brod . png](https://thepracticaldev.s3.amazonaws.com/i/9jvc4botw30gtqx1brod.png)

在这种情况下，编译器说 app-product 由于某种原因不是一个已知的元素。当我们忘记在模块中声明自定义组件时，我们经常会遇到这样的错误。或者，当我们添加组件时，可能会出现这样的情况:我们的应用程序中有多个模块，但它们不能正常交互。或者检查选择器名称或组件声明中的语法错误。

如果我们仍然不能找出错误，GitHub 和 stackoverflow 线程总是会来拯救我们！

使用 console.log()方法
现在，在我们正确读取错误消息后，调试 angular 应用程序的另一种方法是使用传统方法，即使用 console.log()并记录各种属性、对象等。在浏览器的 JavaScript 控制台中查看它们，并在程序的不同阶段检查它们的值。这显然不是调试我们的应用程序的最佳方法，但很多时候却很方便。

从“@angular/core”导入{ Directive，ElementRef，Renderer，host listener }；@ Directive({ selector:'[app-ch-color]' })export 类 BG Directive { constructor(private El:element ref，private Renderer:Renderer){ this . change color(' red ')；} @ Host listener(' click ')foo(){ console . log(' Host 元素被单击')；this.changeColor('绿色')；} change Color(color:string){ this . renderer . setelementstyle(this . El . native element，' color '，color)；}}

[https://gist . github . com/NishuGoel/c 7 CB 4d 94 FCA 191850 FD 368097979 e81b #文件记录](https://gist.github.com/NishuGoel/c7cb4d94fca191850fd368097979e81b#file-logging)

在这里，console.log 将显示并检查 bug，如果有的话！到那时，我们就能找出错误所在。

另一种方法是使用 CLI 提供的源地图。源码图是浏览器开发工具使用的小文件，用来在浏览器中运行的已编译的 JavaScript 代码和我们发现更容易阅读的原始文件之间建立一座桥梁。因此，源地图与浏览器开发工具一起使用。

举个例子，

[https://thepractical dev . S3 . Amazon AWS . com/I/oh 3n zh 6 HL 6 cinf ft 7 zyj . png](https://thepracticaldev.s3.amazonaws.com/i/oh3nzh6hl6cinfft7zyj.png)

我们有这个破碎的角度代码，显示一些错误。现在让我们看看源代码图，我们转到浏览器控制台内的 Sources 部分，在 CLI 项目下查找 webpack 文件夹。在 webpack 文件夹中，我们将找到 src>app，这个 app 文件夹复制了我们的 app 结构，因此我们可以查看我们的任何 typescript 文件，并在其中设置断点，以查看正在运行的应用程序的任何点的属性值。

[https://thepractical dev . S3 . Amazon AWS . com/I/JD 6 w6 a7 y 8 ba 562 w8 vslu . png](https://thepracticaldev.s3.amazonaws.com/i/jd6w6a7y8ba562w8vslu.png)

这些工具使用起来非常强大，因为它们使我们能够非常容易地试验和检查错误。此外，最棒的是这些工具可以在网上免费获得。

augry 是谷歌 Chrome 开发工具的扩展，用于在运行时调试 Angular 应用。它给出了应用程序工作的详细见解。与浏览器开发工具相比，使用 Augury 的好处在于它知道 Angular 架构，也就是说，它给出了组件、属性、依赖注入等的概述。哪个 DOM 没有权限访问！

占卜可以从 chrome 开发工具扩展下载:

Redux 开发工具
最后但肯定不是最不重要的，当我们使用 NgRx 时，我们得到 Redux 开发工具的帮助，这些工具与 NgRx 应用程序相关联。我们可以在 redux dev 工具的帮助下看看 NgRx 商店，从创建到商店的缩减器开始。

这些是使用和调试 Angular 应用程序的最佳工具。