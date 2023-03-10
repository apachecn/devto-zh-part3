# 构建一个简单的 CSS 网格布局系统

> 原文：<https://dev.to/vijitail/build-a-simple-css-grid-layout-system-3nkn>

你必须非常熟悉像 Bootstrap，布尔玛等流行的 CSS 框架的布局系统。但是你有没有想过这些是如何构建和在幕后工作的？🤔

在这个故事中，让我们试着建立我们自己的网格布局系统，并了解基本知识。😃

我将在这个项目中使用 Sass，这样可以编写更少的代码。Sass 真的很简单，如果你是一个纯粹的 CSS 人，你也不会很难跟上。官方 Sass 指南是开始学习 Sass 的好地方。

让我们从定义响应断点开始

[![](img/e35ac9ad80e7083abd926d48890622f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8MvxFsby--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5d6xziawoxoxciv8hcj7.png)

这些将声明网格支持的各种屏幕尺寸。

接下来让我们设计行和列的样式

[![](img/272fe1d97d0f1b39b9319eb57595f74e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PhesWr1D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bg9nec2dm08hgc5jx3mn.png)

接下来的几行将是我们布局的核心，基本上我们将遍历断点图，添加媒体查询，并为每个断点创建 12 列。我们还将包括每个断点的容器。

[![](img/43e3e2666401a481faac191dbefe5a36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--64NhKTaL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hta7v1q3s0pidgksabnc.png)

想象一下，我们用 CSS 为同样的事情要写多少代码。🤦不管怎样，‍♂️在前进。

为了计算列的宽度，我们需要根据百分比大小将列分成 12 组。为此，让我们在 Sass 中编写一个函数，该函数将列大小作为参数，并返回以百分比表示的宽度。

[![](img/e43a079d6d2fe678c34c63c05ff6c3e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L2WFZWCG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sjshnfz6ui7vz8g1593c.png)

然后调用 for 循环中的函数

[![](img/8eb85238c58f678ebdacc229889cbf4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qDxQvw-t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qt3puqqt6w8nmmzgym6e.png)

就这样，我们完成了 12 列布局系统🍻。

作为一个额外的特性，让我们编写一个类来移除列边距，并相应地更新我们的 calcWidth()函数。

[![](img/b05e9e79583081a11337b34c1c9a8bff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DAF3if95--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fywnam2lohpc5wf0ag1z.png)

看看中的布局。调整浏览器的大小，并查看列的行为。

* * *

感谢您阅读帖子。如果你想添加任何其他功能，请在下面告诉我，或者你可以在 Instagram 上给我发消息

*本帖最初发布在[媒体](https://medium.com/@vijit2ail/build-a-simple-css-grid-layout-system-8e37e3e16d5f)T3 上*