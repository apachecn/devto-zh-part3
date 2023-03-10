# 使用 gomvpkg 重构 Go 包

> 原文：<https://dev.to/ballweera/refactor-go-package-by-using-gomvpkg-298a>

<figure>

[![Gopher](img/2939f2dc62cd8e518b27745a65a21766.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rwG7ZFsR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5nla674c0iyppohfafus.jpeg)

<figcaption>https://blog.golang.org/gopher</figcaption>

</figure>

Go 提供了很多工具来帮助 Go 开发者。当 Go 开发者试图用 Go 解决他们的问题时，它们可以帮助他们提高工作效率。你可以在 https://godoc.org/golang.org/x/tools 看到工具列表

* * *

我见过许多从其他技术栈(Java Spring、Ruby on Rails 等)转移过来的开发人员。)使用那些 to Go 项目中以前的项目结构。基于层就是一个例子(控制器、模型、服务、公共、实用程序、存储库等)。).在围棋中，大多数情况下，我们不会那样做。当我们设计一个 Go 包(结构)时，我们考虑的是它提供什么，而不是它包含什么。以及我们如何使用它们。可以看我的博客关于 [Go 包(结构)设计](https://dev.to/ballweera/go-package-structure-design-b0m)

正如你可能知道的，当你试图重构遗留的 Go 包时是很痛苦的。尤其是像 common、util、base 这样的包。因为它一直在项目周围使用(其他包通过导入使用)。如果您重构这些包，您需要付出很大的努力来改变每个派生包中的导入路径。

其实这种情况有一个 Go 工具。是 **gomvpkg** 。【https://godoc.org/golang.org/x/tools/cmd/gomvpkg T2】号

我将向您展示如何使用`gomvpkg`重构遗留的 Go 包。我们有包`common/consumer`。