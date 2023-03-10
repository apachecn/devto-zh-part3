# MongoDB 的 BI 连接器:商业智能的智能连接器

> 原文：<https://dev.to/kenwalger/mongodbs-bi-connector-the-smart-connector-for-business-intelligence-2pno>

在当今世界，我们周围到处都在产生和存储数据。企业利用这些数据来洞察用户和设备的行为。MongoDB 是存储数据的好方法。通过灵活的数据模型和动态模式，它允许将数据存储在丰富的多维文档中。但是，大多数商业智能工具，如 [Tableau](https://www.tableau.com/) 、 [Qlik](https://www.qlik.com/us) 和 [Microsoft Excel](https://www.mongodb.com/blog/post/analyzing-data-in-microsoft-excel-with-the-mongodb-connector-for-bi) ，都需要表格格式的东西。这就是 [MongoDB 针对 BI](https://www.mongodb.com/products/bi-connector) (BI 连接器)的连接器大放异彩的地方。

#### MongoDB BI 连接器

BI 连接器允许使用 MongoDB 作为基于 SQL 的商业智能和分析平台的数据源。这些工具允许在您的数据上创建仪表板和数据可视化报告。利用它们，您可以提取数据中隐藏的洞察力。这可以让你更深入地了解客户是如何使用你的产品的。

MongoDB Connector for BI 是一个用于数据工具箱的工具，它充当数据库和报告工具之间的转换层。BI 连接器本身不存储数据。它充当了 MongoDB 数据和商业智能工具之间的桥梁。

[![MongoDB BI Connector Flow](img/61ab30e011b18bc381f1f885f36c825e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OuY-Qq8S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/webassets.mongodb.com/_com_assets/cms/bi-connector-flow-j9cm8mm6pt.png%3Fw%3D640%26ssl%3D1)

BI 连接器弥补了本地、内部或托管的 MongoDB 实例之间的工具差距。如果您正在使用 [MongoDB Atlas](https://www.mongodb.com/cloud/Atlas) 并且在 M10 或更高的集群上，有一个集成的内置选项。

#### 为什么要使用 BI 连接器

如果没有 BI 连接器，您通常需要对数据执行提取、转换和加载(ETL)过程。将它从数据库中的“真实来源”转移到数据湖。有了 MongoDB 和 BI 连接器，就可以避免这个代价高昂的步骤。对您最新的数据进行分析是可能的。实时的。

商业智能系统有四个组成部分。数据库本身、BI 连接器、开放式数据库连接(ODBC)数据源名称(DSN ),最后是商业智能工具本身。让我们来看看如何连接所有这些部分。

我将在 Mac OS X 做这个例子，但其他系统应该是类似的。在开始之前，您需要满足一些系统要求:

在 Atlas 集群中加载视频中使用的数据集的说明可在[这里](https://github.com/kenwalger/bi-demo)找到。

* * *

*这篇文章最初发表在 [MongoDB 博客](https://dev.to/mongodb/mongodbs-bi-connector-the-smart-connector-for-business-intelligence-12k3-temp-slug-1016511)上。*

[![Facebook](img/79cfd3c9812f2a2b24840e107dfe0e1a.png "Share on Facebook")](http://www.facebook.com/sharer.php?u=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodbs-smart-bi-connector%2F&t=MongoDB%E2%80%99s%20BI%20Connector%3A%20The%20Smart%20Connector%20for%20Business%20Intelligence&s=100&p%5Burl%5D=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodbs-smart-bi-connector%2F&p%5Bimages%5D%5B0%5D=https%3A%2F%2Fwebassets.mongodb.com%2F_com_assets%2Fcms%2Fbi-connector-flow-j9cm8mm6pt.png&p%5Btitle%5D=MongoDB%E2%80%99s%20BI%20Connector%3A%20The%20Smart%20Connector%20for%20Business%20Intelligence)[![twitter](img/e3a323cb1ef89a3b7bfbb40f5a5800cd.png "Share on Twitter")](https://twitter.com/intent/tweet?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodbs-smart-bi-connector%2F&text=Hey%20check%20this%20out)[![google_plus](img/9a7da8faca1a1fde7772bc97d1ffaea0.png "Share on Google+")](https://plus.google.com/share?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodbs-smart-bi-connector%2F)[![reddit](img/67ebb1b320193a9bf3c562f5d77f04df.png "Share on Reddit")](http://www.reddit.com/submit?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodbs-smart-bi-connector%2F&title=MongoDB%E2%80%99s%20BI%20Connector%3A%20The%20Smart%20Connector%20for%20Business%20Intelligence)[![linkedin](img/f9684edc4e8f6cf298d3fa21f5869127.png "Share on Linkedin")](http://www.linkedin.com/shareArticle?mini=true&url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodbs-smart-bi-connector%2F&title=MongoDB%E2%80%99s%20BI%20Connector%3A%20The%20Smart%20Connector%20for%20Business%20Intelligence)[![mail](img/265476ced77e071f0a8d25be8996a26b.png "Share by email")](mailto:?subject=MongoDB%E2%80%99s%20BI%20Connector%3A%20The%20Smart%20Connector%20for%20Business%20Intelligence&body=Hey%20check%20this%20out:%20https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodbs-smart-bi-connector%2F)

帖子 [MongoDB 的 BI 连接器:商业智能的智能连接器](https://www.kenwalger.com/blog/nosql/mongodb/mongodbs-smart-bi-connector/)首先出现在肯·w·阿尔杰的[博客上。](https://www.kenwalger.com/blog)