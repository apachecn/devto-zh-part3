# tflookup -开发者日记第 3 部分

> 原文：<https://dev.to/dploeger/tflookup-developer-diary-part-3-269j>

亲爱的日记！

现在我有了一个文档索引，我可以编写一个服务器来服务这个索引并搜索它。该服务器最终也将交付我的前端应用程序，因为我现在懒得用微服务的方式来做。

目前我所知道的最简单和稳定的 Node web 服务器是 [ExpressJS](https://expressjs.com) ，这就是我所使用的。

我今天主要用 Typescript 做我的项目，所以这是我的基本设置。

在索引被创建或从我的预呈现索引中读取之后，[服务器](https://github.com/dploeger/tflookup/blob/master/lib/Server.ts)启动，注册它的端点并监听请求。

一个[非常简单的控制器](https://github.com/dploeger/tflookup/blob/master/lib/controllers/IndexController.ts)基本上只输出索引的不同区域:

*   [完整索引](https://tflookup.herokuapp.com/api/index)
*   [所有可用的供应商](https://tflookup.herokuapp.com/api/index/vendors)
*   [供应商可用的对象类型(数据源和资源)](https://tflookup.herokuapp.com/api/index/vendors/kubernetes)
*   [所有数据源由厂商提供](https://tflookup.herokuapp.com/api/index/vendors/kubernetes/datasources)和[所有资源由厂商提供](https://tflookup.herokuapp.com/api/index/vendors/kubernetes/resources)

有趣的部分来自搜索端点。

我可以简单地返回包含某个查询的所有项目，但是我希望得到*加权的*结果。

你看，我不是搜索引擎专家，其他人可以做得更好，但我试过了。

我如何衡量搜索结果？我需要为此制定一个[决策流程](https://github.com/dploeger/tflookup/blob/master/lib/api/DocumentationSearcher.ts#L49)并权衡结果

*   数据源和资源比供应商更重要
*   资源比数据源更相关(是的，数据源非常固执己见)
*   名字中的匹配比描述中的更相关
*   如果在供应商**和对象**中找到匹配，结果变得更加相关

此外，我公然窃取了精彩的 [Keeweb 项目](https://keeweb.info/)的[字符串排序算法](https://github.com/keeweb/keeweb/blob/master/app/scripts/util/ranking.js#L2)

我想，这是目前最好的了。

你的丹尼斯

*这篇文章是 [tflookup 开发者日记系列](https://dev.to/t/tflookup)* 的五篇文章之一

*封面图片:弗雷德里克·鲁本森的《日记写作》*