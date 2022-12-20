# 幻灯片基里希多-浏览器中的轻量级演示工具

> 原文：<https://dev.to/myterminal/slide-gazer---a-light-weight-presentation-tool-in-the-browser-2inm>

## 背景

早在 2011 年，当我还是一名. Net 开发人员时，编写 Visual Basic(我最喜欢的编程语言)和 C#的生活感觉很棒，在网页上呈现某些东西的唯一方法(我知道并习惯使用)是使用 Visual Studio 中的工具箱窗口。我可以用我的面向对象语言指定我想要的所有行为，可视化定制过去是通过属性窗口完成的，尽管它有点有限，但我过去常常想知道为什么世界上有人会需要 CSS。部分原因是因为我主要致力于创建 Windows 应用程序，涉足 web 开发有点太晚了。

我很快转向 Windows Presentation Foundation 进行我的应用程序开发，并因此转向 Silverlight for the web，我创作的视觉组件变得更好了。现在，我可以更好地控制一些东西是如何显示的，这一次我沉迷于编写自己的 XAML，而不是依赖于 Visual Studio，甚至进一步融合了表达。

## 快进到我的“前端”生活

2012 年，我被介绍到一个全新的开发世界:前端。随着 Silverlight 几乎被弃用，并且对 web 浏览器中的所有可能有了一点了解，我迷上了客户端技术栈。学习基本的 JavaScript，重新学习 HTML，用 CSS 填补 XAML 留下的空白，这些都是我花时间做的事情。

## 我的第一个基于浏览器的演示工具

有一个问题我一直想自己解决:找点别的东西代替微软 Powerpoint 来创作演示文稿。我认识的每个人都使用同样的软件。

既然我已经熟悉了网络浏览器，我很快就想出了一个用 Knockout.js 作为重要构件创建的演示工具。使用 CSS 制作幻灯片切换动画非常棒，不用使用 Powerpoint，也可以在网上随身携带我的演示文稿。尽管它节省了大量使用 Powerpoint 的时间，但演示文稿仍然需要创建为 HTML 文档。

后来，在我参加技术培训期间，利用我上次取得的经验，我开发了一个用于相同目的的改进工具，将 ASP.Net MVC 和前端堆栈的其余代码结合起来。展示技术培训非常有用，参与者体验了一种不同类型的演示，我过去常常展示与前端开发相关主题的培训，使用的是使用相同技术堆栈开发的幻灯片。这种实现也有同样的问题，尽管我不得不把所有东西都编码成 HTML，并且花时间去做好。

## 将 markdown 带到浏览器

我仍然在寻求创造一些不需要我为了表达一个想法而编写 HTML 页面的东西。我希望有一个简单的工作流程，就像快速编写一个纯文本文档并以幻灯片演示的形式呈现出来一样。

我创造了[淡出演示者](https://github.com/myTerminal/faded-presenter)作为一个成功的实验。这是一个简单的未开发的在线工具，你可以将 markdown 文档放入其中，并使用键盘的左右箭头键开始切换幻灯片。您也可以将演示文稿打印为单个文档。现在，这是有用的东西！

我仍然不明白为什么我把它作为一个 npm 包发布，它显然是一个应用程序，而不是一个可重用的包。令人惊讶的是，它显示今天有超过 1000 次下载。不知道大家是怎么消费的:)。

## 将 web-sockets 的力量与 faded-presenter 结合起来

我在想，我可以把这个东西做得更进一步，也许给它添加更多的风格:从另一个设备，可能是电脑或智能手机，远程控制演示怎么样？这就是我开始研究基里希多幻灯片的地方。

## TLDR；滑动基里希多

在准备工作演示时，我想到了能够通过远程设备控制演示的想法。我有一种感觉，我可以把这个想法再向前推进一点，并决定让 faded-presenter 保持原样，开始一个全新的实现。一如既往，我把它命名为“幻灯片基里希多”。

幻灯片基里希多是我第四次尝试解决这个问题，它建立在 fade-presenter 相同的核心原则上，除了简洁的设计之外，它还增加了许多功能。其中一些是:

*   许多幻灯片切换动画
*   幻灯片自动过渡
*   临时强调幻灯片上的粗体文本
*   能够从另一个设备控制一个演示文稿(这个项目存在的主要目的)
*   您仍然可以从浏览器打印演示文稿，并将其作为单个文档与观众共享

从 2018 年开始，它已经走过了漫长的道路，不幸的是，我没能在它身上花太多时间，所以我仍然有太多想看的东西。我的一些计划是:

*   添加单元测试！
*   改进 UX，使其更易于使用
*   实施“幻灯片搜索”,这样你就可以直接跳到一张幻灯片
*   支持不那么智能的浏览器。我一直只在谷歌 Chrome 上测试，除了 Android 上的 Chrome 之外，几乎没有机会在其他浏览器上尝试，从技术上讲，Android 还是 Chrome。

这仅仅意味着这仍然是一个正在进行的实验，还没有完成。我一直用它来展示我所有的演讲，并一点一点地改进它。

我已经等了很久，想和社区分享这个，并得到他们的意见，所以我写了这个帖子。

你可以在[slide-gazer.teamfluxion.com](http://slide-gazer.teamfluxion.com)查看，在 [GitHub](http://github.com/team-fluxion/slide-gazer) 查看源代码。

你觉得怎么样？