# 宣布 PHP 学徒！

> 原文：<https://dev.to/restoreddev/announcing-php-apprentice-coc>

我很兴奋终于宣布 [PHP 学徒](https://phpapprentice.com)！这是一本学习 PHP 编程语言的在线免费开源书籍。这本书是我花了六个月时间做的一个兼职项目。我的目标是提供一个学习 PHP 基础知识的当代资源，重点是高质量的代码。PHP 已经有 25 年的历史了，从诞生到现在已经发生了很大的变化。许多用于编写优秀 PHP 的资源已经过时，或者在今天已经不适用。我写了 PHP 学徒来弥补学习 PHP 可用网站的缺乏。

目前，PHP 学徒有关于该语言的基本构建模块的章节，但在未来，我将添加关于生成网页、连接到数据库和使用 Composer 和自动加载等高级技术的章节。我正在寻找反馈，因此如果您有任何想法或请求，请在此留下评论或在[存储库](https://github.com/restoreddev/phpapprentice)上留下问题。

《PHP 学徒》的每一章都是作为一个减价文件编写的。然后我用 PHP 构建了一个 HTML 生成器，它获取每个文件并用 [Parsedown](https://parsedown.org/) 解析成一个 HTML 块，然后用一个模板将它们加载到一个完整的 HTML 页面中。然后这些页面由 GitHub 页面提供服务。构建过程可以使用 CLI 命令来执行，所以我使用 [Travis CI](https://travis-ci.com/) 来自动化部署。每当有任何代码被合并到`master`中，Travis 就会把它捡起来，重新构建所有网页，并把它们推送到 GitHub Pages 使用的存储库的另一个分支。

前端都是纯 HTML，CSS，JavaScript。我使用了 [PostCSS](https://postcss.org/) 来添加文件导入和变量。代码高亮显示由 [Prism.js](https://prismjs.com/) 执行。我希望每个页面的焦点都在代码上，所以样式非常简单。该网站响应迅速，但仍有改善移动体验的空间。我把重点放在桌面上，因为我建议在阅读这本书的同时在章节中编写代码。

PHP 学徒的灵感来自于[长生不老药学校](https://elixirschool.com)和[以身作则](https://gobyexample.com/)，这两个网站的清晰度和质量令我钦佩。我希望 PHP 学徒能成为一个对 PHP 生态系统有类似帮助的网站。这是一本活书，所以我会随着时间的推移改变和改进网站。

请查看 [PHP 学徒](https://phpapprentice.com/)，让我知道你的想法！