# 当有疑问时，逆向工程它

> 原文：<https://dev.to/dejavo/when-in-doubt-reverse-engineer-it-1jdk>

[![Reflection ([Green Tree Pixabay](https://www.pexels.com/photo/background-beautiful-blossom-calm-waters-268533/))](img/bc994015329486076abf985478422be5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7mU81MPF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2560/1%2Aw6OYzxPULbPJh7OPaqBIEQ.jpeg)

 *原载于[中](https://medium.com/@dvirsegal/when-in-doubt-reverse-engineer-it-4ad4c4a707e2)于<time title="Thusday, May 2, 2019, 06:36:58 PM">2019 年 5 月 02 日</time>* 

有时候，您会遇到这样的情况:您的应用程序出现了问题，而您无法访问源代码和/或符号。

此时，你更好的选择之一就是反编译你的代码并调试它。IMHO，最好的免费工具之一是由 0xd4d 开发的 [**dnSpy**](https://github.com/0xd4d/dnSpy) 。

根据他的说法，dnSpy 是一个改进的 [ILSpy](https://github.com/icsharpcode/ILSpy) 反编译器，由 Roslyn (C# / Visual Basic)编译器和许多其他开源库增强。此工具支持编辑和调试程序集(任何。dll 或。exe 文件)即使你没有任何可用的源代码，这不是很神奇吗？

#### **快速设置**

前往[https://github.com/0xd4d/dnSpy/releases](https://github.com/0xd4d/dnSpy/releases)，根据您的需求下载一个最新版本，无论是。网芯，。NET 框架或 Unity 调试。

确保安装了 Microsoft Visual C++可再发行软件，如果没有，您可以在[https://support . Microsoft . com/en-us/help/2977003/the-latest-supported-Visual-C-downloads](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)找到。

除此之外，每个版本都依赖于不同的框架，所以一定要安装它们。

注意，开发者提到 Windows 7:必须安装 [KB2999226](https://www.microsoft.com/en-us/download/details.aspx?id=49077) 和 [KB2533623](https://www.microsoft.com/en-us/download/details.aspx?id=26767) 。

#### 保持简单，愚蠢

使用 dnSpy 相当容易。与使用 Visual Studio 的体验相同。它确实感觉到开发者付出了努力。

指向程序集路径，在需要的地方插入断点，就可以开始调试程序集了。

另一种选择是附加到正在运行的进程，类似于 Visual Studio experience。只需在 X86 或 x64 架构中启动 dnSpy，具体取决于程序集。

在下面的视频中，你可以看到我之前提到的 Visual Studio 体验。我编写了一个简单的应用程序，向一个 *StringBuilder 添加一个数字和一个新行。* dnSpy 允许我进入程序集(它们在运行时自我解密)并逐行调试。本地，手表，汽车窗口也支持。

[https://www.youtube.com/embed/qhVWXmrPQ5Y](https://www.youtube.com/embed/qhVWXmrPQ5Y)

支持更多的特性，例如编辑程序集的能力，这样你就可以编辑当前程序集的元数据、方法和类。

[![[Edit current method](https://github.com/0xd4d/dnSpy/raw/mastimg/edit-code-animated.gif)](img/71970d5835c243eafe9a061f9dc7c087.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G9ceYy2k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AZVMFlxRzlrvJHPGFRsE2qg.gif)

您甚至可以添加新的方法、类或成员(在 C#或 Visual Basic 中)。

#### 给我更多…

[![](img/8e514160d9aa04a0c1d2b89f3d78be09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zdpMaChC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AIuFAQrkC7YZ4d7VYUKYyKw.png)

组装编辑器支持使用十六进制编辑器对低级 IL 方法体和元数据表进行更改。你可以在反编译代码和 IL 之间来回切换，你所要做的就是点击反编译代码中的一个地址，然后你就可以在 IL 方法体中或者反过来按 F12 键回到反编译代码中。

此外，dnSpy 可以像在 Visual Studio 中一样搜索方法、程序集和字符串。查找所有类和方法的用法、调用者等等。

支持基本的 IntelliSense 实现，甚至 C#交互窗口也可用。

更多信息和高级主题，请前往 [Wiki](https://github.com/0xd4d/dnSpy/wiki) 页面，其中 0xd4d 描述了如何添加扩展、自行构建 dnSpy 和调试 Unity 游戏。

dnSpy 得到了长相；您可以选择蓝色、浅色和深色主题(以及深色高对比度主题)。该工具被翻译成十种不同的语言，这对于一个开源来说是相当可观的；这确实证明了社区的力量。同样，如果你想投稿并将 dnSpy 翻译成你母语，一个专门的 crowdin 项目正在[https://crowdin.com/project/dnspy](https://crowdin.com/project/dnspy)进行。

[![[https://sayingimages.com/wp-content/uploads/You-Got-It-Dude-meme.jpg](https://sayingimages.com/wp-content/uploads/You-Got-It-Dude-meme.jpg)](img/e337943b2812c8622bd867d76afd9a6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--31oNrzNQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AkZyl8SUZF3JHcueH)

总而言之，尽管有其他优秀的替代产品(如 [dotPeek](https://www.jetbrains.com/decompiler/) ， [JustDecompile](https://www.telerik.com/products/decompiler.aspx) )，dnSpy 是一个开源的便捷工具，对于熟悉领先 ide 的任何人来说，它都具有直观的用户体验。在我看来，dnSpy 是任何软件工程师工具箱中的**必备**。