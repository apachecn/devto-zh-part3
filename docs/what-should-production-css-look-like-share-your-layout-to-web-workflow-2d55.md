# 生产 CSS 应该是什么样的？共享您的布局到 web 工作流程

> 原文：<https://dev.to/jenc/what-should-production-css-look-like-share-your-layout-to-web-workflow-2d55>

当有人让你用 HTML 和 CSS 构建一个布局时，你是怎么处理的？与生产工作相比，您如何在测试中接近它？

我一直在面试，测试，似乎不能搞清楚酒吧是什么。我在有长 af 的地方工作过。css 工作表，以及其他编译较少的工作表。以前我是根据我看过的文档或 glitch.com 的例子来构思如何写 CSS 的。我还听说需要将不同组件的 CSS 模块化，使其更容易重用，更简短，从而更容易阅读。

我是如何做到的:

1.  确保我在 VSCode 上安装了更漂亮的东西或棉绒。
2.  Photoshop 中带参考线的切片布局，在元素之间测量
3.  决定哪些区域需要网格和 flexbox。
4.  启动新的 HTML 文档，嵌入外部 CSS 文件，将 px 转换为 em，包括 3 个媒体查询或带有超光速粒子或引导的原型
5.  或者包含所有与普通 JS 的交互，或者使用 JS 框架

文档和库代码看起来真的很全面(即小、中、桌面屏幕的媒体查询)，所以我试着模仿它。在得到一些反馈后，我意识到我的东西太复杂了，我的 CSS 没有条理。

到目前为止，我收集了一些需要改进的地方:

*   如果使用网格，使用`height: 100vh`使最小高度出现在视口的高度
*   使用具有语义名称的类来应用样式，而不是 id 或元素(例如，`.site_header`而不是`#header ul`)
*   包括任何动画或例外的跨浏览器前缀(即 Safari 中的首字下沉，或 Safari 中的`webkit-`，Firefox 中的`moz-`，IE 中的`ms-`)。使用 autoprefixer 使这变得更容易。
*   将任何尺寸转换为`em`和`rem`而不是 px
*   尽可能使用网格或 flexbox，始终坚持网格
*   写得越少越好，在每一个小细节上详细的空白和填充会使你的风格让其他人读起来很困惑。
*   要使所有东西都显示为设计中应有的大小，请使用`*{box-sizing: border-box;}`
*   不惜一切代价远离内联风格。
*   `float`作为定位选择器已经是过去的事情了。
*   并不总能解决问题

我对你挑战其中的任何一个都持开放态度，因为这是基于中等帖子和观察 glitch 社区示例的拼凑的事后想法

我也有一些问题要问开发者:

1.  当你得到一个布局时，你如何处理手机的响应？我完全理解设计师关于定位和堆叠顺序的决定，沟通是关键。但是在测试的情况下或者和不总是知道自己想要什么的自由客户一起工作时，你通常会怎么做？我默认了一个带滑动/下拉菜单的汉堡菜单。

2.  网格上的任何特定选择(4、10 或 12 列。30em，40em)？我真的很想在更少的列上使用精确的像素来尽可能地匹配桌面版本，但最终我真的对此感到困惑。我还见过最小网格项目重复 9 次的网格布局...我想这取决于设计。

3.  你包括多少媒体询问？你如何决定断点在哪里？我见过完全功能化的例子，人们只为一种屏幕尺寸(400 像素或 768 像素)编写程序。够了吗？

4.  对动画使用 CSS 还是 JS 有什么看法？

5.  在什么情况下你认为有必要少用 SASS？BEM 还是个东西吗？

Annnd...虽然我的人生目标是真正擅长 JS，但我还是忍不住好奇。我发现 CSS 是我倾向于认为很容易擅长的东西，但实际上充满了如此多的细节。
...任何其他想法或批评都非常感谢！感谢您的阅读！

更新:刚刚在 YouTube 上发现了 Jen Simmons 的布局土地系列，并添加了一个链接，因为她非常擅长解释 CSS grid/flexbox 的差异和利弊

在过去的几年里，我一直在阅读其他一些资料:
[使用功能性 CSS 简化样式| Rangle.io](https://blog.rangle.io/styling-with-functional-css/)
[使用 CSS grid 的常见响应性布局和一些不使用 CSS grid 的常见响应性布局| Medium](https://medium.com/samsung-internet-dev/common-responsive-layouts-with-css-grid-and-some-without-245a862f48df)
[初学者指南在 Grid 和 Flexbox 之间进行选择| Medium](https://medium.com/youstart-labs/beginners-guide-to-choose-between-css-grid-and-flexbox-783005dd2412)
[CSS Grid Starter 布局| CSS-Tricks](https://css-tricks.com/snippets/css/css-grid-starter-layouts/)
[圣杯布局 CSS Grid](https://bitsofco.de/holy-grail-layout-css-grid/)
[简单地说:CSS vs Javascript| Jen Simmons 为 YouTube 上的 LayoutLand](https://hackernoon.com/in-simple-terms-css-vs-javascript-abc9d709399d)