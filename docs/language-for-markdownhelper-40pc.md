# markdown_helper 的语言？

> 原文：<https://dev.to/burdettelamar/language-for-markdownhelper-40pc>

我最受欢迎的 Ruby gem， [markdown_helper](https://rubygems.org/gems/markdown_helper) (接近 8000 次下载)的缺点是潜在用户必须安装 Ruby。

对于许多 Linux 用户来说，这不太可能是个问题，因为许多 Linux 安装都包含 Ruby。

但是没有安装 Ruby 的 Windows 用户必须安装它。对于一个小公司来说，这是一个相当大的足迹。

所以我在考虑做第二个实现，它将产生一个开箱即用的 Windows 可执行文件。

要为此决定一种语言，我认为有两个考虑因素:

*   构建必须产生单个可执行文件。
*   语言应该是鼓励而不是阻止贡献者的语言。

这将在 GitHub 上(事实上，该实用程序只在 GitHub 项目内部工作)，因此 GitHub 程序员的偏好将很重要。

所以问题是:

*   语言应该是什么？(当然还有，为什么？)

我会感谢你的想法。

哦，请注意，这个实用程序的实际用户不需要知道那种语言。因为它将是一个可执行文件。