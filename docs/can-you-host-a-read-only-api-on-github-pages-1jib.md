# 可以在 github 页面上托管只读 API 吗？

> 原文：<https://dev.to/matmooredev/can-you-host-a-read-only-api-on-github-pages-1jib>

几周前，我和一位同事讨论是否有可能将只读 API 变成完全静态的，即预先生成所有资源，而不是按需生成。

静态 API 可以托管在像 github pages 这样的服务上，因此即使底层数据需要更改，您也不需要太多的维护就可以保持运行。

圣诞节期间，我尝试制作了一个这样的 API。这个 API 可以让你查找公务员(我工作的地方)使用的行话，在这篇博文中，我将分享哪些行之有效，哪些行之无效。[你可以在我的 github](https://github.com/MatMoore/jargon) 上找到所有代码。

# 将数据存储为版本控制的 CSV

API 的源数据存储在 github 上的 CSV 文件中。这非常有用，因为数据很容易管理(你甚至可以将它加载到电子表格中)，而且你可以在 github 上以表格格式查看它，因为 github 将 CSV 呈现为 HTML。

对于开放数据 API，这使得任何人都可以很容易地发现数据何时发生了变化，并向其提交变化。

# 创建宁静路线

[这篇博文](https://paulsalaets.com/posts/json-api-with-github-pages)解释了 github pages 如何将 URL 路由到文件。

基本上你可以创建任何 URL 结构，但是静态 API 的一个大缺点是它不能响应查询字符串参数。这意味着您不能使用查询参数对大型数据集进行分页。

另一种方法是使每个页面成为单独的资源，例如:

*   [https://my-api/pages/1](https://my-api/pages/1)
*   [https://my-api/pages/2](https://my-api/pages/2)

最后，我决定完全避免页面，因为我的数据集没有那么大，而且不得不在客户端重建它会很烦人。所以我的 API 的路径是:

*   [https://matmoore.github.io/jargon/](https://matmoore.github.io/jargon/)(一切)
*   [https://matmoore.github.io/jargon/{ACRONYM}/](https://matmoore.github.io/jargon/%7BACRONYM%7D/)(单次查找)

这些资源是通过运行一个将 CSV 转换成 JSON 的 ruby 脚本生成的。

# 部署

部署到 github 页面非常容易。我决定使用一个[单独的 gh-pages 分支](https://help.github.com/articles/configuring-a-publishing-source-for-github-pages/),因为我想在 CSV 源代码和生成的 JSON 之间保持清晰的分离。

我重新利用了一些来自[middle-GH-pages](https://github.com/edgecase/middleman-gh-pages)的代码，以便轻松地重新生成 API 并发布到这个分支。这是您可以使用 CI 实现自动化的事情。从理论上讲，你可以将整个东西构建到一个 marketplace 应用程序中，而不是像这样将代码与数据捆绑在一起，但这将用于概念验证。

# 供应商锁定

这个解决方案有点太依赖 github 了。特别是，它依赖 github 页面以特定的方式路由 URL，github 控制 API 返回的所有头。

通过使用 github 页面，您接受了他们的[使用限制](https://help.github.com/articles/what-is-github-pages/),这意味着如果流量太大，您的 API 仍然会关闭，他们不建议将其用于商业目的:

> GitHub Pages 不用于或不允许用作运行您的在线业务、电子商务网站或任何其他主要旨在促进商业交易或提供商业软件即服务(SaaS)的网站的免费虚拟主机服务。

然而，我认为生成 API 的脚本可以支持其他平台，如 [Gitlab Pages](https://docs.gitlab.com/ee/user/project/pages/) 。

# 其他限制

API 的静态特性意味着我只能提供精确的查找。这意味着您必须输入大写字母缩写，标点符号与数据集匹配。如果 API 允许模糊匹配并在没有匹配时返回有用的`200`响应而不是`404`就好了。

像这样的静态 API 也不适合任何需要身份验证/访问控制的情况，或者如果您想要应用自己的速率限制。

# 结论

最终，我不会为任何重要的事情推荐这种解决方案，因为它本质上是不灵活的。有些事情你就是做不到，像 github pages 这样的免费服务永远不会像 Heroku 这样的付费平台那样可靠，因为它可以随时关闭，而不会影响付费用户。

然而，对于零预算的个人项目来说，这是一种提供简单 API 的相当简单的方法。我认为 github 页面也可以很好地工作，如果你想快速原型化一个 API，或者提供模拟响应来测试。