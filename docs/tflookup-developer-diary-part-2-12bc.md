# tflookup -开发者日记第 2 部分

> 原文：<https://dev.to/dploeger/tflookup-developer-diary-part-2-12bc>

亲爱的日记！

嗯...一个查找应用程序。这怎么可能呢？

*   显然我需要一个前端。和后端，做繁重的工作。
*   前端和后端应该通过 REST 进行通信。还有什么？
*   后端应该索引 Terraform 文档
*   当然，一切都应该经过充分的测试

重要的事情先来。整个项目的绝对基础是文档索引。没有它，我会毁了这一切。

那么，terraform 文档实际上是如何构建的呢？

Terraform 开发人员使用一个包含多个子模块的大规模存储库为所有提供商实现这一点:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[hashi corp](https://github.com/hashicorp)/[terra form-网站](https://github.com/hashicorp/terraform-website)

### terraform.io 的构建配置和部分内容

<article class="markdown-body entry-content container-lg" itemprop="text">

# Terraform 网站 [![CI Status](img/9f49cd6b17239bb9eeae4b232c7c148a.png)](https://circleci.com/gh/hashicorp/terraform-website/tree/master)

这个存储库包含构建基础设施和一些用于 [terraform.io](https://terraform.io) 的内容。欢迎来自社区的拉请求！

## 目录

*   [网站如何运作](https://raw.githubusercontent.com/hashicorp/terraform-website/master/#how-the-site-works)
*   [医生住在哪里](https://raw.githubusercontent.com/hashicorp/terraform-website/master/#where-the-docs-live)
*   [将更改部署到 terraform.io](https://raw.githubusercontent.com/hashicorp/terraform-website/master/#deploying-changes-to-terraformio)
*   [在本地运行站点](https://raw.githubusercontent.com/hashicorp/terraform-website/master/#running-the-site-locally)
*   [预览 Terraform 核心的变更](https://raw.githubusercontent.com/hashicorp/terraform-website/master/#previewing-changes-from-terraform-core)
*   [编写普通文档内容](https://raw.githubusercontent.com/hashicorp/terraform-website/master/#writing-normal-docs-content)
*   [截图](https://raw.githubusercontent.com/hashicorp/terraform-website/master/#screenshots)
*   [导航条](https://raw.githubusercontent.com/hashicorp/terraform-website/master/#navigation-sidebars)
*   [使用子模块](https://raw.githubusercontent.com/hashicorp/terraform-website/master/#using-submodules)
*   [查找断开的链接](https://raw.githubusercontent.com/hashicorp/terraform-website/master/#finding-broken-links)
*   [更多关于`stable-website`T2】](https://raw.githubusercontent.com/hashicorp/terraform-website/master/#more-about-stable-website)

## 网站如何运作

↥ [回到顶端](https://raw.githubusercontent.com/hashicorp/terraform-website/master/#table-of-contents)

[terraform.io](https://terraform.io) 目前正处于过渡阶段，生产地点是混合型的:

*   快速处理所有流量。

*   以下路径(目前是所有营销内容)被代理到在 Vercel 上运行的 Next.js 应用程序:

    *   `/`(头版)
    *   `/community`
    *   `/cloud`(以及`/cloud`下的所有子路径)

    有关这些页面的帮助，请联系 Web 平台团队！

*   网站的其余部分都是由中间人构建的静态页面。这就是这个存储库所管理的！

…</article>

[View on GitHub](https://github.com/hashicorp/terraform-website)

他们在 Markdown 中编写所有文档，包括 Frontmatter，并与中间人一起将所有内容捆绑在一起

封面包括标题、布局、描述和文档在网站文档树中的位置信息。

所以这就是我需要的。

我还“子模块化”了 terraform-website 存储库，并编写了一个[索引器](https://github.com/dploeger/tflookup/blob/master/lib/api/DocumentationIndexer.ts)，它遍历 terraform 网站中所有提供者可用的所有数据源和资源文档。

这工作得相当好(除了一些小错误)[[https://github . com/dploeger/TF lookup/blob/master/index errors . txt](https://github.com/dploeger/tflookup/blob/master/indexErrors.txt)]，在这里人们忘记了如何编写合适的 Frontmatter。

然而，我需要做一些假设:

*   每个供应商都有一个存放文档的“网站/文档”子文件夹
*   只有一个数据源或资源
*   数据源位于“d”目录中，资源位于“r”目录中
*   标题设计为“:”

我没有浏览所有的文档文件，但是文档似乎是这样组织的。

使用所有这些信息，我可以创建我的文档索引，它包括标题、名称、它是数据源还是资源、描述以及到实际文档页面的链接。

我包含了存储索引文档的方法，所以未来的前端服务器不需要为此初始化所有需要的子模块。我还写了一个[脚本，每天晚上更新文档](https://github.com/dploeger/tflookup/blob/master/updateIndex.sh)，它运行在我的私人服务器上。

好了，索引完成。

你的丹尼斯

*这篇文章是 [tflookup 开发者日记系列](https://dev.to/t/tflookup)* 的五篇文章之一

*封面图片:弗雷德里克·鲁本森的《日记写作》*