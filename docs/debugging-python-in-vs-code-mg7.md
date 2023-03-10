# 在 VS 代码中调试 Python

> 原文：<https://dev.to/j0nimost/debugging-python-in-vs-code-mg7>

[![debug](img/5b23afce4801256103b1df655e0205d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q2zMhxhG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xxbci17j9b769rgrjnul.jpg)

我已经介绍了如何在您的虚拟环境中开始安装 [Pep8 和 Pylint](https://dev.to/j0nimost/setting-up-pep8-and-pylint-on-vs-code-34h) 。因此，您已经编写了一些 Flask/Django/Normal Python 代码，并希望调试您的应用程序。你如何着手做这件事？这篇文章将生动地描述如何做到这一点。

首先你需要在 VS 代码中打开一个终端，你可以使用 [![ctrl + escape](img/b716b08268bea8a391e64d5fae5bff91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b076wb7w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8jles7knwriapmkq8y3r.png) 
来完成这个操作。一旦终端打开，启用你的 virtualenv

```
 $ source env/bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以在 vscode 中启用调试了。您可以使用`f5`或
通过单击菜单选项卡中的“调试”来完成此操作。开始调试
[![debug start](img/afb9ad0f63379e2ec009ada7a3a745e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pRS0PwnX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qd3ql8gzpft0qehufqq2.png) 
当激活时，您应该会看到一个调试面板。vscode 的 python 包附带了许多针对不同 python 框架的预配置调试环境。我们需要设置环境变量来调试我们的应用程序，从新的调试面板中选择设置图标，然后添加环境变量来运行您的应用程序。对于我的例子，我添加了`env`并定义了一些环境变量来运行我的 Flask 应用程序。
[![add env](img/dba36e013cc5d2834d1610f2b2302b13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eevSYVTe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/to7coexi5sha9vfmnr63.png)

从 python 下拉列表中选择您选择的调试环境。这将允许应用程序在调试环境中运行，捆绑了在该环境中运行应用程序的需求集。
[![enable debug](img/96627d3c2cbe455ae3491b1036c89ba6.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--qeeNP6j5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/495g0r8ru4zyytiyeb2x.png)

最后，您现在可以设置[断点](https://en.wikipedia.org/wiki/Breakpoint)，这将允许您跟踪方法调用和应用程序运行周期。在调试过程中，您可以通过单击要中断/暂停的行的行号的左侧位置来添加断点。当调试暂停时，它应该看起来像这样。
[![breakpoint](img/e89e9600891b75e3b9b01061f53b94b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X0SK4_nH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/67k7v2o33s703k41xwoy.png) 
您可以使用顶部提供的调试控件来跟踪方法调用。
[![debug controls](img/78769dc6897d286073cf696aab66a481.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3Jorm0e5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h2vi4tw4802x6wza3bbz.png) 
从左边开始；

*   第一个控件继续运行并忽略该断点
*   第二个控件忽略该断点中的方法调用，并跳转到下一行代码
*   第三个控件遵循该行代码中的方法调用
*   第四个控件返回到父方法调用
*   第五个控件重新启动调试会话
*   第六个控件停止调试会话。

### 注:

VS 代码中的调试适用于所有支持的语言，设置可能会有所不同。