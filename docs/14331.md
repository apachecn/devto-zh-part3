# 榆树工具

> 原文：<https://dev.to/zwilias/elm-tools-571a>

Elm，除了是一种拥有漂亮的软件包生态系统的令人敬畏的语言之外，还有一个令人难以置信的工具生态系统。在这篇文章中，我列出了几个我最喜欢的，并无耻地插入了几个我自己的。

# [`elm-format`T4】](https://github.com/avh4/elm-format/blob/master/README.md)

> `npm i elm-format`

这是 Elm 生态系统中最著名的工具之一，无需介绍。

快速概述:

*   所有 Elm 代码的零配置格式
*   与流行编辑器的集成
*   可以使用`--validate`标志在 CI 中使用

# [`elm-test`T4】](https://github.com/elm-explorations/test/blob/master/Readme.md)

> `npm i elm-test`

`elm-test`是测试 Elm 代码的一站式商店。对于普通的老单元测试，使用[模糊测试](https://package.elm-lang.org/packages/elm-explorations/test/latest/Fuzz)的基于属性的测试，以及结合一些选择器使用 [`Test.Html.Query`](https://package.elm-lang.org/packages/elm-explorations/test/latest/Test-Html-Query) 检查生成的 HTML，它可以实现一些真正强大的测试场景。

使用`elm-test`的实际方式是通过基于节点的测试运行程序。

值得注意的是:它在 [travis](https://blog.travis-ci.com/2018-11-20-elm-is-now-supported-on-travis-ci) 上也得到了支持！

# [`elm-verify-examples`T4】](https://github.com/stoeffel/elm-verify-examples/blob/master/Readme.md)

> `npm i elm-verify-examples`

在为你的包编写文档时，非常有用，`elm-verify-examples`允许运行你的例子并验证它们的正确性。

如果您正在发布包，这应该是您的 CI 设置的一部分！

# [`elm-doc-preview`T4】](https://github.com/dmy/elm-doc-preview/blob/master/README.md)

> `npm i elm-doc-preview`

当有一种很好的方式来预览包的实际外观时，为包编写文档就容易多了。这就是`elm-doc-preview`的用武之地！

`elm-doc-preview`可以在本地使用，但也可以作为托管版本存在，可以预览 GitHub 存储库的文档，只要提交一个`README.md`和`docs.json`。

# [`elm-doc`T4】](https://github.com/zwilias/elm-doc)

> `npm i elm-doc`

沿着同样的思路，但是更适合于编写应用程序，`elm-doc`允许在一个单独的位置，完全离线地查看所有依赖关系的文档。

*免责声明:这是我做的*

# [`elm-analyse`T4】](https://github.com/stil4m/elm-analyse/blob/master/README.md)

> `npm i elm-analyse`

分析你的 Elm 代码，可以帮助识别一系列(潜在的)问题，同时允许快速修复一系列最佳实践。非常酷的工具！

# [`elm-xref`T4】](https://github.com/zwilias/elm-xref/blob/master/README.md)

> `npm i elm-xref`

受 Erlang/OTP 的[http://erlang.org/doc/apps/tools/xref_chapter.html](https://dev.toXref)的启发，`elm-xref`是一个交叉引用工具。

这实质上意味着它可以对您的代码库进行静态分析，识别未使用的函数(私有的和公开的)，或者查找所有使用给定函数的地方。

*免责声明:这也是我做的*

# [`elm-dependencies-analyzer`T4】](https://www.markuslaire.com/github/elm-dependencies-analyzer/)

一个分析 elm.json 的在线工具，可以告诉您哪些依赖项有可用的更新，显示您的依赖项的可用版本之间的潜在不兼容性，等等。

如果你曾经想知道为什么不能更新`elm/http`，这个工具可以告诉你为什么！

# [`@dillonkearns/elm-graphql`T4】](https://github.com/dillonkearns/elm-graphql/blob/master/README.md)

如果您将 GraphQL 与 Elm 一起使用(或者想要将 GraphQL 与 Elm 一起使用)，这个包/工具组合允许基于模式为您的 GraphQL 后端生成一个类型安全的接口。

很棒的文档，非常好的 API，总的来说，绝对值得一试！

* * *

我错过了你喜欢用的工具吗？欢迎在评论中指出！