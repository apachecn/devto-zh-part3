# 即使在今天，每行 80 个字符也是一个值得坚持的标准

> 原文：<https://dev.to/nickjj/80-characters-per-line-is-a-standard-worth-sticking-to-even-today-26kk>

**本文最初发布于 2018 年 6 月 5 日:[https://nickjanetakis . com/blog/80-characters-per-line-is-a-standard-worth-stick-to-even-today](https://nickjanetakis.com/blog/80-characters-per-line-is-a-standard-worth-sticking-to-even-today)**

* * *

人们很容易想到*“天啊，我有一个超大的显示器，为什么我要把自己局限于 1928 年为[穿孔卡](https://softwareengineering.stackexchange.com/a/148729)或 20 世纪 70 年代末的[终端创建的标准？”](https://en.wikipedia.org/wiki/VT100)*

当时，由于硬件的限制，它是有意义的，但是我确信它也很好地与程序的开发方式保持一致。

我当时没有编程，所以我不能肯定地说这一点，但我猜想当时编写的大多数程序都非常专注于一次编辑一个“东西”/文件。

## 当今网络发展

想想你现在通常是如何开发一个 web 应用程序的。你在代码编辑器中打开你的应用程序([我真的很喜欢 VSCode btw](https://nickjanetakis.com/blog/switching-to-vscode-from-sublime-text) )，当你修改你的代码时，你经常在浏览器中查看结果。

我知道这取决于你编程的内容和你喜欢的风格。例如，有些人喜欢 TDD 风格的方法，所以他们会在编写测试的同时编写代码来通过这些测试。

就我个人而言，我不喜欢 TDD 方法，但这不是本文要讨论的。这篇文章是关于在做典型的 web 开发工作时如何编辑代码的模式。

**总体模式是我们非常专注于同时查看和编辑多个文件**。通常这至少是 2 个文件，但在某些情况下能够一次查看 3 个文件是非常有用的。除此之外，我们还有一个浏览器和文档要看。

#### 上下文切换烂透了

我不知道你是怎么想的，但是当我不需要经常做微操作，比如在代码编辑器中切换两个文件时，我会工作得更好。

首先，它让我感到幽闭恐怖，但它也让我无法集中注意力。我发现如果我能一次看到我正在做的所有事情，推理起来会容易得多。

这也是我在白板和纸上计划事情的部分原因，因为我不被单一的屏幕所束缚。一次看到整件事有一种非常特别的感觉，让一切都变得清晰起来。对你来说也一样吗？

即使在两个文件之间切换只需要 500 毫秒，但不能同时看到两个文件对我保留信息的能力有巨大的负面影响。

## 使用 80 字行的好处

有相当多的额外津贴，所以让我们去吧。

#### 1080p 显示器

1080p 仍然是显示器最受欢迎的分辨率之一，碰巧的是，对于大多数代码编辑器来说，你可以轻松地并排放置两个 80 个字符的代码窗口，如果你喜欢这种东西，甚至可以有侧边栏的空间。

##### 在 1080p 显示器上并排显示 2 个代码窗口，每行 80 个字符:

[![2 80 char code windows 1080p](img/c8b281139b64cb19a89d57b7a02dd75d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---QmeXVPD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickjanetakis.com/assets/blog/1080p-80-characters-2-windows-15px-63a84727eb64ae7208699728a54cbf15a66f089f1d283dfb33dfa13b07e0c378.jpg)

您在编辑器窗格中看到的灰色垂直线位于 80 个字符处。我用它们作为向导，一目了然地看每行有多少个字符。

一些业内人士希望将标准提高到 120 甚至 132 个字符，但是看看如果我们将每行字符提高到 120 个，我们的双代码窗口布局会发生什么。

##### 1080 p 显示器上并排的 2 个代码窗口，每行 120 个字符:

[![2 120 char code windows 1080p nope](img/3a7fe39311e2a288cca6a2eafe72614c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L3J0FthH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickjanetakis.com/assets/blog/1080p-120-characters-2-windows-15px-22a0be3eac0b8df3aead904e13a3dbdcc7dbdddbe4471f4560a44bd5bdd38bd2.jpg)

120 个字符不能并排放 2 个窗口，即使去掉侧边栏，还是放不下。你最终切断了第二个窗口的大部分。

不得不水平滚动来阅读一整行也是不可接受的。

现在，你可以把字体变小，但是为了让 2x 120 个字符的窗口并排显示，你需要使用 10 像素的字体。

##### 微小字体大小，适合并排的 2 个代码窗口，每行 120 个字符:

[![2 120 char code windows 1080p yep](img/b86120cf7e8b9eef43b8021245cf8e1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EZnFLus9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickjanetakis.com/assets/blog/1080p-120-characters-2-windows-10px-3e16e8f8fa96ce0718687b2e723b82d960f8e8b90a58c1146287a6c6a52b5620.jpg)

即使是《指环王》里的勒苟拉斯也不会同意全职使用这种字体大小。

#### 1440p 显示器

这就是事情变得有趣的地方。

我个人使用 1:1 缩放比例的 2560x1440 显示器，我写道这是我在过去 5 年中进行的[最佳开发环境升级。](https://nickjanetakis.com/blog/how-to-pick-a-good-monitor-for-software-development)

这个设置很酷的一点是，你可以并排放置 3x 80 个字符的代码窗口，甚至可以使用侧边栏。这是一个舒适的 15px 的字体大小。它和上面显示的 1080p 80 字符双窗口的字体大小一样。

##### 在 1440p 显示器上并排显示 3 个代码窗口，每行 80 个字符:

[![3 80 char code windows 1440p](img/b9d6a414d3e03d5448e003f46e14855f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T9p5Hoh6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickjanetakis.com/assets/blog/1440p-80-characters-2-windows-15px-7b559274e488a38cbc878040ec0493d8fba7dff15c11c38ef5cf337fb4650770.jpg)

能够在一个视图中看到这些文件对于日常的 web 开发非常有用。或者，如果我在做操作工作，我倾向于打开两个代码窗口，并在第三个代码窗口通常所在的位置显示一个终端。

如果您以 1:1 的比例运行 2560x1440，您可以轻松地并排容纳 2x 120 个字符行，但 3 甚至还不够。我甚至懒得展示试图将它们并排放在一起的图片。字体小得可笑。

顺便说一句，如果你去掉侧边栏，你也可以在 132 个字符中放入 2 个代码窗口。

#### 读取代码

让我们忽略一次查看多个文件，只关注阅读代码。

我不知道你怎么想，但我发现当代码行不是很长时，阅读代码要容易得多。我越向右看，就越难读。

我想我并不孤单，因为想想报纸和大多数现代网站是如何定位文本的。作为人类，当句子长度较短时，我们可以更有效地消化文本。代码行长度没有什么不同。

甚至当我在 markdown 中写这些博客文章时，我也在 80 个字符处使用硬换行，因为这样会使文本更容易阅读。这也意味着我可以把这个代码窗口和浏览器并排放在一起，并在我写的时候获得即时反馈。

##### 用浏览器并排书写 80 个字符的降价:

[![markdown writing side by side](img/7e8e0f8898e2548e7a77d9d85705c5e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pldfxucE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickjanetakis.com/assets/blog/1440p-80-characters-live-preview-b2c87aa83137ab662955a41027f41dfea226b09f1a08228512364237977ceea0.jpg)

从技术上讲，120 个字符也可以，但这意味着缩小你的浏览器。能够看到网站“大”版本的实时预览是很重要的。

此外，不要忘记我在这里使用的是 1440p 显示器，它比 1080p 显示器有更多的水平空间。1080p 即使 80 字也很局促。

#### 代码林挺和格式化工具

我提出这一点是因为你可能认为总是集中精力把你的行限制在 80 个字符是超级烦人的，但这真的不坏。

许多语言都有内置的支持或第三方包来帮助你限制代码长度。这适用于您决定限制代码长度的任何行。

这样你甚至不需要考虑自己限制线路。我仍然出于习惯这样做，但是我总是用一个热键就能让一个工具为我自动格式化代码。

我过去讨厌自动格式化，但现在我喜欢它，因为我不再花 5 分钟时间试图将一行代码调整到 80 个字符，而是让语言作者或社区(代码标准规则)用自动化工具为我完成。

#### 对视频进行放大的方式

这可能不会影响你的日常生活，但如果你像我一样记录编码视频，那么这是一个相当大的胜利。

在 1080p 下放大到 80 个字符适合你整个屏幕的程度是非常好的。这将确保字体足够大，以便较小的设备可以阅读它，更重要的是，人们将能够阅读它，而不必全屏显示您的视频。

**你是每行 80 字的粉丝还是别的？下面让我知道！**