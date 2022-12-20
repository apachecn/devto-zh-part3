# 实时预览博客编辑

> 原文：<https://dev.to/zeerorg/live-preview-blog-edits-2oh1>

# 实时预览博客编辑内容

*这是继我在[博客](https://blog.zeerorg.site)上的主要项目之后的第二篇文章。*

最初的需求没有考虑实时预览。似乎我错了，实时预览将帮助我看到窗口内的当前变化。这也让我可以灵活地看到自己的变化，让我对自己写的东西更有信心。这些变化中有很多与我在编辑器 markdown 预览中看到的不兼容，因为我在文章中使用了自定义 CSS。

添加实时预览涉及前端和测试后端的变化。我考虑的一个选择是:

**直接上传到 Azure 存储。**这太好了，因为我不必在后端做太多改变，并且可以利用我到目前为止已经建立的东西。但是生活并不容易，上传到后端需要一些时间，azure blob 触发器可能需要 10 分钟才能运行😲！！这个解决方案显然是不可行的，我不能轻易逃脱这种折磨。

## 测试后端

我不情愿地从构建后端开始。好的一面是，设计一个简单的博客架构使这个过程变得更加容易。如果我能找到一种方法来提供当前的草稿作为前端的输入，我就可以在我的本地机器上预览修改。这被证明是一个相对容易解决的问题。

对于后端，我运行一个基于 HTTP 的文件服务器来模拟我的 blob 存储使用。我使用基于 nodejs 的 [http-server](https://www.npmjs.com/package/http-server) 。

对于前端，我检查`process.env.NODE_ENV`环境是否设置为`"development"`并指向本地服务器。

## 楼盘降价促销

我的 azure 函数后端使用 [Markdig](https://github.com/lunet-io/markdig) 将 markdown 编译成 HTML。我有一个几天前的测试项目，它使用 Markdig 编译 markdown，我把它捡起来重新用于构建变更时的 markdown 文件。我实现了一个简单的文件监视器，并编译了更改为 markdown 的文件。我会把代码连接起来。

## 我学到了什么

C# [FileSystemWatcher](https://docs.microsoft.com/en-us/dotnet/api/system.io.filesystemwatcher?view=netcore-2.1) 很容易实现。我还学习了 React 中的`process.env.NODE_ENV`变量。

## 代码链接

*   [博文](https://github.com/zeerorg/BlogPosts/tree/master/src)
*   [降价文件监视器和编译器](https://github.com/zeerorg/BlogPosts/tree/master/MarkdownCheck)
*   [反应前端](https://github.com/zeerorg/BlogSite)