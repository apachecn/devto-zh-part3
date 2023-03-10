# 使用浏览器的开发人员控制台调试 JavaScript

> 原文：<https://dev.to/lambdatest/debugging-javascript-using-the-browsers-developer-console-3111>

一个前端开发人员花了相当多的时间来修复脚本错误。上个月，当我们研究 JavaScript 中的[跨浏览器兼容性问题时，我们以压倒性的共识发现，无论代码有多完美，](https://www.lambdatest.com/blog/javascript-cross-browser-compatible-issues-and-how-to-solve-them/) [JavaScript 错误](https://www.lambdatest.com/blog/common-javascript-errors-and-how-to-handle-them/)总是会存在。在早期，使用 console.log 或 alert()来检查错误。将它们散布在代码的每一行，最终帮助开发人员找到错误的真正所在。这是一个非常耗时的实践。在大型应用程序的情况下，这更像是要求雕刻家用铅笔刀雕刻出一个巨大的石头雕像。

然而，随着浏览器的最新更新，几乎所有主流浏览器都内置了开发工具。在本文中，我们将了解使用开发人员控制台进行调试以找出 JavaScript 错误的速度有多快。

### 我们开始吧！

在几乎所有基于 Windows 平台的主流浏览器中，可以通过按 F12 或 Ctrl+Shift+i，或者右键单击元素并导航到检查元素来访问开发工具。如果您使用的是 Mac，可以通过按 Cmd+Opt+i 在浏览器中访问开发工具。脚本调试器可以位于源代码选项卡下。开发人员不需要使用警告弹出窗口，只需使用脚本调试器上的断点就可以轻松检查代码。

[![](img/c60df2315aeb5eafa032ffe5109bba4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3xaInDJ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/07/JS1.png)

### 典型场景

让我们考虑一个典型的场景，您的 JS 代码正在抛出一个异常。如果你使用的是 Chrome，首先要做的是启用“异常暂停”图标。通过启用它，浏览器会在捕获到异常时停止代码执行。如果库处理异常，在这种情况下代码执行将不会暂停。然而，该功能在 [Firefox](https://www.lambdatest.com/test-on-firefox-browsers) 和 [Chrome](https://www.lambdatest.com/test-on-chrome-browsers) 中可用，在 IE 中不可用。

[![](img/eb1680cbdc6543810981217b5f1a39c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6TXDcURc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/07/JS2.png)

### 检查代码

暂停执行后，我们现在可以观察代码并找出错误。这是使用调试器的最大好处之一。脚本可以访问的一切对开发人员来说都是可见的。

在代码视图中，无论异常被抛出到哪里，在该行的末尾都会有一个红色图标。我们可以通过悬停在图标上或查看控制台上的错误消息来查看错误消息。将鼠标悬停在变量上可以进行进一步的检查。在 scope 选项卡下，我们还可以找到关于变量的更多信息。在“范围”选项卡中，变量按范围排序。例如，所有局部变量都排列在“局部”部分下。

[![](img/11f6d8978f5e42b93560fa52bb5819bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1FW_R1iv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/07/JS3.png)

### 弄清了问题

在小错误的情况下，错误消息让我们清楚地知道是什么导致了异常。例如，如果使用了 forEach 函数，而 url 的值不正确，我们将得到类似“forEach 不是函数”的错误消息。可以通过使用 if 语句并检查变量类型来修复该错误。但是作为一个有经验的前端开发人员，你需要在修复之前检测出问题的根源。

[![](img/2b42f11b2f710083448ec1db2b4c108f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IrW3n7wu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/07/JS4.png)

### 根本原因分析

可以通过检查“调用堆栈”选项卡来查找错误的根本原因。调用堆栈向我们显示了正在检查的当前函数的父元素。它以列表形式显示功能。通过点击列表，可以访问所有以前的功能。应该检查父函数，直到找到错误的根本原因。一旦我们找到一个产生坏价值的地方，搜索就结束了。

### 使用断点调试复杂问题

脚本调试器还有一个有趣的特性叫做断点，它可以帮助开发人员解决更复杂的问题。可以通过添加调试器来设置断点；语句内联，或者在脚本调试器中单击行号。

添加调试器后，当我们单击“run”按钮时，代码执行开始，当到达包含调试器语句的行时，调试器自动暂停执行。除非找到包含错误的行，否则应该在每个函数后添加断点。

调试时，检测到的任何脚本错误都会自动显示在控制台上。开发者只剩下一个工作了。找出有错误的代码行，修复它并再次调试代码。

[![](img/78cc271f787906342e72bdc65ce548a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9noxndyc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/07/JS5.png)

### 使用控制台进行调试

除了内置的调试工具，开发人员还可以在控制台中使用某些命令来修复性能问题。

*   如果一个进程花费了相当多的时间，console.time()可以用来启动一个倒计时器。用 console.timeEnd()打印这个值将会给出执行某个函数所花费的时间的信息。
*   对于一个更棘手的性能问题，为了查找内存泄漏，可以使用 console.memory 来显示堆大小。
*   对于重复出现的代码，console.count()可用于确定脚本读取代码的次数。
*   如果您想在条件为 false 时记录一个值，可以使用 console.assert(condition，msg)。然而，在使用 Node JS 时，这可能会抛出断言错误。
*   使用 console.table()可以将记录的对象以一种精心安排的表格格式显示出来。
*   最后，如果您的控制台中有许多日志，并且您希望在一个干净的视图中重新开始，请使用 console.clear()。

[![](img/1d2554885b19c0143332c99e8ead370b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8sKS1Dk5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/07/JS6.png)

浏览器的内置调试工具足够强大，可以减少调试代码的时间。无论您使用的是控制台还是脚本调试器，如果您的浏览器得到更新，调试现在会更加高效和有趣。

### 调试 LambdaTest 中浏览器兼容性 JavaScript 错误

JavaScript 不能跨浏览器兼容，这是一个不争的事实。不是所有的浏览器都完全支持 JavaScript，早期的浏览器完全不支持现在一些非常常用的 JS 标签。发现跨浏览器兼容性问题并调试它们是一个相当大的挑战。但是，在 [LambdaTest](https://accounts.lambdatest.com/register) 我们提供了一个拥有 2000 多种浏览器和操作系统组合的平台，每一种都配备了强大的调试工具。这是一个查找和调试跨浏览器兼容性问题的完美平台，尤其是在您无法本地访问的浏览器版本或操作系统上。我们在移动浏览器上也有调试工具，给你一个调试移动网络问题的完美工具集。

原文出处:[lambdatest.com](https://www.lambdatest.com/blog/debugging-javascript-using-the-browsers-developer-console/)