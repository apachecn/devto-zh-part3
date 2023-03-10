# Chrome 开发工具——更高级的调试技巧

> 原文：<https://dev.to/dirkstrauss/chrome-dev-tools--more-advanced-tips-for-better-debugging-5cgh>

Chrome 开发工具——Chrome 浏览器中的一些高级调试技巧为 Web 开发人员提供了难以置信的价值。[在上一篇文章](https://dev.to/dirkstrauss/chrome-developer-tools--easy-web-debugging-you-need-to-know-4bko-temp-slug-1396917)中，我们看了一些你应该知道的基本调试技巧。在这篇文章中，我们将更深入地研究一下，看看在 [Chrome 开发工具](https://amzn.to/2UxaIzx)(这是一个附属链接)中还有什么可供 web 开发者使用的。

## Chrome 开发工具让调试变得简单

Chrome Dev Tools 是一组直接内置于 Chrome 浏览器中的 web 开发工具。您可以在不重新编译代码的情况下动态地进行更改。您还可以更快地调试和解决问题。这有助于 web 开发人员更快地构建网站，并创建更稳定的 web 应用程序，这些应用程序可以跨不同的设备适当地伸缩。

### 找别的？请尝试以下链接:

*   [Chrome 开发者工具——你需要知道的简单网络调试](https://dev.to/dirkstrauss/chrome-developer-tools--easy-web-debugging-you-need-to-know-4bko-temp-slug-1396917)
*   这个了不起的扩展会让你变得时髦起来
*   难以置信的强大多选功能。现在提高生产率

将此与 Sass CSS 结合起来，您就能够通过定位特定断点来轻松地设计您的 web 应用程序。断点是指站点内容对特定设备的响应点。然后更改布局，为用户提供可能的最佳观看体验。

## 让代码看起来漂亮

[![Chrome Dev Tools Make Code Look Pretty](img/09958d05d4c1816308277636f4d5c83c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fdrIMIKo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-pretty-code-1.png%3Fw%3D640%26ssl%3D1)

在检查器中查看文件时，有时您需要更加清晰。对于脚本文件来说尤其如此。幸运的是，你可以点击花括号，Chrome Dev Tools 将打开一个包含格式化代码的新文件。在上面的截图中，您可以看到我正在查看 **Sources** 选项卡中的脚本。

## 节流为慢 3G

[![Chrome Dev Tools Network Slow 3G](img/f16bb204cad2f1ccfd091335cd62c4e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZDckP-8M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-network-slow-3g.png%3Fw%3D640%26ssl%3D1)

有时你需要意识到这样一个事实，当你的一些用户没有快速连接时，他们需要访问你的站点。Chrome Dev Tools 允许 web 开发人员调节他们的 web 应用程序，以模拟缓慢的网络速度。

在**网络**选项卡上，点击**在线**下拉菜单，选择一个预设或添加一个自定义网络节流配置文件。

## 给脚本添加断点

[![Adding breakpoints in Chrome Dev Tools](img/254596f37da95e38b7e3ad83f62c0a98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m7bQwPXN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-add-breakpoints.png%3Fw%3D640%26ssl%3D1)

如果你需要调试你的一个脚本，Chrome Dev Tools 允许你通过 **Sources** 标签来完成。在左侧的页面树中选择脚本，然后单击要添加断点的行号。

下次运行脚本时，代码将在您设置的断点处停止。

[![Chrome Dev Tools Step Controls](img/d200efb4c237deb27ee0f5dfda506ff2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--84QxfP4B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-debugger-controls.png%3Fw%3D640%26ssl%3D1)

要浏览您的脚本，您可以单击步骤控件。这些是:

*   恢复脚本执行
*   跳过下一个函数调用
*   进入下一个函数调用
*   跳出当前函数
*   步骤

如果需要，您还可以**取消断点**和**暂停异常**。

## 事件监听器断点

[![Event listener breakpoints in Chrome Dev Tools](img/a7015d2e084b9d2b7e0952138b4a558d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9QyQHG6I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-event-listener-breakpoints.png%3Fw%3D640%26ssl%3D1)

Chrome Dev Tools 的另一个很好的特性是**事件监听器断点**窗口。这允许您在特定事件侦听器执行时暂停代码执行。

在**源**选项卡中，点击**事件监听器断点**窗口并展开事件。然后，您可以检查想要中断的事件。当事件监听器执行时，Chrome Dev 工具将暂停，允许您检查代码。

## 添加表达式

[![Chrome Dev Tools Adding Expressions To Watch Window](img/60699323bb73d119f225cb6b9e1ea23c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t29wVq7_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-add-expression-to-watch.png%3Fw%3D640%26ssl%3D1)

Chrome Dev Tools 中的 **Watch** 窗口允许 web 开发者添加表达式。然后将它们添加到"监视"窗口中。您可以从**范围**窗口中看到当前范围内变量的值。

## 登录控制台窗口

[![Using the Console Window in Chrome Dev Tools](img/0b6194102a866dc37e6300f1d0aadca1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VGfAIRGQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-console-window.png%3Fw%3D640%26ssl%3D1)

Chrome Dev Tools 允许 web 开发人员将信息记录到**控制台**窗口。如果您将*控制台. log* 添加到您的脚本中，该信息将被输出到**控制台**窗口。

您可以从**控制台标签**访问**控制台**窗口，或者作为显示在任何其他窗口旁边的抽屉。要将其作为专用的**控制台**面板打开，在 Mac 上按 *Ctrl+Shift+J* 或 *Cmd+Opt+J* 。

## 保存你的控制台输出

[![Save Console Output in Chrome Dev Tools](img/449c718a84cb55bd454fea7a813e8f02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YgC42HDS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-save-console-output.png%3Fw%3D640%26ssl%3D1)

如果你需要保存你的**控制台**窗口的输出，Chrome Dev Tools 允许你通过右击并从上下文菜单中选择**另存为…** 来完成。

## 保存控制台历史

[![Chrome Dev Tools Preserve Console History](img/094145c095532d3b392a04f85834d56a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jnroyAlW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-preserve-console-history.png%3Fw%3D640%26ssl%3D1)

Chrome Dev Tools 允许 web 开发人员在次页面刷新之间保存控制台历史**。默认情况下，此选项不打开。要启用它，请单击设置图标。然后勾选**保存日志**复选框。**

输出到控制台窗口的消息将被保留，直到您清除控制台或关闭选项卡。

## 测试代码修复

[![Edit code in Chrome Dev Tools](img/3c35d46b55d2ede4d739eeb611865250.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VehJh6s5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-edit-script-code.png%3Fw%3D640%26ssl%3D1)

如果你在你的脚本中发现了一个 bug，你可以直接从 Chrome Dev 工具中编辑代码。您不需要离开 Chrome Dev Tools UI 来更改脚本代码。

如果您的代码当前在一个断点处暂停，单击**恢复脚本执行**按钮。然后根据需要添加或编辑脚本。你会注意到我刚刚添加了一个 *console.log* 来输出单词 **Hello** 。另请注意，该文件被标记为已修改。

要保存更改，请在 Windows 上按 *Ctrl+S* 或在 Mac 上按 *Command+S* 。请注意，对此文件的更改不会保存到文件系统中。当您再次运行脚本时，我所做的更改会显示在**控制台**窗口中。

## 审核网站速度

[![Run site audit in Chrome Dev Tools](img/bef14dc0ad16aa4df05fe17f421e4960.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D61N1CFU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-run-audit.png%3Fw%3D640%26ssl%3D1)

Chrome 开发工具允许你审计你的网站速度。建议您在**匿名**模式下浏览您的网页。要在 Visual Studio 中以匿名模式运行您的页面，[请查看 Scott Hanselman 的文章](https://www.hanselman.com/blog/VisualStudioWebDevelopmentTipAddChromeIncognitoModeAsABrowser.aspx)，了解如何在 Visual Studio 中实现这一功能。

要开始审计，点击**审计**选项卡。然后，您可以为您的审计设置几个选项。您可以选择审核您站点的**移动**版本或**桌面**版本。

**审计**选项允许您选择要运行的审计类别。因为我只对网站的性能感兴趣，所以我只选择了**性能**选项。

您还可以根据特定的移动设备连接来限制页面。最后， **Clear storage** 选项允许您在加载网页之前删除所有存储。这类似于访问者第一次浏览你的页面。要审核回头客，取消选中**清除存储**选项。

### 运行您的审计

准备好后，点击**运行审计**按钮。

[![Chrome Dev Tools Performance Metrics](img/2098de067551e440195d009659b2bc50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X2KOAx4l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-performance.png%3Fw%3D640%26ssl%3D1)

因为我们使用了模拟节流，Chrome Dev Tools 现在可以根据你的页面在桌面上的加载时间推断出它在移动设备上的加载时间。它实际上并没有限制你的 CPU 或网络。

当审计报告加载时，它会显示您的性能基线。您的绩效分数显示在报告的顶部。这个数字越高越好。它还显示了您站点的各种指标。

您可以将鼠标悬停在某个指标上以了解更多信息，并访问特定指标的文档。

[![Chrome Dev Tools Opportunities and Diagnostics](img/1ad4ddbdf1e2d5f5fa9261b23e6411a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NUKcWCDL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-opportunities.png%3Fw%3D640%26ssl%3D1)

在下面的**机会**部分，报告显示了一些提高网站性能的技巧。**诊断**部分显示了关于到底发生了什么的更详细的信息。

Chrome Dev Tools 审计可以让你对你的站点的性能和瓶颈有有价值的了解。这里还有很多东西要学。查看 Google 的 Web 开发者工具网站上的优秀支持文章。

## 模拟移动设备

[![Set device mode in Chrome Dev Tools](img/63e7f7432f7715fe235a68579c9625a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pHGb3EnM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-view-mobile-devices.png%3Fw%3D640%26ssl%3D1)

如果你需要跨不同设备检查你的站点的响应布局，你可以让 Chrome Dev 工具模拟特定的移动设备。为此，点击左上角的**切换设备工具栏**图标。也可以按 *Ctrl+Shift+M* 。

[![Specify specific mobile device in Chrome Dev Tools](img/bafaea1b58ca872bf1077ae6e081d4bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xS0-qtJP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/dirkstrauss.com/wp-content/uploads/2018/12/chrome-dev-tools-view-as-iphone-x.png%3Fw%3D640%26ssl%3D1)

你的浏览器窗口将会改变你的站点的布局，就像在不同的设备上被浏览一样。在这种情况下，我选择了 iPhone X，但您可以从下拉列表中选择其他几个设备。

为了迎合不同的断点，您需要在样式表中针对这些断点。我使用 [Sass CSS](https://dev.to/dirkstrauss/sass-css--this-awesome-extension-will-make-you-sassy-3def-temp-slug-408589) 在我的样式表中轻松定位不同的移动断点。

## 包装完毕

Chrome Dev Tools 为 web 开发人员提供了一套丰富的调试工具，使您能够微调您的 web 应用程序。在这些工具中，您还可以做更多的事情(超出了我在一篇文章中所能涵盖的范围)。查看谷歌提供的文档了解更多信息。

帖子[Chrome Dev Tools——更高级的调试技巧](https://dirkstrauss.com/chrome-dev-tools-advanced-debugging-tips/)首先出现在[编程和技术博客](https://dirkstrauss.com)上。