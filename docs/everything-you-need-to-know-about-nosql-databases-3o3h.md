# 关于 NoSQL 数据库你需要知道的一切

> 原文：<https://dev.to/lmolivera/everything-you-need-to-know-about-nosql-databases-3o3h>

你好戴夫。已经有一段时间了，但我现在有了一篇经过大量研究的文章，因为我觉得这里的答案对我来说还不够。

我建议你先看看我写的这篇关于关系数据库的文章，以便更容易理解这篇文章。

# 索引

*   什么是 NoSQL？
    *   [定义](#definition)
    *   [特性](#features)
    *   [术语](#terminology)
*   [优缺点](#advAndDis)
    *   [优势](#adv)
    *   [缺点](#dis)
*   [NoSQL 数据库的类型](#types)
    *   [键值](#key)
    *   [文件](#document)
    *   [图形](#graph)
    *   [柱状](#columnar)
*   [NoSQL 和关系数据库的比较](#comparison)
    *   [缩放](#scaling)
    *   [维护](#maintenance)
    *   [数据模型](#dataModel)
    *   [缓存](#caching)
*   [选择特定的数据库](#choosing)
    *   [特征对比](#featureComparison)
    *   [NoSQL 越过关系](#NoOverRel)
    *   [多语言持久性](#polyglot)
    *   [基准数据库](#benchmarking)
*   [如何设计 NoSQL 数据库](#design)
*   [重要链接](#more)
*   [来源](#sources)

# 什么是 NoSQL？

### 定义

[关系数据库](https://dev.to/lmolivera/everything-you-need-to-know-about-relational-databases-3ejl)是在[瀑布模型](https://en.wikipedia.org/wiki/Waterfall_model)非常流行的时候创建的，但它们并不是为了应对现代应用程序的规模和敏捷性而设计的，也没有利用当今可用的商品存储和处理能力。

NoSQL 是在 90 年代后期为解决这些问题而创建的一种数据库，之所以这样称呼是因为它们不使用 SQL(但是今天它们被称为“不仅仅是 SQL ”,因为一些管理系统实现了查询语言)。NoSQL 数据库主要解决了一些问题:非关系的、分布式的、开源的和水平可伸缩的。

值得一提的是，如今关系数据库已经有了显著的改进，解决了它们在处理当今技术时遇到的大部分问题。NoSQL 数据库是存储数据的另一种方式，不一定比关系数据库好。两者都旨在解决不同类型的需求。

### 特性

*   分布式计算系统。
*   更高的可扩展性。
*   降低成本。
*   灵活的模式设计。
*   处理非结构化和半结构化数据。
*   没有复杂的关系。
*   开源的。

### 术语

*   节点:提供某种服务、本地存储和访问更大的分布式系统或文件存储的网络计算机。
*   集群:一组节点。
*   分片(或水平分区):根据某个字段的值对数据库进行分区。
*   复制:部分数据被写入多个节点，以防其中一个节点出现故障(确保可用性)。
*   酸: **A** 渗透性， **C** 相容性， **I** 溶解性， **D** 耐久性。是数据库事务的一组属性，旨在即使在出错、断电等情况下也能保证有效性。
*   基础:**B**asially**a**available(24/7 不可用) **s** oft-state(数据库可能不一致)**e**vent 一致(最终会一致)。

# 优点和缺点

### 优点

*   **弹性可伸缩性**:这些数据库设计用于低成本的商用硬件。
*   **大数据应用**:NoSQL 数据库轻松处理海量数据。
*   **经济**:关系数据库需要安装昂贵的存储系统和专有服务器，而随着交易和数据量的增加，NoSQL 数据库可以很容易地安装在廉价的商用硬件集群中。这意味着您可以以更低的成本处理和存储更多的数据。
*   **动态模式** : NoSQL 数据库不需要模式就可以开始处理数据。在关系数据库中，您必须首先定义一个模式，这使得事情变得更加困难，因为每次需求改变时，您都必须改变模式。*注意*:这意味着必须对应用程序进行所有数据质量控制。*注 2* :没有模式并不是每个 NoSQL 数据库的特点，如果我们不正确地组织数据，这也可能是一个缺点。
*   **自动分片**:关系数据库是垂直扩展的，这意味着你经常会有很多数据库分布在多个服务器上，因为它们需要磁盘空间来工作。NoSQL 数据库通常支持自动分片，这意味着它们可以在任意数量的服务器上自动传播数据，而不需要应用程序知道服务器池的组成。
*   **复制**:大多数 NoSQL 数据库还支持自动数据库复制，以便在发生停机或计划维护事件时保持可用性。更复杂的 NoSQL 数据库是完全自我修复的，提供自动故障转移和恢复，以及跨多个地理区域分布数据库的能力，以承受区域故障并实现数据本地化。
*   **集成缓存**:许多 NoSQL 技术都具有出色的集成缓存能力，将常用数据尽可能多地保存在系统内存中，无需单独的缓存层。

### 缺点

*   NoSQL 数据库不具备关系数据库所具备的可靠性功能(基本上不支持 ACID)。
    *   这也意味着 NoSQL 数据库在性能和可伸缩性方面提供了一致性。
*   为了支持 ACID，开发者必须实现他们自己的代码，这使得他们的系统更加复杂。
    *   这可能会减少提交交易的安全应用程序的数量，例如银行系统。
*   NoSQL 与 SQL(完全)不兼容。
    *   *注意*:一些 NoSQL 管理系统确实使用结构化查询语言。
    *   这意味着您将需要一种手动查询语言，使事情变得更慢、更复杂。
*   与关系数据库相比，NoSQL 是非常新的，这意味着它远没有关系数据库稳定，功能也少得多。

# NoSQL 数据库的类型

[![](img/d042c46796ea3b13ca83b728da928a8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c5dHJonR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h3qcpsvalz0mqqwnd7hk.png) 
*注意*:有些规则会取决于你选择的管理系统。

### 键值

键值存储是最简单的 NoSQL 数据库。数据库中的每一项都存储为属性名(或“键”)及其值，类似于字典。

**特性**

*   可扩展性:大量的数据和用户。
*   速度:大量的查询。
*   数据模型:键值对。
*   一致性。
*   交易。
*   质疑能力。
*   可扩展性。

**操作**

*   get(key):获取给定键的值。
*   put(key，value):根据给定的键创建/更新一个值。
*   delete(key):删除给定键的值。
*   execute(key):调用一个值的操作。

**限制**

*   多重数据之间没有关系。
*   多操作事务:如果您存储了许多键，但其中一个键保存失败，则无法回滚其余的操作。
*   通过“值”查询数据:基于在键-值对的“值”部分找到的一些信息搜索“键”。
*   分组操作:由于一次只能操作一个键，所以无法同时运行几个键。

**真实生活示例**
键值最适合存储用户配置文件:

*   用户标识，用户名。
*   其他属性/首选项:
    *   语言
    *   国家
    *   时区
    *   用户收藏夹
    *   等等

**键值数据库**

*   [DynamoDB](https://aws.amazon.com/dynamodb/?nc1=h_ls)
*   [Riak](https://riak.com/products/)
*   [伯克利数据库](https://www.oracle.com/technetwork/database/database-technologies/berkeleydb/downloads/index.html)
*   [再说一遍](https://redis.io/)

### 文档

文档存储将每个键与一个称为文档的复杂数据结构配对，该文档可以包含许多不同的键-值对、键-数组对，甚至嵌套文档。文档被视为完整的，并且避免将文档分割成其组成的名称/值对。

**特性**

*   可伸缩性:对于更复杂的对象。
*   数据模型:文档的集合。
    *   类似于 JSON 和 XML。
*   实现 ACID 事务并适应 RDBMS 特征。
    *   允许基于文档的主要标识符和属性对文档进行索引。
    *   支持查询事务(在一定程度上)。
*   设计模式允许在单个操作中检索信息。
*   避免在应用程序中执行连接。

**操作**

*   搜索依据:
    *   田
    *   范围
    *   正则表达式
*   查询:可以包含 Javascript 函数。
*   索引:可以在任何领域进行。

**类型**
XML 数据库: [![](img/13474d76f01cfb83695fd79d61f93a8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gTb09VX2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tzggwqq1s2lctakvkhy8.png)

*   XML 文档形成了第一个文档 DB。
*   XML 有各种各样的标准和工具来帮助创作、验证、搜索和转换 XML 文档。
    *   XPath:从 XML 文档中检索特定元素的语法。
    *   XQuery:用于烧烤 XML 文档的查询语言，也称为“XML 的 SQL”。
    *   XML schema:文档模板，解释哪些元素可能出现在指定的 XML 文档类中，以验证文档的正确性。
    *   XSLT:将 XML 文档转换成其他格式的语言，比如 HTML 等非 XML 格式。
*   著名的 XML 数据库:eXist(开源)和 MarkLogic(商业)。

JSON 数据库: [![](img/4866e400c45330b604ab2570aebd276a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G9faRw5C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2mpaf258xcbvq13h2yin.png)

*   JSON 文档数据库期望数据以 JSON 的格式存储。
*   类似于 RDBMS 中的行。
*   包含一个或多个键值对、嵌套文档和数组。数组可能包含复杂的层次结构。
*   集合(数据桶)是一组共享一些共同目标的文档(类似于 RDBMS 中的表)。
*   尽管是首选，集合中的文档不必是同一类型。
*   JSON 数据库:
    *   MongoDB
    *   CouchDB
    *   OrientDB
    *   DocumentDB。

**数据建模**
[![](img/c7ab137ba33f070d7167079746cca08c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2vzHiSi9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/valby86btntl0ildccq9.png)

*   与 RDBMS 相比，确定性较低。
*   由要执行的查询的性质驱动，而在 RDBMS 中，由要存储的数据类型驱动。

**限制**

*   多个文档中的基本信息重复
*   使设计复杂化，导致不一致。
    *   解决方案:使用文档标识符链接多个文档(类似于 RDBMS 中的外键)

**现实生活中的例子**
下面是一份[现实生活中的例子](https://www.edureka.co/blog/real-world-use-cases-of-mongodb/)。

**文档数据库**

*   [MongoDB](https://www.mongodb.com/es)
*   [CouchDB](https://couchdb.apache.org/)

### 图表

图形存储是一种表达结构，由节点和相互链接的关系组成，用于存储关于数据网络的信息，如社会关系。基于图论的数学理论。

**图形的一部分**

*   节点-实体的表示。
*   属性-关于节点的信息。
    *   可以被索引。
*   边-节点之间的关系。
    *   可以被索引。
    *   单向或双向，不限边。

**理解图论**
[![](img/3e7a2502be5db2175e316c64385bacd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KN1iHKtk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zrn6wxokavk2ni3jnitb.png)

根据图论，图的主要组成部分包括:

*   代表不同对象的顶点或节点。
*   在这些对象之间建立连接的边、关系或弧。
*   节点和关系都带有一些属性。
    *   节点的属性类似于关系表/JSON 文档的属性。
    *   关系属性考虑关系的类型、强度或历史。

图论赋予数学符号

*   从图形中添加/删除节点或关系
*   执行操作以跟踪相邻节点。

核心规则--“没有断开的链接”:一个关系应该总是有一个开始和结束节点。如果不删除节点的关联关系，就不可能删除节点。

**类型**
在一个非常高的层次上，图形存储可以分为两种:

1)图形数据库-(实时)

*   实时执行事务性在线图形持久化。
*   类似于 RDBMS 领域的联机事务处理(OLTP)数据库。

2)图形计算引擎-(批处理模式)

*   通过一系列步骤批量执行离线图形分析。
*   类似于在线分析处理(OLAP ),用于批量数据分析，如数据挖掘。

**特性**

*   适应数据的复杂性。
*   关注互联性。
*   许多查询语言。

**操作**
将取决于它的查询语言。例如，Neoj4 使用[密码查询语言](https://neo4j.com/developer/cypher/)。

**限制**

*   缺乏高性能并发性:在许多情况下，图形数据库提供多读取器和单写入器类型的事务，这阻碍了它们的并发性和性能，从而在一定程度上限制了线程并行性。
*   缺乏标准语言:缺乏一种良好建立的标准声明性语言是当今的一个问题。Neo4j 正在提出 Cypher，Oracle 正在开发一种语言。
*   缺乏并行性:一个重要的问题是划分一个图是一个问题。因此，大多数不提供对非常大的图形的无共享并行查询。因此，允许并行本质上是一个问题。

**现实生活中的例子**
图形存储用于模拟各种不同的场景，例如:

*   建造宇宙火箭。
*   交通系统(公路和火车)。
*   供应链和物流。
*   病史。
*   欺诈检测。
*   网络和 IT 运营。

**图形数据库**

*   [Neo4J](https://neo4j.com/)
*   [吉拉夫](https://giraph.apache.org/)。

### 柱状

宽列/列/列存储针对大型数据集上的查询进行了优化，这些存储基于列族。列存储数据库使用一个叫做键空间的概念。键空间有点像关系模型中的模式。键空间包含所有列族，列族包含行，行包含列。

列数据库与关系数据库有很大的不同:表不是由一组行或元组组成，每一列都有一个值，表是一组列，每一列可能包含也可能不包含特定行键的值。

[![](img/7c258e218dc0f0efba9ffd9a4a78196b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--feeOhkUf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/06234ahrswsgw2d7yuab.png)

[![](img/ef4e314231e47f24acc0129d413d5c04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pTpGdcIL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/96bmmqj5jrvwnusc0kx6.png)

**特性**

*   压缩:列存储在数据压缩和/或分区方面非常有效。
*   聚集查询:由于它们的结构，列数据库在聚集查询(如 SUM、COUNT、AVG 等)方面表现特别好。
*   可伸缩性:列数据库是非常可伸缩的。它们非常适合大规模并行处理，这涉及到将数据分散到一个大型的机器集群中——通常有数千台机器。
*   快速加载和查询:柱状存储可以非常快地加载。几秒钟内就可以加载十亿行的表。

这些只是使列数据库成为处理大数据的组织的热门选择的一些好处。

**操作**
根据您使用的管理系统，操作和某些功能会有很大差异。

**限制**

*   增量数据加载:写数据比读数据花费更多的时间。在线事务处理(OLTP)的使用。
*   只对几行进行查询:读取特定数据比预期花费更多的时间。

**现实生活中的例子**

*   一家超市的蔬菜专栏。
*   客户端的柱族。
*   用户的列族。

柱状数据库

*   卡桑德拉
*   [HBase](https://hbase.apache.org/)
*   [Kudu](https://kudu.apache.org/overview.html)

### 上限定理

[![](img/569a6894c34750668c60ab382ab9a7bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cYcceEJt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xkiuv5g9bdllz1cxmfjw.png)

理解 NoSQL 数据库的局限性是非常重要的。NoSQL 无法同时提供一致性和高可用性。Eric Brewer 在 CAP 定理中首次表述了这一点。

CAP 定理或 Eric Brewers 定理指出，对于一个数据库，我们最多只能实现三个保证中的两个:一致性、可用性和分区容差。

*   一致性:每次读取都会收到最近的写入或错误。
*   可用性:每个请求都会收到一个(无错误的)响应，但不能保证它包含最近的写入。
*   分区容忍度:即使数据中心出现网络中断，一些计算机无法访问，系统仍会继续运行。

没有一个系统能提供超过两种保证。在分布式系统的情况下，网络的划分是必须的，因此总是在一致性和可用性之间进行权衡。

如果你想了解更多关于 CAP 的信息，请查看[这个链接](https://howtodoinjava.com/hadoop/brewers-cap-theorem-in-simple-words/)和[这个](https://cloudxlab.com/assessment/slide/11/nosql/345/nosql-cap-theorem)。

### NoSQL 与关系数据库的比较

[![](img/530c52541217d621274bed4255111b05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zN-dzpKP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v8q6idhbis51r55h88og.png)

考虑到这种比较是在数据库级别进行的，它不包括任何实现这两者的管理系统。数据库管理系统包括它们自己的技术来解决这些问题，并提高性能和可靠性。

**缩放**

*   关系数据库:垂直扩展。
    *   架构设计在单台机器上运行良好。
    *   要处理更大的运算量，就要用更快的处理器或更大的内存来升级机器。
    *   由于您需要更多的计算机来处理更多的数据，因此规模/级别是有限制的。
*   NoSQL 数据库:水平缩放。
    *   NoSQL 数据库旨在运行在相对低规格的服务器集群上。
    *   要处理更多数据，请向群集中添加更多服务器。
    *   经过校准，即使使用低成本硬件，也能全速运行。
    *   处理增加的数据的相对便宜的方法:操作的数量和数据的大小。

**维护**

*   关系数据库:维护高端 RDBMS 系统是昂贵的，并且需要训练有素的数据库管理人员。
*   NoSQL 数据库:需要最少的管理，并且它支持许多特性，这使得管理和调优需求变得更少。这包括自动修复、更容易的数据分发和更简单的数据模型。

**数据模型**

*   关系数据库:严格的数据模型。
    *   RDBMS 要求按照定义的数据模型使用结构化格式的数据。
    *   由于变更管理是 SQL 中一个令人头痛的问题，它强烈依赖于主键/外键，所以特别的数据插入变得更加困难。

*注意*:值得一提的是，关系数据库在处理非结构化或半结构化数据方面已经变得越来越好，PostgreSQL 的可索引二进制 JSONB 数据类型处于领先地位。如果您有一个混合体，将您的非结构化数据放入关系上下文比尝试将您的关系数据放入 NoSQL 上下文要容易和安全得多。

*   NoSQL 数据库:没有模式/数据模型。
    *   NoSQL 数据库是无模式的，因此即使没有任何预定义的模式，也可以轻松地将数据插入到数据库中。
    *   格式或数据模型可以随时更改，不会中断应用程序。

**缓存**

*   关系数据库:典型的 RDBMS 数据库中的缓存需要单独的基础设施。
*   NoSQL 数据库:NoSQL 数据库支持系统内存中的缓存，因此提高了数据输出性能。

### 选择特定的数据库

现在，我们已经了解了不同种类的 NoSQL，您现在应该知道，NoSQL 数据库并不相似，也不是为了解决相同的问题而设计的。

根据具体情况，了解哪个数据库是合适的非常重要。选择 NoSQL 数据库时要考虑的参数有:

*   数据库功能
*   表演
*   基于上下文的标准

对它们进行分组的最佳方式是比较它们的特征，以便为我们面临的问题选择正确的特征。

#### 功能对比

**可扩展性**

*   并不是所有的 NoSQL 数据库都承诺同等利润的水平可伸缩性。
*   HBase 和 Hypertable 占据优势，Redis、MongoDB 和 Couchbase Server 落后。
*   随着数据大小增长到几 Pb 以上，这种差异变得更加明显。

**交易完整性和一致性**

*   只有当数据被修改、更新、创建和删除时，事务完整性才适用。
*   与纯数据仓库和挖掘环境无关，在纯数据仓库和挖掘环境中，数据写入一次，读取多次。
    *   比如网络流量日志、社交网络状态更新、股市行情数据和游戏比分。
*   如果更新是常见的，并且操作范围要求更新的完整性，RDBMS 是最佳选择。
*   如果单个项目级别的原子性足够，列族数据库(HBase 和 Hypertable)和文档数据库(MongoDB)就非常适合。

**数据建模**
关系数据库管理系统(RBDMS)提供了一种一致的、有组织的数据建模方法，并实现了标准化。NoSQL 世界没有为标准化和定义良好的数据模型提供任何空间，因为它们不一定要解决相同的问题或具有相同的架构。

MongoDB 逐渐采用了一些 RDBMS 概念，例如:

*   类似 SQL 的查询。
*   基本的关系引用。
*   数据库对象(受标准表和基于列的模型的启发)。

**查询支持**
轻松有效地从任何数据库中查询数据被认为是一个有待解决的有趣难题。由于有了标准化的语法和语义，RDBMS 依靠 SQL 支持轻松访问数据。

在 NoSQL:

*   MongoDB 和 CouchDB (Document DB)具有与 RDBMS 同样强大的查询功能。
*   Redis (Key-Value DB)单独提供对它存储的数据结构的查询。
*   在 Columnar DB 下，HBase 有一点点查询功能。

**访问和接口可用性**

*   MongoDB 在这个领域占据主导地位，它提供了主流库的接口和交互驱动程序。
*   CouchDB 除了 RESTful HTTP 接口之外，几乎没有可用的驱动程序。
*   用于连接大多数主流语言的语言绑定只有少数语言可用，如 Redis、Membase、Riak、HBase、Hypertable、Cassandra 和 Voldemort。

#### NoSQL 统领关系

在下列情况下，应该选择 NoSQL 数据库而不是关系数据库:

*   您拥有非结构化或半结构化数据，或者混合了非结构化和关系数据。
*   您需要支持多个查询，同时加载大量数据。
*   您需要在多个项目中重用部分数据。
*   您拥有快速变化的模式，或者需要在没有六个月(或更长)开发周期的情况下获取新的信息源。
*   您需要整合多种不同的数据类型和数据源，而不必被迫对数据建模或创建模式。

#### 多种语言的持久性

[![](img/e97a05a8ddfb4b2636d266f0ee35ae8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NPB9QYZi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cn0qsjjc1ydrpq6owmr2.png)

通晓多种语言的:懂得或使用几种语言的。

多语言编程允许我们为适当的任务选择适当的语言。一个数据库并不适合所有的规模和知识，采用多个数据库是明智的策略。多种数据库产品和方法的知识和使用现在被普遍地称为多语言持久性。

#### 基准数据库

基准测试使我们能够深入了解不同的 NoSQL 产品的情况。

*   雅虎！云服务基准测试是比较 NoSQL 产品的著名基准测试基础设施之一
*   [东京内阁基准](http://tokyocabinet.sourceforge.net/benchmark.pdf)
*   [Redis 有多快](https://redis.io/topics/benchmarks)

### 如何设计一个 NoSQL 数据库

NoSQL 数据库的设计取决于数据库的类型。

*   对于 NoSQL 文档数据库建模的指导，在这里输入。这里还有一个链接。

*   这里有一个微软官方 Youtube 账号[视频](https://www.youtube.com/watch?v=IUxT7ZRHlZ4)用于文档数据库建模。

*   这是亚马逊键值数据库 [DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/best-practices.html) 的官方文档。

    *   有人告诉我，如果用“碎片键”替换“分区键”，用“索引”替换“排序键”，这对 Cassandra 也很有用。它们也翻译成 MongoDB。
*   这个[环节](https://www.kidscodecs.com/database-design/)和[另一个](https://highlyscalable.wordpress.com/2012/03/01/nosql-data-modeling-techniques/)包括各种数据库。

*   也可以查看[这个有趣的线程](https://www.quora.com/What-is-a-good-way-to-design-a-NoSQL-database)。

### 重要环节

*   NoSQL 数据库:一个有点过时的网站，包含了很多关于 NoSQL 数据库的信息。
*   [MongoDB 官方文档](https://docs.mongodb.com/):开始使用最著名的文档数据库。
*   本文通过 mongose:[![akhilaariyachandra image](img/ee0bcfb7229adae7e40949640d794de8.png)](/akhilaariyachandra) [## 解释了如何在 Node 中轻松使用 MongoDB 用猫鼬在节点. js 中设置 MongoDB

    ### 阿克希拉阿里亚钱德拉 1919 年 5 月 22 日 3 分钟阅读

    # JavaScript# node# MongoDB](/akhilaariyachandra/setup-mongodb-in-node-js-with-mongoose-4gik)T22】
*   Freecodecamp :它有一个名为“API 和微服务”的证书，在这个证书中，他们教你如何在 Node 中使用 MongoDB 和 Mongoose。
*   关于 NoSQL 的免费 Udemy 课程。
*   [大量 NoSQL 数据库的对比](https://kkovacs.eu/cassandra-vs-mongodb-vs-couchdb-vs-redis)。
*   我发现了一篇关于 MongoDB 的有争议的博文，我想你可能会感兴趣。

### 来源

*   我的工作培训。
*   [http://nosql-database.org](http://nosql-database.org)
*   [MongoDB: NoSQL 解释](https://www.mongodb.com/nosql-explained)。
*   [https://blog.pandorafms.org/es/bases-de-datos-nosql/](https://blog.pandorafms.org/es/bases-de-datos-nosql/)
*   [https://blogs . Oracle . com/Spain/qu-es-una-base-de-datos-no SQL](https://blogs.oracle.com/spain/qu-es-una-base-de-datos-nosql)
*   [https://www . Hadoop 360 . data science central . com/blog/advantages-and-lessons-of-no SQL-databases-what-you-should-k](https://www.hadoop360.datasciencecentral.com/blog/advantages-and-disadvantages-of-nosql-databases-what-you-should-k)
*   [https://mapr . com/blog/data-modeling-guidelines-no SQL-JSON-document-databases/](https://mapr.com/blog/data-modeling-guidelines-nosql-json-document-databases/)
*   [NoSQL 的局限](https://www.channelfutures.com/cloud-2/the-limitations-of-nosql-database-storage-why-nosqls-not-perfect)。
*   [中:SQL 和 NoSQL 的区别](https://medium.com/xplenty-blog/the-sql-vs-nosql-difference-mysql-vs-mongodb-32c9980e67b2)。
*   [维基百科:酸](https://en.wikipedia.org/wiki/ACID_(computer_science))。
*   [维基百科:CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)。
*   [维基百科:多语言持久性](https://en.wikipedia.org/wiki/Polyglot_persistence)。
*   [https://howtodoinjava . com/Hadoop/brewers-cap-theory-in-simple-words/](https://howtodoinjava.com/hadoop/brewers-cap-theorem-in-simple-words/)
*   [https://cloud xlab . com/assessment/slide/11/no SQL/345/no SQL-cap-theory](https://cloudxlab.com/assessment/slide/11/nosql/345/nosql-cap-theorem)
*   [https://database.guide/what-is-a-column-store-database/](https://database.guide/what-is-a-column-store-database/)
*   [https://www . flydata . com/blog/whats-unique-about-a-column-database/](https://www.flydata.com/blog/whats-unique-about-a-columnar-database/)
*   [https://mapr . com/blog/data-modeling-guidelines-no SQL-JSON-document-databases/](https://mapr.com/blog/data-modeling-guidelines-nosql-json-document-databases/)
*   [https://neo4j.com/blog/why-graph-databases-are-the-future/](https://neo4j.com/blog/why-graph-databases-are-the-future/)
*   [https://www . quora . com/What-Is-a-limit-of-graph-database-Is-any-situation-when-performance-of-graph-database-degrade](https://www.quora.com/What-is-a-limitation-of-graph-database-Is-there-any-situation-when-the-performance-of-the-graph-database-degrades)
*   [https://www . slide share . net/blimp yacht/polyglot-persistence-52711581](https://www.slideshare.net/blimpyacht/polyglot-persistence-52711581)
*   假人版的 NoSQL。
*   [企业 NoSQL 傻瓜版，MarkLogic 特别版](https://www.marklogic.com/resources/enterprise-nosql-for-dummies/?type=PD&publisher=GGLCPC&utm_source=google&utm_medium=cpc&utm_campaign=US/UK-NB&utm_content=nosql-for-dummies&gclid=EAIaIQobChMI7tnr97_G4gIVB4zICh06Xg7aEAAYASAAEgJqfvD_BwE):进入链接免费获得这本电子书！

### 最后的话

我希望这篇文章对你有用，如果你看到一些错误，想让我改正，请不要犹豫，在评论中告诉我！

感谢 Dian Fay 和 Slavius 在评论中做出的更正！

*感谢您的阅读。别忘了在 dev.to 和 Twitter 上关注我！*