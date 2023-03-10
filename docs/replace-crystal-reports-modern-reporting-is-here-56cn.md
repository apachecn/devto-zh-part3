# 水晶报告开源替代方案

> 原文：<https://dev.to/jan_blaha/replace-crystal-reports-modern-reporting-is-here-56cn>

> 这篇文章是为谁写的
> 这篇文章是为软件开发人员写的，他们在使用 [Crystal Reports](https://www.crystalreports.com/) 时遇到了问题，并且正在考虑替换旧的或者实现新的报告层。

## 什么是水晶报表

Crystal Reports 是目前由 [SAP](https://www.sap.com) 销售的一款报表软件。它主要用于分析和生成 pdf、excel、word 和其他类型的文档。它曾经是市场上主要的报告软件，尤其是。基于. NET 的开发人员仍然在使用它，因为它集成了 Visual Studio。

[![crystal-designer](img/c8cf8ea6abcfb9fb1315b4d70037719d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YsFeinJL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/101oqar2k6jnwt20rnfr.png)

## 主要问题

当我在 [stackoverflow](https://stackoverflow.com/questions/tagged/crystal-reports?sort=newest&pageSize=50) 上无休止地寻找解决方案时，水晶报告多次给我带来深深的挫败感。如何让这个场变成我想要的样子？如何按照我想要的方式计算这个表格...？这些是你可以在那里找到的典型问题。现在，我将尝试指出我对 Crystal Reports 的最大问题。

*   共享的样式和布局
    我希望我的报表共享主题和布局。我知道我可以使用子报告功能来解决其中一些问题。但是，我希望完全能够单独设计我的报告并重用它们。就像我能用 css 做的一样。

*   **报告版本控制和协作**
    报告模板存储在某种二进制`rpt`文件中。这是不可能通过公共源代码控制来版本化的。也不可能与多个开发人员合作，因为文件合并不起作用。

*   **漫长的学习曲线**
    学习曲线实在太长了。通常，团队中有一个开发人员负责使用 Crystal Reports。这不是因为它很难理解，而是因为你需要学习如何说服设计师按照你想要的方式工作的技巧。使用水晶报表和浏览 StackOverflow 几个小时后你学会的技巧。

*   **限制设计师**
    设计师对人类来说是伟大的，但对开发者来说不是。在某些条件下做一个“for 循环”比使用设计器点击它要快得多。在许多情况下，报告受到设计者能力的限制。

*   **不是开源的**
    Crystal Reports 是一款闭源软件这一事实非常具有局限性。当一个有经验的软件开发人员遇到困难时，他通常能够在库代码中找到答案。通常情况下，使用 Crystal Reports 在 StackOverflow 上搜索没有其他帮助。

*   **积极发展和创新**
    水晶报告的上一次发布是在 2016 年。这只是很久以前的事了。我认为有很多事情要做，我希望每隔几个月就会有新的发布。尽管有成千上万的活跃用户和客户，但这种情况并没有发生。

## 现在怎么样了

我为我的消极态度道歉。水晶报表是极具特色的传奇。特别是对于非开发人员来说，是设计者和数据源选择的好方法。然而，对于软件开发人员来说，这对我来说还不够好，我决定用不同的方式做报告。

## jsreport

jsreport 是一款报告软件，它的工作方式与 Crystal Reports 不同。事实上，最初开发它是为了解决我在使用 Crystal Reports 时遇到的问题。让我们一步一步来。

[![jsreport-editor](img/25891339aba5078cd1f114704ac3eb58.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LfFvEehp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/he8xodw9a04icff07i6z.png)

*   **漫长的学习曲线**
    有了 jsreport，你不需要学习专门的设计器或者格式。你只需使用你所知道的标准如 html、css 和 javascript 来编写报告。

*   **共享的样式和布局**
    你可以使用 css 来设计你的报告。模板的布局是使用 javascript 模板引擎实现的，比如 [handlebarsjs](https://handlebarsjs.com/) 。这意味着你有无限的可能性来设计你的报告。

*   **限制设计师**
    你不会被 jsreport 困住，因为设计师不是限制。你编写你的报告，唯一的限制是你的想象力。

*   **不是开源**
    它是一个托管在 github 上的开源项目[。您可以随时查看代码，找出错误所在。](https://github.com/jsreport)

*   **报告版本控制和协作**
    jsreport 默认情况下将模板存储在文件系统上的文本文件中。通常，你在磁盘上看到的只是`html`、`js`、`css`和`json`文件。这些易于版本化，您可以简单地将 jsreport 模板文件夹添加到 git 之类的源代码控件中。

*   **积极开发和创新**
    积极开发和维护 jsreport。你可以在 jsreport 博客上看到，每隔几个月就会有很棒的新功能出现。

### js report 入门

你可以看看 jsreport [上的展示](https://jsreport.net/showcases/)，在现场操场上运行[示例，如果你喜欢它的工作方式，你可以做出决定。然后你可以](https://playground.jsreport.net/)[下载](https://jsreport.net/on-prem)它并查看[文档和教程](https://jsreport.net/learn)。

[![jsreport-showcase](img/ef202c0696293d2795025870b4b2dad3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eDWnp0Vv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1aoltny9x3u72wv6nhuh.png)

## 结论

这是我在使用 Crystal Reports 时遇到的问题的快速总结。jsreport 用一种完全不同的方法解决了这些问题，我推荐你去看看。

如果您对 jsreport 有任何疑问，请查看我们的[论坛](https://forum.jsreport.net)并填写。