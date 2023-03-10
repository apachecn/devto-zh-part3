# tflookup -开发者日记第 5 部分

> 原文：<https://dev.to/dploeger/tflookup-developer-diary-part-5-4e95>

亲爱的日记！

我的查找应用程序有效！耶！

至少在当地是这样。

但是一个 web app 有什么用，如果只能自己享受的话？

所以我需要一种方法来自动部署我的应用程序(而且是免费的)。

幸运的是，Heroku 就在附近。Heroku 是一个简单的应用程序托管提供商，免费托管非常简单的应用程序。他们连接到你的 [github 库](https://github.com/dploeger/tflookup)并部署那里的应用程序。

很简单。

**错误**

我想，大多数应用程序都是如此，但我遇到了一些困难:

*   在启动应用程序之前，我有一个前端和一个后端要编译
*   在启动应用程序之前，我必须下载预渲染索引

Heroku 的文档也相当广泛。然而，我无法理解他们的发行阶段。我所有配置版本的尝试都没有成功。

所以最后，我简单地将我的完整应用程序包装到一个 [docker 容器](https://github.com/dploeger/tflookup/blob/master/Dockerfile)(尽管如此，这还是不错的)中，并使用 Herokus [容器部署特性](https://devcenter.heroku.com/categories/deploying-with-docker)将最终应用程序部署到 Heroku。

现在，我亲爱的日记，我不得不结束我的写作。我终于完成了我的查找应用程序。它仍然没有光泽或稳定，但它的工作，我很高兴与它。

如果有人读了这篇文章会觉得有趣并想使用它，他们都可以在这里找到它:

[https://tflookup.herokuapp.com/](https://tflookup.herokuapp.com/)

你的丹尼斯

**结尾**

*这篇文章是 [tflookup 开发者日记系列](https://dev.to/t/tflookup)* 的五篇文章之一

*封面图片:弗雷德里克·鲁本森的《日记写作》*