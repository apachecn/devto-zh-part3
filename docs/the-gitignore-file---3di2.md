# Gitignore 文件

> 原文：<https://dev.to/zellwk/the-gitignore-file---3di2>

[https://www.youtube.com/embed/xlQIABDgTOs](https://www.youtube.com/embed/xlQIABDgTOs)

如果您不想将文件提交到 Git 存储库中，那么最好不要将文件显示在临时区域中。

您可以用 Gitignore 文件做到这一点。

在视频中，我们用 npm 安装了一个名为 Typi 的库。当我们安装 Typi 时，许多文件进入我们的暂存区。

[![Node modules files in staging area](img/9e6ce93d875012f028a30093384e8f23.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aBgE5cha--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/gitignore/node-modules-folder.png)

要防止`node_modules`及其文件出现在临时区域，您需要:

1.  在项目的根目录下创建一个`.gitignore`文件
2.  在 Gitignore 文件中添加`node_modules`

集结地变得干净了。此时，您需要提交您的`.gitignore`文件来保留要忽略的内容。

[![Node modules removed from staging area](img/4556a0fa54f3a1354c7ed3d2d689f5b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AXxI4Ezd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/gitignore/clean-staging-area.png)

## 如何使用 Gitignore

Gitignore 文件中的每一行都可以用来匹配您不想在 staging 区域看到的文件和文件夹。

1.  要忽略一个文件，你写文件名。
2.  要忽略一个文件夹，您可以写下文件夹名称
3.  要忽略扩展名，可以使用`*`通配符，比如`*.log`

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。