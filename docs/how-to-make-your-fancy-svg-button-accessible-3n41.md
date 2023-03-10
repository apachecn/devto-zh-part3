# 如何让你的 SVG 按钮变得可访问

> 原文：<https://dev.to/jonathanspeek/how-to-make-your-fancy-svg-button-accessible-3n41>

你很可能会发现自己有一天不得不制作一些设计师想象出来的疯狂按钮。你可能会开始寻找那辆老款的`<div>`，但是很容易，大换档杆🚚—让我们尝试使用您正在避免的那个`<button>`元素😬

我们首先简单地获取我们想要使用的 SVG 图标的代码。我很快做了一个 [Chemex 图标，你可以在这里使用](https://codepen.io/JonathanSpeek/pen/pQxYqo)(我喜欢☕️).咖啡将它粘贴到 HTML 中的一个`<button>`标签之间，就像这样(SVG 代码会很长)。

[![Initial <button> with SVG code inside](img/0d392967e5fc3faf246a4bdd65f76bf5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kpA9tfCz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xia56rz5uqbuhcd50q2d.png)

我们希望这个按钮去掉它的默认样式，所以让我们给这个按钮一个“id ”,并用一些 CSS 来定位它。

[![Strip the default styling of the <button> so we can make it better 💁](img/74912a6af5573cf47130ea7e4e991b6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BpRgaEFQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bwvk0ny8h5gf3560mabj.png)

给按钮一个比我们的 SVG 大的宽度/高度——这将有助于轮廓的可见性。说到这个，确保你的轮廓颜色和背景颜色之间的对比度通过这个。去掉讨厌的边框和背景，确保光标设置为指针。

此时，您有了一个可点击的按钮，当点击它时，显示您的浏览器为焦点状态选择的默认轮廓。让我们改变这种状况，让它变得更好。

[![Giving the button some focus 🤓](img/06910688e30dcf468a622621081ae5ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iI-32Ugh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0z1ugwhfiz8oax9la5ps.png)

现在，当我们点击或切换到按钮时，我们会得到一个很酷的小虚线轮廓，让我们知道我们关注的是哪里。

我们还想确保单击 SVG 本身不会得到一个轮廓。我们希望确保 Firefox 不会添加默认的虚线轮廓。当我们这样做的时候，我们可以给 SVG 一点悬停效果。

[![Adding our flavorful hover effect 😺](img/6ad10bf788a1f9bab08e255d423b258c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SElBoJgU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6gvpmbom69llymgergiq.png)

现在我们可以到酷的部分了😎我们不想让我们的屏幕阅读器用户因我们的按钮而烦恼或困惑。所以我们需要一个好的简短的描述来说明会发生什么。你通常也希望视觉用户知道他们点击的是什么，现在让他们猜猜看...

我们可以通过在按钮中的文本周围放置一个`<span>`元素并在视图之外对其进行样式化来轻松实现这一点。确保不要将 display 设置为“none ”,因为这也会阻止我们的屏幕阅读器访问它。

[![Telling our screen reader users what they’re clicking on 💬](img/79497d34cb19d28530473fe49c1a5313.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rSGl95oc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/87h0yfe65f8nyrxjfnd9.png)

最后，让我们确保:

*   对任何使用辅助技术的人隐藏 SVG
*   将 tabindex 设置为“0 ”,以便浏览器对任何键盘用户使用预期的 tab 键顺序。

[![Setting the proper tab order ⌨️](img/949f53cc41a69e62094b48697f1df201.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PM7SHmig--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p5c4lwsspyqrcm3i42dz.png)

你现在应该有一个真正的可访问的按钮，你可以感到自豪💥除了拍拍自己的背——现在就做——展望未来，你现在有了一些可重用的模式，你可以实现它们来帮助 web 变得更容易访问😘

这里有一个到 CodePen 示例的[链接，你可以随意创建自己的副本🍴](https://codepen.io/JonathanSpeek/pen/JeRwgp)

感谢阅读。如果你有一些关于可访问性的知识，一定要留下评论！