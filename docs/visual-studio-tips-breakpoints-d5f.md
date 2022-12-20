# Visual Studio 提示-断点

> 原文：<https://dev.to/hutchcodes/visual-studio-tips-breakpoints-d5f>

我们大多数人都知道断点以及如何使用它们，但是您应该知道断点的一些未被充分利用的特性。

可以通过多种方式设置简单的断点

1.  将光标移动到要中断的行，然后按 F9 键
2.  右键单击代码行
3.  在檐槽中点击

一旦你设置了一个断点，你可以用它做一些事情。首先，如果你现在不需要断点，但是你认为你可能会回到它，你可以禁用它。

[![alt text](img/4b06da5f3f866bc65dd5c47d9a88eb93.png "Visual of disabling a breakpoint.")](https://res.cloudinary.com/practicaldev/image/fetch/s--tjPp9VlW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hutchcodes.net/img/2019/BreakpointDisable.gif)

您也可以从 Debug-> Disable all breakpoints 禁用所有断点。当您需要运行并重置，然后重新启用所有断点并再次开始调试时，这很方便。

您还可以根据变量值或任何布尔表达式来设置断点(是的，您可以使用 [No Side Effects](https://dev.to/2019/03/visual-studio-tips-no-side-effects-function-eval/) 格式化程序)。如果您希望在变量值为某个值时中断，这可能会很有帮助。

[![alt text](img/7fc5e46ab3c5a13de3f21d3eaf9faf20.png "Visual of creating a conditional breakpoint based on a boolean expression.")](https://res.cloudinary.com/practicaldev/image/fetch/s--vOMs60Ci--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hutchcodes.net/img/2019/BreakpointConditional.gif)

您还可以根据断点被命中的次数来设置断点的条件。例如，您可能知道第一次遇到断点对您的调试场景来说并不有趣，但之后的每次都很有趣。您可以将点击次数设置为大于 1。

[![alt text](img/c43261bdbc43c521d5d1421ac9f17e58.png "Visual of creating a conditional breakpoint based on hit count.")](https://res.cloudinary.com/practicaldev/image/fetch/s--mPFIKlMU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hutchcodes.net/img/2019/BreakpointHitcount.gif)

还可以根据名称或 id 将断点筛选到单个线程或排除某个线程。