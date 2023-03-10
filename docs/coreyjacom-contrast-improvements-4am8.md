# coreyja.com 对比度提高

> 原文：<https://dev.to/coreyja/coreyjacom-contrast-improvements-4am8>

*此文最初发布于[coreyja.com](https://coreyja.com/coreyja-dot-com-contrast-improvements/)T3】*

我在 Reddit 上发布了我的最后一篇博客，我得到的一个回复是关于我的博客如何由于我的颜色和背景之间的低对比度而难以阅读。大声喊出来 [@Narishma 指出这一点](https://www.reddit.com/r/rust/comments/9yek2s/using_rust_faster_devicon_file_preview_with_fzf/ea3xhpb/)！

## 自动化测试

我发现了这个网站，它会检查你网站上文字的对比度，并告诉你你做得怎么样，[https://color.a11y.com/Contrast/](https://color.a11y.com/Contrast/)。我在我的博客上运行了这个，我在文本中使用的每种颜色都没有通过对比色检查。

[![Panic](img/05ba8869c24ef31518854ef7c95a3c5a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QYMMlb7I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://coreyja.cimg/posts/coreyja-dot-com-contrast-improvements/panic-cd8d2338.gif)

## 铬债务工具

事实证明，谷歌 Chrome 有一些非常好的工具，可以更容易地调整我的配色方案。如果您正在查看字体颜色，您可以打开颜色选择器，查看更多对比度信息。这里有一个截图显示了我的博客的新字体颜色。

[![Devtool Screenshot](img/6cd812389c13e965acbffaff6bc6e3fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fCtfQfJV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://coreyja.cimg/posts/coreyja-dot-com-contrast-improvements/devtools-screenshot-cb361cb4.png)

可以看到现在的对比度是 7.23。`AA`等级是较低的可接受值，而`AAA`是理想值。我试图确保我所有的颜色都在`AAA`类别之上。使用当前的背景颜色和字体大小，`AA`值是 4.5，`AAA`值是 7.0。在比色图表上，你可以看到一条覆盖在颜色上的白线。这代表`AAA`对比度，如果您在该曲线下选择任何颜色，对比度将大于或等于 7.0。

Chrome 还有一个检查对比度的内置工具，我在为我的博客尝试不同的颜色时广泛使用了这个工具。要访问它，请转到 Devtools 中的`Audits`选项卡，并选择`Accessibility`审计。这项审计要做的事情之一是检查字体颜色的对比度。

## Font Colors

我把正文字体颜色和标题字体颜色都做成深灰色。标题颜色以前是`#bcbcbc`，现在是`#767676`。字体颜色以前是`#9a9a9a`，现在是`#575757`。这里有前后的照片来展示不同之处。

[![Text Colors Before](img/0b029fb8f1d6da7f781effda514bd8c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sHP01BLL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://coreyja.cimg/posts/coreyja-dot-com-contrast-improvements/text-colors-before-e9b7d521.png) 之前

[![Text Colors After](img/dfe5ea66fc7b634059e24ff266835fc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kUYWuAeN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://coreyja.cimg/posts/coreyja-dot-com-contrast-improvements/text-colors-after-a844a81d.png) 后

## 代码样本

我改变的另一件大事是代码示例样式。我用了一些我的博客的强调色来突出语法，而我用的大多数柔和的颜色在浅色背景下对比度不够高。实际上，我更喜欢深色背景的代码，所以这似乎是一个改变它的好时机！我选择了一种接近黑色的灰色，但不是完美的黑色，作为代码样本的背景，我最终选择了`#1a1a1a`。然后我把代码涂成白色，把一些注释涂成灰色。在这之后，我所有的彩色粉笔都有了很好的对比，所有的代码都更容易阅读。再次这里有一些前后截图！

[![Code Colors Before](img/6a610a81525e43de59a826b6235950c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h42UF0RZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://coreyja.cimg/posts/coreyja-dot-com-contrast-improvements/code-colors-before-745112fa.png) 之前

[![Code Colors After](img/3887878106cc4ff4df895049c1c9924c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NNoKIOTI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://coreyja.cimg/posts/coreyja-dot-com-contrast-improvements/code-colors-after-b13153b0.png) 后

## 工作上的事情

虽然总的来说，我认为这是一个巨大的进步。但是还有几件事我想继续努力。其中最大的一个是我在文章标题上使用的颜色。这是我目前的一种强调色。我很喜欢我的口音颜色，所以我不想改变它们。然而，我不认为它们是最好的字体颜色。因此，我想围绕我的博客文章做一些设计上的改变，这样我可以用灰色作为标题颜色，同时在其他地方使用我的强调色。