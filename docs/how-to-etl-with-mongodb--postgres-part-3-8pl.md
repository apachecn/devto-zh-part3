# 如何用 MongoDB & Postgres 进行 ETL(第 3 部分)

> 原文：<https://dev.to/chingu/how-to-etl-with-mongodb--postgres-part-3-8pl>

第 3 部分，设计数据库

<figure>[![](img/d52f937ef99ddb1979a7598a5fae3a24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_Oas7rzY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AvlsoaZWot_ig5SX2Px8Cvg.jpeg) 

<figcaption>照片由 [Edho Pratama](https://unsplash.com/photos/T6fDN60bMWY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Unsplash](https://unsplash.com/search/photos/design?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

提取、转换和加载(ETL)过程的目标是获取源数据、清理和组织它，并最终将它存储在一个永久的位置。清理数据和组织数据结构通常是在临时暂存区进行的，而永久位置通常是操作数据库或数据仓库。

ETL 过程的两个基本组件是数据通过应用程序的路径和用于暂存和操作数据库的数据库设计。影响这些存储位置设计的因素是如何访问和操作信息，以及 ETL 过程中的步骤。

例如，操作数据库必须满足用户对性能和可用性的期望。快速加载新数据非常重要，因此不会影响用户[服务级别协议](https://en.wikipedia.org/wiki/Service-level_agreement) (SLA)。如果需要气候数据来支持每天的天气预报，来自观测站的数据必须及时可用，预报才有用。毕竟，如果周二的预报要到周三才能得到，那么它又有什么价值呢？

在本文中，我们将定义 Climate Explorer 应用程序所依赖的 ETL 过程的要求和约束，更重要的是，它如何影响暂存和操作数据库的设计。

本系列的前几篇文章提出将 Climate Explorer 应用程序作为一种探索 ETL 过程的方法，该过程将 MongoDB 用于临时区域，将 PostgreSQL 用于操作数据库。他们还描述了 Climate Explorer 前端和后端环境的设计和实现。

*   [第一部分，了解情况](https://dev.to/jdmedlock/how-to-etl-with-mongodb-and-postgres-part-1-5775)
*   [第二部分，设计&搭建环境](https://dev.to/jdmedlock/how-to-etl-with-mongodb--postgres-part-2-1422)

本文的 [Climate Explorer](https://github.com/jdmedlock/climateexplorer/tree/feature/04-config-dbs) 应用程序位于 GitHub 的 [feature/06-article-part3](https://github.com/jdmedlock/climateexplorer/tree/feature/06-article-part3) 分支中。

### 设计 ETL 流程

<figure>[![](img/855fc3d7b2b597297ad90470647b8995.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0gHmpGjJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATRDOdBFGd73v2LKwJWePfQ.png) 

<figcaption>图 1 —气候浏览器 ETL 流程</figcaption>

</figure>

ETL 过程的总体目标是将数据从其源获取到可操作的数据库中。ETL 过程中的每个阶段都有一个特定的目标和一组约束:

*   *提取* —从 NOAA 站点获取并准备每日气候观测数据，以预测故障并支持自动恢复。
*   *转换* —清理数据，并以支持快速加载到操作数据库的方式组织数据。
*   *Load* —从临时区域填充操作数据库。加载业务数据库必须高效，并且必须支持增量加载，因为新的天气观测每天都会到达。

#### 提取程序

提取程序使用[文件传输协议](https://en.wikipedia.org/wiki/File_Transfer_Protocol) (FTP)从 NOAA 网站检索每日天气观测文件，如下所示:

<figure>[![](img/fc770bce3da816d21c15407ad71ab1d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VPmKtt_q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AkqkVaf-1xpPVAvKOclkRKQ.png) 

<figcaption>图 2 — ETL 提取流程</figcaption>

</figure>

提取从从检查点检索最近完成的文件的条目开始。检查点条目定义了在先前运行期间遇到的每个文件的状态，并用于确定在当前执行中从哪个文件开始。

*   文件名
*   修改日期
*   ETL 状态—提取完成、转换完成和加载完成

起点将是下列之一，按优先顺序排列:

1.  最近启动但未完成的检查点条目。如果所有三个 ETL 状态标志都被禁用，就会出现这种情况。
2.  以前没有遇到过的文件名
3.  修改日期比其检查点条目更新的文件。

对于临时区域的初始加载，该过程可以使用配置选项来基于经过的时间或文件计数控制运行的持续时间。一旦初始数据加载完成，该过程可被安排为根据需要运行，以添加由观测站创建的新的或修改的文件。

提取过程的最后一步特别重要，因为它确保了如果当前运行中断，下一次运行将重复处理当前文件。

有一个考虑到边缘情况的健壮的错误处理策略是至关重要的。例如，如果在检测到错误时没有通知 DevOps 团队采取纠正措施，可能会导致不良的副作用，例如在每次运行时重复处理相同的文件。

#### 转换程序

转换过程负责在以下步骤中分析添加到临时区域的原始数据:

<figure>[![](img/d9b366870ee38a5360adaee204d95cba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--znww1Hbj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/601/1%2Ah4Sw8z0XRM-GRzWzQoTIbw.png) 

<figcaption>图 3 — ETL 转换过程</figcaption>

</figure>

转换过程的主要目标不仅是确保文档和字段有效，而且是尽可能自动纠正错误。这可能包括修改源数据，在没有纠正措施时排除文档，记录错误，以及在发生灾难性故障时通知适当的人员。

这包括确保文档之间的关系在每个关系的两端都包含正确的共享字段值，以准备加载到 SQL 操作数据库中。

#### 加载程序

ETL 过程的最后一步是将暂存的数据传输到操作数据库。这听起来可能是一个简单的任务，但是操作数据库是一个 SQL DBMS，所以数据的插入方式必须考虑到表之间的关系。

因为 SQL 关系是基于共享列值的，所以新数据的插入从父表行开始，然后是相关的子表行。随着表之间关系的数量和复杂性的增加，这可能会变得很困难。

考虑图 4 所示的情况。由于*区域*表是*客户*和*销售代表*表的父表，并且由于*客户*和*销售代表*之间存在一对多关系，相关行被添加到*区域*表，然后是*销售代表*，最后是*客户*。试图在关联的父*区域*或*销售代表*之前插入*客户*行将导致违反外键约束。

<figure>[![](img/58f3e2bb66ede6fb81dec2bb7aa7464f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oWOCr45i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/404/1%2As1Atfp5D1SYZv5YIidaqiQ.png) 

<figcaption>图 4 —应用数据库设计示例</figcaption>

</figure>

图 5 展示了一个更复杂的例子，其中一个表 *Part* 参与了一个自身的嵌套关系。这种关系也被称为[物料清单](https://en.wikipedia.org/wiki/Bill_of_materials)关系。换句话说，一个表包含由其他子组件和部件组成的多个子组件。

<figure>[![](img/0f3e8536bdc9297f98d643b5aa59228e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DjZ6iPun--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/947/1%2As9B8bkftryjz5CT1HpMI3w.png) 

<figcaption>图 BOM 数据库设计示例</figcaption>

</figure>

在这个例子中，将行插入到*部分*表中可以通过两种方式之一来执行。

第一种方法是按照产品和组件对输入记录进行分组，这种方法允许从“自下而上”地建立关系，以确保不会在父记录之前添加子记录。首先加载没有子组件的部件，然后加载作为父部件的子组件，依此类推，直至关系链。

<figure>[![](img/b8e9e234c28f3175c760438d31a5edaa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pvFDSAz9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/447/1%2ATM5nH5nl4wpQIV3AzFsr9g.png) 

<figcaption>图 BOM 零件表插入顺序</figcaption>

</figure>

第二个选项是删除*零件*表上的关系，插入新行，然后重新建立关系。尽管初始数据加载可行，但需要停机并因此影响用户的事实使其成为最后的选择。

幸运的是，Climate Explorer 中的实体关系相对简单，所以这种情况并不适用。

### 设计暂存数据库

<figure>[![](img/cae30ea9bdbcacc01145639463e5143e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CpMVnVmd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/922/1%2Adpx6cHgPdKwF_LFYqKJoHA.png) 

<figcaption>图 7 —暂存数据库 ER 图</figcaption>

</figure>

Climate Explorer 的[暂存数据库](https://en.wikipedia.org/wiki/Staging_(data))使用一组 MongoDB 集合，其中导入的文件类型映射到唯一的集合。例如，ghcnd-stations.txt 被加载到电台集合中。图 7 显示了临时数据库集合及其逻辑关系。

包含暂存数据库定义的 MongoDB 档案位于 Github 上的气候浏览器 [repo](https://github.com/jdmedlock/climateexplorer/tree/feature/06-article-part3) 中的[这里](https://github.com/jdmedlock/climateexplorer/blob/feature/06-article-part3/server/src/artifacts/mongodb/climateexplorer_mongodump_archive_20190201_085700)。

#### 数据库规范化

包含来自站点的天气数据的每日文件包括多达 31 个观测值的阵列，每个月的每一天一个。添加到临时数据库观测值中的数据分为两个集合-父集合标识观测站、年和月，子集合标识每个每日观测值。

还为*站*、*观测*和*每日气象*文件中使用的代码定义了集合。不像 NOAA 文件那样依赖于`readme.txt`中的文档，查找表为编码字段提供了权威的定义。例如，*网络*表定义了*站*表中使用的网络代码。

没有任何东西禁止将信息不加修改地导入临时区域。相反，我们选择了规范化暂存数据，以简化在 ETL 加载阶段将数据加载到操作数据库的过程。

有关规范化的更多信息，请参见下面的“设计操作数据库”一节。

#### 模式验证

<figure>[![](img/92b7b674fd1eb66a343888b970714977.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6IruuuRx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/821/1%2AtIchb_ObD_kB6Ge1npa2Ug.png) 

<figcaption>图 8 —国家集合的模式验证器</figcaption>

</figure>

使用在每个集合上定义的 MongoDB [模式验证器](https://docs.mongodb.com/manual/core/schema-validation/index.html)来验证文档更新和插入。气候浏览器集合中的验证器只执行字段类型检查。字段内容的分析留给转换逻辑，因为目标是通过自动更正错误来限制被拒绝的记录的数量。

#### 关系，嵌入文档，&索引

因为 NOAA 数据的状态在这个时候是未知的，所以我们选择不在暂存数据库中使用关系或嵌入文档。必须首先对数据进行分析和清理，以确保逻辑关系的有效性。

在关系的“多”侧和基于文档量的“一”侧为所有文档字段定义索引，如*站*和*观察*文档的情况。

#### 额外文档字段

提取过程依赖于 MongoDB 事务，根据是否发生错误来提交或回滚整个文件。因为没有记录级恢复/重启的使用案例，所以需要定义数据从何处导入或何时导入的文档字段。

### 设计运营数据库

<figure>[![](img/01c00992400a32875727dd4a4c0080a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FT5aOpAn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/957/1%2AjdzzKE_lNeofhApBN2oW0Q.png) 

<figcaption>图 9 —作战数据库 DB 图</figcaption>

</figure>

[操作数据库](https://en.wikipedia.org/wiki/Operational_database)的职责是支持 Climate Explorer 满足用户查询的能力。该数据库是气候数据的永久存放地，并作为一组 PostgreSQL 表和关系来实现。

定义操作数据库的 PostgreSQL 脚本位于 Github 上气候浏览器[报告](https://github.com/jdmedlock/climateexplorer/tree/feature/06-article-part3)中的[这里](https://github.com/jdmedlock/climateexplorer/tree/feature/06-article-part3/server/src/artifacts/postgresql)。

#### 更新&指数

禁止更新气候数据，因为[记录系统](https://en.wikipedia.org/wiki/System_of_record)是 NOAA 全球历史气候网络。因此，操作数据库是为快速查询而不是更新而设计的。

索引是在关系中的父列和子列上定义的。没有创建额外的索引，但是以后会根据性能要求添加新的索引。

#### 数据库规范化

大部分的[数据库规范化](https://en.wikipedia.org/wiki/Database_normalization)过程是在临时数据库的设计过程中执行的。这是由这样一个假设驱动的，即与减少临时数据库的加载时间相比，最小化操作数据库的加载时间具有更高的优先级。

### 回顾——向前失败

#### 使用 FTP 检索文件

如前所述，提取过程使用文件传输协议(FTP)从 NOAA 网站检索文件。NPM 包 [promise-ftp](https://www.npmjs.com/package/promise-ftp) 提供了 ftp 协议的接口，中间件模块 FTPAPI.js 提供了它和 app 之间的接口。

建立到数据源的连接需要从 Apollo 上下文对象中的数据源创建一个 FTP 会话，如下所示。

```
// Data sources required by the resolvers. These are available to 
// subclasses of DataSource via config.context.
const ftpSession = new FTPAPI( {
  host\_url: process.env.NOAA\_FTP\_URL,
  host\_port: process.env.NOAA\_FTP\_PORT,
  user: process.env.NOAA\_FTP\_USER,
  password: process.env.NOAA\_FTP\_PASSWORD
} );

const mongo = new MongoAPI();
const postgres = new PostgresAPI();
const country = new Country(mongo);
const location = new Location(postgres);
const user = new User(mongo);

const dataSources = () => ({
  ftpSession: ftpSession,
  country : country,
  location: location,
  user: user,
});

.
    .
    .

// Create and start the Apollo Server
const server = new ApolloServer({
  typeDefs,
  dataSources,
  context,
  resolvers,
}); 
```

Countries.js 中的以下示例演示了如何从源代码中检索文件。

```
async extractCountriesFromGhcnd() {
  const ftpSession = this.context.dataSources.ftpSession;
  return new Promise(function (resolve, reject) {
    ftpSession.connect()
    .then(() => {
      return ftpSession.getFile(`${process.env.NOAA_FTP_GHCN_DIRECTORY}/${process.env.NOAA_FTP_COUNTIES_FILE}`
      );
    })
    .then(stream => {
      let countries = '';
      stream.on('readable', function() {
        let data;
        while (data = this.read()) {
          countries += data.toString();
        }
        resolve(countries);
        ftpSession.disconnect();
      });
    })
    .catch(err => {
      throw new Error('FTP from NOAA failed.')
    });
  })
  .then(countries => {
    const countriesObject = this.convertToObject(countries);
    return countriesObject;
  })
  .catch(err => {
    throw new Error('FTP from NOAA failed.');
  });
} 
```

#### 清理暂存数据库

应该何时从临时区域删除数据？一旦将数据成功添加到操作数据库中，就没有理由在临时数据库中保留数据。两种情况会对此产生影响:

*   *负载故障*。如果加载失败，将每个文件的加载包装在 SQL 事务中会强制回滚所有插入，从而强制要求所有插入必须成功才能提交。
*   *坏数据*。即使加载文件可能成功，也有可能在以后发现完整性问题。可以有把握地认为，这种情况发生的概率会随着时间的推移而降低。

基于此，暂存数据将保留到下一次加载运行开始。

### 包装完毕

<figure>[![](img/4c43c5557cf2811e705e934a44357cca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fUncu_F9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A143qCDpww31xSFrimYJlqg.jpeg) 

<figcaption>照片由[米卡·鲍梅斯特](https://unsplash.com/photos/Wpnoqo2plFA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[下](https://unsplash.com/search/photos/data-diagram?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

数据库设计的目的是以一种易于访问和操作的方式组织数据。开发可靠的数据库设计需要将应用程序需求和数据流与 DBMS 提供的功能相匹配。本文遵循这种方法来完成临时数据库和操作数据库的设计。

正如敏捷方法所规定的，已知的需求已经被考虑在内，还没有作为需求实现的假设和“蓝天”想法已经被记录下来，但是没有付诸行动。这意味着，如果现有需求发生变化或出现新的需求，就有必要进行修订。

毫无疑问，我们将在下一篇实现 ETL 过程的文章中遇到这种情况。准备好一些代码吧！

***接下来—第四部分，提取、转换、&加载数据***

* * *