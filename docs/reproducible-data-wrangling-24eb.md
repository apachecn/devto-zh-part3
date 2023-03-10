# 可再现的数据争论

> 原文：<https://dev.to/korenmiklos/reproducible-data-wrangling-24eb>

> “我花了一半以上的时间来整合、清理和转换数据，却没有进行任何实际的分析。”(在开创性的坎德尔、派普克、赫勒斯坦和赫尔商业分析实践访谈研究中的受访者)

在数据科学中，我们将大部分时间用于获取、转换、合并或以其他方式为实际分析准备数据，这几乎是一种陈词滥调。

[![Photo by [Mr Cup / Fabien Barral](https://unsplash.com/@iammrcup?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)](img/0f44bd386b109a4d3a797f12e819ef73.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--an560vG9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/8576/0%2A9F8L8Wj47VUOvtWF) *照片由[杯先生/杨奇煜·巴拉](https://unsplash.com/@iammrcup?utm_source=medium&utm_medium=referral)上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)* 

然而，这种*数据争论*也应该是可再现的。期刊审稿人、编辑和读者已经开始期望，如果我做一个理论陈述，我就提供一个证明。如果我做了一个统计上的声明，我会通过对方法论的讨论来支持它，并提供复制的软件代码。然而，争论的可重复性往往取决于作者的陈述，如“*我们使用 2013 年世界发展指标浪潮*”或“*数据来自宾大世界表*7。”

大多数作者不会让他们的数据争论具有可重复性，因为可重复性很难。非常辛苦。数据以各种格式出现，有些文件非常大，而且大多数研究人员不会使用一种通用的编程语言来实现数据转换过程的自动化。其实大部分数据转换还是 *ad hoc* ，在 Excel 里指指点点，复制粘贴，做一堆 VLOOKUPs。(声明一下，VLOOKUPs 很棒。)

举下面这个例子。对于最近的研究，我真的很想认真对待再现性，并按书上说的做每件事。这导致了一些挑战。

*   **大型数据集**。我使用的原始数据集有几十 GB 大小。到我扯皮结束的时候，最后也就几百 MB，但是如果我想让整个过程透明可复制，我还需要展示原始数据。

*   **不一致的 URL 和模式**。西班牙*通讯社*在网上发布*所有*他们的贸易非常有帮助。他们存储文件的方式和内容有很多结构，但每年都有一些不一致的地方让我畏缩和调试几个小时。(例如，在这里找到[链接中的奇数。)](https://www.agenciatributaria.es/AEAT.internet/Inicio/La_Agencia_Tributaria/Memorias_y_estadisticas_tributarias/Estadisticas/_Comercio_exterior_/Datos_estadisticos/Descarga_de_Datos_Estadisticos/Descarga_de_datos_mensuales_maxima_desagregacion_en_Euros__centimos_/2009/Enero/Enero.shtml)

*   **国家名称**。这是模式不一致的一个特例。每个数据源都使用自己的代码簿来识别国家。在最好的情况下，你得到 3 个字母的 ISO-3166 代码的国家，如匈奴和美国。这些都很棒，因为它们是标准的，非常容易阅读，对吗？没那么快。你知道当国家改变名称时，3 个字母的代码也会改变吗？当扎伊尔成为刚果民主共和国时，它的[代码从 ZAR 变成了 COD](https://www.iso.org/obp/ui/#iso:code:3166:ZR) 。最好是使用 ISO-3166 的 [*数字代码*，它们随着时间的推移相当稳定，但几乎没有人使用它们。](http://en.wikipedia.org/wiki/ISO_3166-1_numeric)

*   **网站上未记录和未支持的数据**。世界银行的[营商环境](http://doingbusiness.org/)项目提供了最大的跨国数据资源之一。但是当他们提出“获取所有数据”时，他们实际上并不是这个意思。

[![](img/9ca78003f64d623ed21095aa8d41d6cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cL-ca0-5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2A23yVozJ8i5uo3TPI.png)

他们的网站上有更详细的数据，你无法下载，也没有存档。例如，这些是 2014 年阿富汗进口的详细成本，但该网站不公布早些年的数据。幸运的是，[web.archive.org](http://web.archive.org/web/20091003023159/http://www.doingbusiness.org/ExploreTopics/TradingAcrossBorders/Details.aspx?economyid=2)前来救援。

[![](img/409003609702ec064ec16c318a3087cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DEJVv9Ig--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2A0lGAN_KO3AuJYFMs.png)

*   **大箱子的数据**。有一个 18MB 的。我用的是 860MB 的 xls 文件。一个作者在他们的网站上发布的 zip 文件。目标是值得称赞的(就像我上面说的，在复制包中提供所有可用的东西)，但是我更喜欢选择只下载我需要的东西。

*   **无证 vs 非法**。我使用的大多数经济学数据集都没有明确的许可条款。参见这个很有帮助的 [NBER 列表](https://www.nber.org/data/)，例如。对于大多数数据集，我不知道我可以用它们做什么。没有人喜欢做非法的事情，所以最好把它们从复制包中去掉。

为了让“可再生研究”和“开放数据”运动真正流行起来，我们需要更多的工具，比如来自 [FrictionlessData](https://frictionlessdata.io/) 、 [DataCite](https://datacite.org/) 的工具，以及可以通过编程方式查询的数据 API(比如[世界银行数据 API](http://data.worldbank.org/developers/api-overview) )。

如果你发布原始数据，请，请，遵循[世界银行](https://datacatalog.worldbank.org/search?sort_by=field_wbddh_modified_date&sort_order=DESC#)，[公开注册](https://offeneregister.de/daten/)，[公开招标](https://opentender.eu/start)，并提供不只是简单的下载方式，而是简单的许可条款，如[知识共享](https://creativecommons.org/)或[开放数据库许可](https://en.wikipedia.org/wiki/Open_Database_License)。