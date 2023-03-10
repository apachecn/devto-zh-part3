# JavaScript 跨浏览器兼容问题及其解决方法

> 原文：<https://dev.to/lambdatest/javascript-cross-browser-compatible-issues-and-how-to-solve-them-oe8>

近来，一个好的应用程序或网站已经不仅仅是静态的部门元素的集合。现代网站是交互式、高性能的应用程序，有助于完成复杂的端到端业务流程。JavaScript 为我们达到这一水平做出了重大贡献。它在开发者中非常受欢迎，但是并不是所有的浏览器都支持它。开发运行 javascript 的跨浏览器兼容网页有时是一个挑战。帮助开发者应对这一挑战是我们开发[浏览器兼容性测试平台](https://accounts.lambdatest.com/register)的驱动力之一。

### JavaScript 跨浏览器兼容性

各种原因，如使用[不正确的文档类型、特定于供应商的 CSS 样式和过时的浏览器更正](https://www.lambdatest.com/blog/9-ways-to-avoid-cross-browser-compatibility-issues/)都被引用，并且还提出了一些可能的解决方案。

JavaScript 开发人员从来没有重视过浏览器的功能，因此只要这种语言存在，就会出现 JavaScript 错误。就正确支持 JavaScript 而言，甚至一些非常流行的 web 浏览器也落后一两年。

所有浏览器处理脚本的方式都不同；因此，所有报告 JavaScript 错误的方式都不同。不幸的是，除非所有的 web 浏览器开发人员都同意一套处理 JavaScript 或任何其他脚本语言的标准，否则我们将会看到 JavaScript 错误。

为了更好地说明这一点，在这里，我们将首先讨论一些常见的 JavaScript 问题，然后是跨浏览器的 JavaScript 问题。

### 解决常见 JavaScript 问题

**使用 Linters**
追随 HTML 和 CSS 的脚步，Linters 可以为你提供质量更好、错误更少的 JavaScript 代码。它们还会显示有关不良行为的警告，并且可以定制为严格或宽松的方式。

**使用 JavaScript 调试器和其他浏览器开发工具**
浏览器开发工具已经被发现在[调试 JavaScript](https://www.lambdatest.com/blog/debugging-javascript-using-the-browsers-developer-console/) 时非常有帮助。首先，JavaScript 控制台会报告代码中的错误。这类工具的一个显著特点是能够向代码添加断点。在断点处，您可以方便地判断环境的当前状态，并查看正在发生什么以及需要采取什么样的进一步行动。

**其他一些性能问题**
使用 Browserify 这样的工具制作一个单独的脚本包可以让你不必加载过多的 JavaScript 代码。此外，减少 HTTP 请求的数量对于提高性能也非常有效。在使用 API 时，建议在不经常使用时关闭 API 功能，因为它们可能会对电源造成很大负担。同样，动画对性能的要求也很高。许多 JavaScript 库提供由 JavaScript 编程的动画功能，但更好的方法是使用本地浏览器特性制作动画。

### 解决跨浏览器 JavaScript 问题

**[特征检测](https://www.lambdatest.com/blog/feature-detection-with-modernizr-for-cross-browser-compatibility/)**
HTML 和 CSS 以其宽容的本性而闻名，但 JavaScript 在出错时没有太多空间。如果 JavaScript 引擎发现错误或无法识别的语法，很可能会显示一条错误消息。这种语言的许多现代特征在旧的浏览器中不能工作。“Promises”和“Typed Arrays”就是两个很好的例子。类似地，“箭头函数”为编写匿名函数提供了更短、更方便的语法。然而，Internet Explorer 和 Safari 不支持它，如果您在代码中使用它，很可能会给您带来问题。

这个想法是首先运行一个测试来判断一个特性是否被当前的浏览器所支持。接下来是代码的有条件执行，为所有浏览器提供所需的体验，而不管它是否支持该特性。

**[使用库](https://www.lambdatest.com/blog/top-21-javascript-and-css-libraries/)**
当选择一个用于编码的库时，开发者必须确保它能在你希望应用程序支持的一组浏览器上工作，并彻底测试实现。此外，您应该确保该库是受欢迎的和得到良好支持的，并且不太可能在短期内过时！

聚合填充
聚合填充本质上是一段代码或一个插件，提供浏览器原本应该支持的技术。它们通常由外部 JavaScript 文件组成，您可以在自己的项目中轻松使用这些文件。然而，它们与图书馆有很大的不同。一方面，库倾向于补充已经存在的特性并使开发者的生活更容易，另一方面，polyfills 提供了根本不存在的特性支持。

当开发人员希望使用现代 JavaScript 功能时，他们已经开始探索的另一个选项是将具有 ECMAScript 6/ECMAScript 2015 功能的代码转换为与旧浏览器兼容的版本。这被称为 JavaScript Transpiling。

**不良浏览器嗅探代码**
网络浏览器有一个用户代理字符串，其功能是找出浏览器是什么。当 Netscape 或 Internet Explorer 曾经是唯一的选择时，开发人员利用“浏览器嗅探代码”来找出用户正在使用的浏览器，并为他们提供正确的代码来运行相同的浏览器。

对于开发者来说，跨浏览器兼容性不再是一个选项。这是开发过程中的一个重要部分，需要同等的重视(如果不是更多的话！)作为样式或脚本。JavaScript 很简单。只需稍微注意一下细节，你就可以推出一款在所有可用平台上都能完美运行的摇摆应用了！

来源: [LambdaTest 博客](https://www.lambdatest.com/blog/javascript-cross-browser-compatible-issues-and-how-to-solve-them/)