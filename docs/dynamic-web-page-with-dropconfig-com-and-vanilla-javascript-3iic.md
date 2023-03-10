# 使用 Dropconfig.com 和普通 JavaScript 的动态网页

> 原文：<https://dev.to/dropconfig/dynamic-web-page-with-dropconfig-com-and-vanilla-javascript-3iic>

就像拉里·沃尔(Perl 编程语言的创始人)一样，我们构建了 Dropconfig，让简单的事情变得容易，让困难的事情变得可能。

Dropconfig 位于非常方便的抽象层:托管小块数据，使它们易于编辑，也易于代码使用。

在这里，我将展示它们是如何一起流动的。我们将需要:

*   codepen.io 帐户，
*   对 HTML 和 JavaScript 知之甚少，
*   可选的 dropconfig.com 帐户(这是可选的，因为 DropConfig 允许创建类似 Pastebin 的匿名 drop config 而无需注册)。

这样，我将创建一个 codepen，并使用 dropconfig-file 对其进行远程控制。一旦我们完成，我将能够改变代码笔中显示的内容，而不必编辑代码笔本身。Dropconfig 将成为我管理显示内容的地方。

当然，这不是很令人兴奋，因为编辑 codepen 已经和编辑 dropconfig 一样容易了。当你想像修改 codepen 一样简单地修改公开托管的 HTML 页面时，真正的好处就显现出来了。我将演示的技术可以用于任何 HTML 页面。

我将从一个最小的“Hello World”代码开始:

[![](img/85c682be7ef402d716103ef6f551db53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I7JI5Hfd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ace47D9g8ybtPqo7GPZoyMw.png) 

<figcaption>典型的《Hello World》笔名</figcaption>

我已经有了一个 Dropconfig 账户和一个 Dropconfig-org(它们类似于 Github orgs)。我将创建一个 Dropconfig-repository 来保存我的 codepen 实验:

[![](img/59572da990bc90ee5c097c036d2016a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WyLUuqdG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ax4EqceCKKTmA59UhTe5YtA.png) 

<figcaption>点击【创建回购】</figcaption>

新的存储库看起来是空的，但不会太久:

[![](img/3554d089215ef4b9119a322dce372c53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rqCy1_rl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXIGkyziIivHHvh1ssxZLXA.png)

我从一个非常简单的代码开始: