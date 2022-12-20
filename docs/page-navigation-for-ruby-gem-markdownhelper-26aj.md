# Ruby Gem markdown_helper 页面导航

> 原文：<https://dev.to/burdettelamar/page-navigation-for-ruby-gem-markdownhelper-26aj>

你可能已经看到了，我放了一个更新的`markdown_helper`宝石。

现在我在想，下一个功能将是“页面导航”

在这个特性中，输入是一系列降价页面。输出是那些相同的页面，但是现在每个页面都有到上一页和下一页的链接。

在我的项目 [RubyTest](https://github.com/BurdetteLamar/RubyTest#rubytest) 中，我有一个这样链接的[测试者](https://github.com/BurdetteLamar/RubyTest/blob/master/examples/github/TesterTour.md#tester-tour)。(它是由 rakefile 构建的，而不是由`markdown_helper`。)

问题是:

*   如何指定页面的系列？

一些可能性:

*   页面通过总览标记页面上的链接来标识(类似于上面的测试者之旅)。

*   页面由非降价页面中列出的 URL 来标识。

*   页面都在一个特定的目录中。还需要某种列表来指定顺序。

我倾向于上面的第一种。

所以，Rubyists，你的乐趣是什么？

*   以上一项(或多项)？
*   还有别的吗？

让我知道。