# Chrome DevTools 能做到？

> 原文：<https://dev.to/yashints/chrome-devtools-can-do-that-4a8l>

Google Chrome 是目前开发者使用的最流行的浏览器之一。 [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools/) 可以帮助你在浏览器中开发、测试和调试你的网站，从而极大地改善你的工作流程。

然而，有很多我们不知道的提示和技巧可以帮助我们更进一步。仅仅因为我们在菜单中没有看到它们，并不意味着我们不应该使用它们来提高我们的生产力。

所以我从[伯克·霍兰德](https://twitter.com/burkeholland)和[莎拉·德拉斯纳](https://twitter.com/sarah_edo)的 [VS 代码可以做](https://vscodecandothat.com/)系列中得到灵感，并决定在这里写下这些。希望它能帮助你，就像它每天帮助我和许多其他人一样👌。

你准备好了吗？我们开始吧:

## 屏幕截图

您可以捕获整个页面的屏幕截图或屏幕中的内容，而无需任何扩展。只需按下`CTRL` + `Shift` + `P`用于 windows，按下`Cmd` + `Shift` + `P`用于 Mac 用户，打开命令托盘，键入`screenshot`即可看到三个选项的菜单:

*   捕捉全尺寸截图
*   截图
*   捕获节点屏幕截图

选择这些选项中的任何一个都会将网站的图像保存到您的计算机上。

[![Chrome DevTools Screenshot](img/76c930a5fc9f405522974c9fd2ea7142.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wcWocnan--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://yashints.dev/screenshot-be57f65b4afe2d166e8804fbd60e7bc4.gif)

## 在元素面板中拖拽

我直到上周才知道，这太棒了。你可以将元素拖放到元素面板中任何你想要的地方，它会反映页面中的变化。如果你想快速看到一些小的变化，这是非常好的。

[![Chrome DevTools Drag Drop](img/85464a1418d45ab5afdf513d5e198a8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MKqSgyLR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://yashints.dev/drgdrop-f4feae655551aa9792fb04e45003d921.gif)

## {漂亮打印}缩小版源代码

有时您希望看到源代码像原始代码一样格式化。这对于 CSS 规则或代码来说更有意义。这个内置特性在调试 CSS 规则时节省了我一些时间。只需按下页面底部的`{}`。

[![Chrome DevTools pretty print](img/228f9bad3e77a674c0aaebdebd4e0787.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vcAYNxTI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://yashints.dev/prettyprint-dd38eccdebbc15c2d7eb637b9958fb57.gif)

## 引用控制台中选中的元素

有时，您可能希望使用 JavaScript 操作元素，而不是在元素面板中更改 HTML。

[![Chrome DevTools reference element](img/a7bb91eb566ecf3601650af8ff241c51.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cvwg-4x4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://yashints.dev/refelement-a4ae79b64ba660179313786a587b9d52.gif)

## 观看表情

很多时候你想观察一个特定的表情，看看它在特定的环境下是如何变化的。这真的很简单，只需将表达式添加到 watch 部分:

[![Chrome DevTools watch expression](img/1377d67874d0f992fac15a8b7670bbd0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LOzsXkU---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://yashints.dev/watch-a0314ef5d74fb1ba52144f99565669ea.gif)

正如你看到的，你可以看到最后一个使用`$_`的表达式。

## 片段

我们都有一些反复使用的代码。从点击处理程序的模板到文档就绪函数，它可以是任何东西。这就是代码片段部分派上用场的地方。你可以储存一个片段，并很容易地使用它。只需打开片段部分，点击`new`。写下您的代码片段，无论何时您想使用它，windows 用户按下`Ctrl` + `Enter`，Mac 用户按下`Cmd` + `Enter`，或者右击并按下`run`。'

[![Chrome DevTools code snippets](img/040171fc51c5db4b2c232b53ee8b2cc5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pAr-QNWC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://yashints.dev/snippet-fd4d9ef66e4d2df85260caf41e36bd57.gif)

## 覆盖位置

如果您正在编写一个 web 应用程序，其中的信息取决于用户的位置，那么您可能希望检查该应用程序在用户的位置方面的行为是否符合预期。这就是 Chrome DevTools 中位置覆盖非常有用的地方。只需按下`Ctrl` + `Shift` + `P`用于 windows，按下`Cmd` + `Shift` + `P`用于 Mac 用户，打开命令托盘，键入`sensors`，然后从覆盖位置下拉列表中选择不同的位置。如果你有的话，你甚至可以设置一个自定义的纬度和经度。

[![Chrome DevTools location override](img/c5b45617d5297a2152f00f1ff6505ac7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xe4FA6w---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://yashints.dev/locationoverride-2a3d8bc3c58bc2f66ba4f8a03db1f6ce.gif)

## 编辑屏幕上的任何文本

有时你有一个有限的宽度，想看看一个长文本在页面上是什么样子。或者您只是检查文本是否足够长，省略号是否出现。Chrome DevTools 有一个名为设计模式的功能，你可以在控制台中设置它，然后更改任何你想要的文本，并动态观察这些更改。超级酷吧？😎

[![Chrome DevTools design mode](img/fe991dba53e1c58db7c8a198a67d6fe2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AEMWWCxk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://yashints.dev/designmode-109b4a5d9db472db0cab6ff52fcf42d8.gif)

这个帖子到此为止。留意下一个帖子👀。