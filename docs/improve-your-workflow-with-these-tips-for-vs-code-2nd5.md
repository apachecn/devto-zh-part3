# 用这些 VS 代码的技巧改进你的工作流程

> 原文：<https://dev.to/jsgoose/improve-your-workflow-with-these-tips-for-vs-code-2nd5>

<figure>[![](img/25253c0af65ba4565c974b26819b3204.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uD5mz8wZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonathansexton.me/blog/wp-content/uploads/2019/05/denny-muller-1260091-unsplash-1024x683.jpg) 

<figcaption>
照片由[丹尼米勒](https://unsplash.com/@redaquamedia?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

当我开始自学编码时，我需要做的第一件事就是决定使用哪种文本编辑器。

没有进入太多的细节，在一些彻底的测试后，我选择了微软的 [VS 代码](https://code.visualstudio.com/)。(补充说明——这不是一个关于我对哪个文本编辑器是“最好的”的看法的帖子，所以如果那是你正在寻找的，那你来错地方了。坦白说，最好的是自己喜欢的，最符合自己需求的。).

在使用这个文本编辑器几年后，我整理了一份清单，上面列出了一些真正帮助我简化工作流程的技巧。这些范围从化妆品到小有用的提示，但也有游戏改变者，将需要一点额外的工作。

让我们开始吧——有一个小小的警告:我在 Windows 机器上工作，所以以下所有内容都与该操作系统相关。我向 Mac 用户道歉，因为我不知道那个平台。

好了，现在让我们开始吧！

## 学习和使用键盘快捷键

这是迄今为止我给几乎所有软件用户的最有效的节省时间的建议之一。设置键盘快捷键是为了帮助“超级用户”充分利用他们的软件体验。你会在游戏、网络应用、桌面应用等中找到快捷方式，值得你花时间去学习和使用它们。

这是最难实施的建议之一，因为它需要你重新连接你的肌肉记忆。如果你已经使用一个应用程序一段时间了，你不用想就知道它是什么感觉。学习使用键盘快捷键需要你在执行操作之前思考一下。

老实说，在第一周左右，你会感觉有点陌生，但是一旦你掌握了它，你就会以更快的速度完成动作。

我在 VS 代码中最常用的一些动作是:

*   创建一个具有 class/id 的 HTML 元素->***div . container+tab**=<div class = ' container></div>或***div # container+tab**=<div id = ' container '></div>
*   Ctrl + Shift + E - >显示文件浏览器
*   Ctrl + Shift + G - >显示源代码控制(查看如何将 Git 集成到下面的 VS 代码中)
*   Ctrl + B - >显示/切换侧边栏
*   Ctrl +`(颚化符) - >显示集成终端
*   Ctrl + PageDown - >向下移动页面
*   Ctrl + PageUp - >向上翻页
*   Ctrl + F - >搜索当前文档
*   Ctrl + P - >转到文档
*   Ctrl +，(逗号) - >首选项

这些只是我日常使用的一些快捷方式。如需完整列表，请查看微软官方 PDF 版本的[键盘快捷键](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf)。

## 集成源代码控制

[![](img/92b4379a47a49ec45e609d0169d601de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MBPQZREW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonathansexton.me/blog/wp-content/uploads/2019/05/image-1.png)

我听到了双方不同的观点，但对我来说，将源代码管理集成到 VS 代码中是我对工作流程所做的最简单也是最有效的改变之一。在集成之前，我大约每隔 3-5 分钟就要在两个窗口之间切换一次，中间浪费了一些时间。

现在，这看起来没什么大不了的(特别是如果您有多个监视器，并且可以同时保留命令行和 VS 代码),但是将它放在 VS 代码中被证明是非常宝贵的。

我不再需要在命令行中使用 ***Alt + Tab*** 来键入提交消息，然后提交。就这么简单，按下 ***Ctrl + Shift + G*** ，输入我的消息，再按下 ***Ctrl + Enter*** 然后我又回到编码。

查看关于将源代码控制集成到 VS 代码中的官方文档。一旦你完成了集成，还有一个关于[使用源代码控制](https://code.visualstudio.com/docs/introvideos/versioncontrol)(特别是 Git)的视频。

长话短说——将 Git 集成到我的 VS 代码中让我的生活变得更轻松，我的工作流程甚至更好！

## 使用集成终端

说到集成，除了版本控制，我们还可以将终端引入 VS 代码。它内置了对 [Git Bash](https://gitforwindows.org/) 的支持，但也有一些扩展允许你使用 [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/components/vscode/using-vscode?view=powershell-6) 。

将终端放在与代码相同的空间内不仅很好，还允许您拥有多个终端标签。我经常打开两三个终端标签，特别是当我运行一堆需要保持活跃的 [Gulp](https://gulpjs.com/) 任务时，但我也需要放入一些 Git 命令。*边注——如果你发现自己一直在使用多个终端，你可以设置快捷键来切换标签。*

当然，如果你更喜欢使用外部终端，可以通过键盘快捷键***【Ctrl+Shift+C****(这只是学习/使用这些键盘快捷键发挥作用的另一个例子:D)* 。使用此命令会将命令行打开到集成终端所在的目录。

吸引我使用集成终端的最后一个有趣的地方是，你可以在屏幕大小允许的情况下，将终端分成尽可能多的标签。

下面你可以看到我把我的终端分成了 5 个标签，然后当我没有更多空间的时候弹出了一个错误。

<figure>[![](img/bc0c42511b099d4c6564339e832761e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EqBFZl6r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonathansexton.me/blog/wp-content/uploads/2019/05/terminal-split.png) 

<figcaption>将集成的终端拆分成多个选项卡，每个选项卡都有自己的实例</figcaption>

</figure>

## 利用扩展

VS 代码周围有一个充满活力的扩展社区。如果你能想到一个你喜欢的特性，很有可能会有一个扩展或者它会出现在未来的版本中。

扩展增加了额外的功能以及一些定制选项，这些都不是 VS 代码“现成的”。这种功能包括代码片段，或者通过像林挺这样的工具让你的开发者生活变得更容易。

这些扩展中的许多通过添加一些功能使我更有效率，比如只需输入 **html+tab** 就可以给我 HTML 样板文件，或者允许我用不同的标志/颜色突出我的评论以引起注意。

我最喜欢的，也是最常用的扩展之一是[更漂亮的](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)。它允许我通过修改缩进或在 JavaScript 末尾添加分号来将代码格式化为易读的格式。(需要注意的是，我在这里站在“语法”的角度，说这是我喜欢的。如果你喜欢另一种方式，那很好，也没有错——但对我来说，这是最有效的方式。)

此外，如果你想浪费几个小时的时间，只需查看 VS 代码可用的[主题](https://marketplace.visualstudio.com/search?target=VSCode&category=Themes&sortBy=Downloads)。有编辑器主题和文件图标主题。如果你像我一样，那么你会安装尽可能多的，并不断在他们之间切换:D

* * *

我希望您喜欢这篇关于使用 VS 代码提高生产率的各个方面的文章。如果你有我没有提到的自己的建议/技巧，我希望你能和我分享。

这篇文章最初发表在我的博客上。既然你在那里，为什么不注册我的**时事通讯**？我保证我永远不会给你的收件箱发垃圾邮件，你的信息也不会与任何人/网站共享。我喜欢偶尔发送我发现的有趣资源、关于 web 开发的文章以及我的最新帖子列表。

我也在[媒体](https://medium.com/@joncsexton)上发布文章，这样你也可以在那里找到我的作品！

祝你有一个充满爱、快乐和编码的美好的一天！