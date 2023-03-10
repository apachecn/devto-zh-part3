# 主题开发中的 Css 样式和 Javascripts。

> 原文：<https://dev.to/vanaf1979/wordpress-css-styles-and-javascripts-in-theme-development-in-depthish-k42>

[最初发布于我的网站 2019 年 2 月 21 日](https://since1979.dev/wordpress-css-styles-and-javascripts-in-theme-development-in-depth/)

### 给 WordPress 主题添加 Css 和 Javascript。

在本教程中，我将向你展示在你的 WordPress 主题中包含 Css 样式和 Javascripts 的“正确方法”。如果你以前在没有 WordPress 的情况下创建过网站，你可能习惯于使用 *link href=""* 和 *script src=""* 标签。虽然没有什么可以阻止你将这些标签直接插入到你的主题中，但这被认为是 WordPress 开发中的一个“坏习惯”。

WordPress 通过[*WP _ enqueue _ style()*](https://developer.wordpress.org/reference/functions/wp_enqueue_style/)和[*WP _ enqueue _ script()*](https://developer.wordpress.org/reference/functions/wp_enqueue_script/)函数族有自己特定的处理 Css 样式和 Javascripts 的方式。

用 WordPress 的方式做事情会多写一点代码，但比直接使用标签有一些真正的优势，比如代码的可重用性，基本的依赖管理，以及如果你正确地插入 Css 样式和 Javascripts，插件可以出于各种目的控制它们，比如缩小和连接。

让我们开始吧…

### 给 WordPress 主题添加 Css 样式:

要添加样式表，我们首先需要连接到 WordPress 过程。如果你想知道关于 WordPress hooks 的所有细节，可以看看这篇文章，但是简而言之……Hooks 就像是让一个厨师在烹饪过程的某一点拜访你。比如，让他打电话给你，告诉你什么时候该往汤里加蔬菜了，这样你就可以添加你最喜欢的蔬菜了。在我们的例子中，WordPress 正在制作网页，我们希望在收集样式和脚本(enqueue_scripts)时被调用，这样我们就可以添加自己的样式和脚本。

#### 钩入 WordPress:

将下面的代码添加到你的主题的【functions.php*中*