# 宣告勇气:一个带有静态站点生成器的博客降价编辑器

> 原文：<https://dev.to/kahlil/announcing-grit-a-markdown-editor-for-blogging-with-a-static-site-generator-52il>

去年我决定写更多。我想我会尝试托管解决方案，因为我想专注于写作，而不是摆弄网站。

对我来说，微博是最有吸引力的解决方案，但在使用了一段时间后，我意识到，作为一名开发人员，我不能把博客的建设工作交给别人。有太多我想调整/改变的东西，我无法忍受的错误等等。

所以我最终坚持了我的雨果解决方案。

不幸的是，当使用静态站点生成器写博客时，有许多小麻烦，因为你的帖子是文件夹中的一堆降价文件，它们有特定的文件名和一些 frontmatter 样板文件。

写一篇博客文章总是很乏味。在你开始写作之前，有太多琐碎的事情要做。

为了弥补这一点，我创建了一个 [blog-cli](https://github.com/kahlil/blog-cli) ,专门为我提供新的博客帖子。它运行良好，但我真的想要一个编辑器，可以为我做到这一点。

这就是我建造[砂砾](https://github.com/kahlil/grit)的原因。这是一个小的电子应用程序，可以让你管理一个装满降价文章的文件夹，还可以编辑它们。

它允许你存储你的 Markdown 文件的路径，过滤文件，创建一个新文件，编辑文件并通过 Git 发布。

我用 [Preact](https://preactjs.com/) 和 [htm](https://github.com/developit/htm) 来编写它，因为我懒得设置构建步骤，而且我喜欢编写浏览器可以直接解释的代码。对于 Grit 中的文件编辑部分，我使用的是 [CodeMirror](https://codemirror.net/) ，这是一个很棒的产品，哇。

如果你使用 Hugo，Jekyll，Gatsby 或者其他静态站点生成器来创建你的博客，并且正在寻找一点方便，试试吧！参见 GitHub 上的当前功能列表[。](https://github.com/kahlil/grit)

这是阿尔法版本，所以会有龙。但是我正在计划一些改进，比如:拖放图像，可配置的 frontmatter 和一些关于文件创建的增强。

如果你愿意，让我知道你在 Twitter 上的想法。