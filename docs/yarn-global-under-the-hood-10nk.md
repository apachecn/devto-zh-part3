# 引擎盖下的“全球纱线”

> 原文：<https://dev.to/lucifer1004/yarn-global-under-the-hood-10nk>

[![yarn](img/9fc60ef4f58d11e47a4251f8e5b039b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pNMJf6YN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://yarnpkg.com/assets/feature-speed.png)

帮助我们管理全球 npm 包，这对 CLI 工具尤其有用。它是如何工作的？

默认情况下，它会在`~/.config/yarn/global`创建一个目录(在 OSX 和非根 Linux 上)，如果你进入这个目录，你会发现它的结构是这样的:

> node _ modules☆t0]package . JSON□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□

而在`package.json`中，所有的全局包都被定义为`dependencies`，就像你在一个普通的 JS/TS 项目中所熟悉的一样。

在这个目录中，你可以做任何你平常会做的事情，比如`yarn add`、`yarn remove`、`yarn upgrade`、`yarn upgrade-interactive`等。

使`yarn global`与众不同的关键点在于，它通过在您用`--prefix`选项指定的目录中创建符号链接来处理二进制文件。在 OSX 和非根 Linux 上，默认前缀是`/usr/local`，这意味着二进制文件将被符号链接到`/usr/local/bin`，通常在环境变量`$PATH`中。因此，如果您喜欢使用自定义目录作为符号链接，请确保该目录在`$PATH`中。