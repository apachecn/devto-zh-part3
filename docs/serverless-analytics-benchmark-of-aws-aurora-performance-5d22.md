# AWS Aurora 性能的无服务器分析基准

> 原文：<https://dev.to/paveltiunov/serverless-analytics-benchmark-of-aws-aurora-performance-5d22>

众所周知，由于锁和性能问题，禁止对生产 RDBMS 实例进行分析。许多现代 RDBMSs 对复制的引入使这一过程稍微容易了一些，但并不理想。这里的下一步发展是无服务器 RDBMSs。将处理能力从存储中分离出来，使我们对如何使用 RDBMS 的理解达到了一个新的水平。这为我们提供了无限扩展数据库基础架构的机会，并允许我们同时拥有 OLTP 和 OLAP 工作负载。

在本文中，我们使用 [Cube.js 无服务器分析框架](https://github.com/statsbotco/cube.js)对 AWS Aurora MySQL 进行了分析性能基准测试。

**TL；DR** :由无服务器 MySQL Aurora 运行的未优化的 100M 行测试数据集可以在 176 秒内完成查询。使用多阶段查询方法，对于相同的数据集，可以实现少于 200 毫秒的查询时间。

## 极光 MySQL 设置

Aurora MySQL 的设置相当简单。只需在 AWS 控制台中转到您的 [RDS 服务并选择创建数据库。](https://console.aws.amazon.com/rds/home)

[![](img/7780ae8aa4e73fb4105589cc86003887.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iO99wcub--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/uAneZZ7S3GTvrD6pRwRd)

在这里，您应该选择支持无服务器部署的 Aurora 和 MySQL 兼容版本。然后选择无服务器并输入您的凭证来访问您的数据库。就是这样！您的 Aurora 实例已经配置好，可以启动了。

## 准备数据集

出于测试的目的，我们将从 [Sakila 样本数据库](https://dev.mysql.com/doc/sakila/en/)中生成一个 100M 的行数据集。为此，我们将使用稍微修改过的 Sakila 表。

首先我们来介绍一下`customer`表:

```
CREATE TABLE customer (
  customer_id SMALLINT UNSIGNED NOT NULL AUTO_INCREMENT,
  store_id TINYINT UNSIGNED NOT NULL,
  first_name VARCHAR(45) NOT NULL,
  last_name VARCHAR(45) NOT NULL,
  email VARCHAR(50) DEFAULT NULL,
  address_id SMALLINT UNSIGNED NOT NULL,
  active BOOLEAN NOT NULL DEFAULT TRUE,
  create_date DATETIME NOT NULL,
  last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY  (customer_id),
  KEY idx_fk_store_id (store_id),
  KEY idx_fk_address_id (address_id),
  KEY idx_last_name (last_name)
)ENGINE=InnoDB DEFAULT CHARSET=utf8; 
```

然后是`payment`表:

```
CREATE TABLE payment (
  payment_id SMALLINT UNSIGNED NOT NULL AUTO_INCREMENT,
  customer_id SMALLINT UNSIGNED NOT NULL,
  staff_id TINYINT UNSIGNED NOT NULL,
  rental_id INT DEFAULT NULL,
  amount DECIMAL(5,2) NOT NULL,
  payment_date DATETIME NOT NULL,
  last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY  (payment_id),
  KEY idx_fk_customer_id (customer_id),
  CONSTRAINT fk_payment_customer FOREIGN KEY (customer_id) REFERENCES customer (customer_id) ON DELETE RESTRICT ON UPDATE CASCADE
)ENGINE=InnoDB DEFAULT CHARSET=utf8; 
```

快速提示:Aurora 不能使用公共 IP 访问，只能在 VPC 访问。要从您的机器连接到它，您应该使用 VPN/SSH 隧道，或者您可以使用 AWS RDS 查询编辑器，它只在浏览器中工作。

为了填充`customer`和`payment`表，我们将使用来自 [Sakila 样本数据库](https://dev.mysql.com/doc/sakila/en/)的前 600 `customer`行和前 500 `payment`行的样本。

然后，我们将使用`payment`表为 100M `payment_big`表生成实际记录。它的定义很像`payment` :

```
CREATE TABLE payment_big (
  payment_id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
  customer_id SMALLINT UNSIGNED NOT NULL,
  staff_id TINYINT UNSIGNED NOT NULL,
  rental_id INT DEFAULT NULL,
  amount DECIMAL(5,2) NOT NULL,
  payment_date DATETIME NOT NULL,
  last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY  (payment_id),
  KEY idx_fk_customer_id (customer_id),
  CONSTRAINT fk_payment_big_customer FOREIGN KEY (customer_id) REFERENCES customer (customer_id) ON DELETE RESTRICT ON UPDATE CASCADE
)ENGINE=InnoDB DEFAULT CHARSET=utf8; 
```

由于 MySQL 没有生成器特性，我们将为此引入一些助手视图。

最多 16 位的整数生成器:

```
CREATE OR REPLACE VIEW generator_16
AS SELECT 0 n UNION ALL SELECT 1  UNION ALL SELECT 2  UNION ALL 
   SELECT 3   UNION ALL SELECT 4  UNION ALL SELECT 5  UNION ALL
   SELECT 6   UNION ALL SELECT 7  UNION ALL SELECT 8  UNION ALL
   SELECT 9   UNION ALL SELECT 10 UNION ALL SELECT 11 UNION ALL
   SELECT 12  UNION ALL SELECT 13 UNION ALL SELECT 14 UNION ALL 
   SELECT 15; 
```

256 以内的整数生成器:

```
CREATE OR REPLACE VIEW generator_256 AS SELECT ( hi.n * 16 + lo.n ) AS n FROM generator_16 lo , generator_16 hi; 
```

为了生成实际的付款，我们将使用下面的 SQL，它一次生成 2，048，000 行:

```
INSERT INTO payment_big (customer_id, staff_id, rental_id, amount, payment_date, last_update) 
SELECT customer_id, staff_id, rental_id, amount * 10 * RAND() as amount, TIMESTAMPADD(MINUTE, generator_256.n * 1000 + 1000 * RAND(), payment_date) as payment_date, last_update
FROM payment, generator_256, generator_16 
```

我们将调用它 50 次，以得到一个大约有 100M 行的表。

## Cube.js 设置

[Cube.js](https://github.com/statsbotco/cube.js) 是一个开源的分析框架，作为分析用户和数据库之间的媒介。它提供了分析 SQL 生成、查询结果缓存和执行编排、数据预聚合、安全性、用于获取查询结果的 API 以及可视化。我们将主要使用它进行缓存和预聚合，以了解 Aurora MySQL analytics 有多快，以及数据接收和从中获得洞察力之间的延迟。

要创建 Cube.js 应用程序，您需要安装 Cube.js CLI 和用于部署的无服务器 CLI:

```
$ npm install -g cubejs-cli
$ npm install -g serverless 
```

然后，我们来创建一个 app:

```
$ cubejs create aurora-benchmark -d mysql -t serverless 
```

在`serverless.yml`中，您应该定义凭证来访问您的 Aurora MySQL 和 Redis 实例，并提供 vpc 设置来访问您的资源。请[点击](https://statsbot.co/cubejs/docs/deployment)了解更多部署信息。

我们还将为我们的基准引入 Cube.js 模式定义。
T0】:

```
cube(`Customers`, {
 sql: `select * from test.customer`,

 measures: {
   count: {
     type: `count`
   }
 },

 dimensions: {
   id: {
     sql: `customer_id`,
     type: `number`,
     primaryKey: true
   },

   email: {
     sql: `email`,
     type: `string`
   },

   name: {
     sql: `${CUBE}.first_name || ${CUBE}.last_name`,
     type: `string`
   }
 }
}); 
```

`schema/Payments.js` :

```
cube(`Payments`, {
 sql: `select * from test.payment_big`,

 joins: {
   Customers: {
     sql: `${CUBE}.customer_id = ${Customers}.customer_id`,
     relationship: `belongsTo`
   }
 },

 measures: {
   count: {
     type: `count`
   },

   totalAmount: {
     sql: `amount`,
     type: `sum`
   }
 },

 dimensions: {
   id: {
     sql: `payment_id`,
     type: `number`,
     primaryKey: true
   },

   date: {
     sql: `payment_date`,
     type: `time`
   }
 }
}); 
```

这就是我们部署它所需的一切，让我们开始吧:

```
$ serverless deploy -v 
```

如果一切设置正确，您应该在您的 shell 中看到类似这样的内容:

```
Service Information
service: aurora-benchmark
stage: dev
region: us-east-1
stack: aurora-benchmark-dev
resources: 16
api keys:
  None
endpoints:
  GET - https://123456789a.execute-api.us-east-1.amazonaws.com/dev/
  ANY - https://123456789a.execute-api.us-east-1.amazonaws.com/dev/{proxy+}
functions:
  cubejs: aurora-benchmark-dev-cubejs
  cubejsProcess: aurora-benchmark-dev-cubejsProcess
layers:
  None 
```

转到您的主 GET 端点链接，看起来应该是这样的:`https://123456789a.execute-api.us-east-1.amazonaws.com/dev/`

如果一切都做对了，您应该会看到一个带有示例饼状图的代码沙箱。

让我们为`Payments`立方体创建时间序列图。为此，让我们将`moment`导入添加到`index.js` :

```
import moment from 'moment'; 
```

您应该使用蓝色的`Add Dependency`按钮或`package.json`将它添加为一个依赖项。

然后更换`renderChart`和`query` :

```
const renderChart = resultSet => (
  <Chart scale={{ category: { tickCount: 8 } }} height={400} data={resultSet.chartPivot()} forceFit>
    <Axis name="category" label={{ formatter: val => moment(val).format("MMM DD") }} />
    <Axis name="Payments.count" />
    <Tooltip crosshairs={{ type: 'y' }} />
    <Geom type="line" position="category*Payments.count" size={2} />
  </Chart>
);

const query = {
  measures: ["Payments.count"],
  timeDimensions: [{
    dimension: 'Payments.date',
    granularity: 'day',
    dateRange: ['2005-09-01', '2005-10-08']
  }]
}; 
```

编辑之后，您应该会得到一个类似于下面的`index.js`:

```
import React from "react";
import ReactDOM from "react-dom";

import cubejs from "@cubejs-client/core";
import { QueryRenderer } from "@cubejs-client/react";
import { Chart, Axis, Tooltip, Geom, Coord, Legend } from "bizcharts";
import moment from 'moment';

const API_URL = "https://123456789a.execute-api.us-east-1.amazonaws.com/dev"; // change to your actual endpoint

const renderChart = resultSet => (
  <Chart scale={{ category: { tickCount: 8 } }} height={400} data={resultSet.chartPivot()} forceFit>
    <Axis name="category" label={{ formatter: val => moment(val).format("MMM DD") }} />
    <Axis name="Payments.count" />
    <Tooltip crosshairs={{ type: 'y' }} />
    <Geom type="line" position="category*Payments.count" size={2} />
  </Chart>
);

const query = {
  measures: ["Payments.count"],
  timeDimensions: [{
    dimension: 'Payments.date',
    granularity: 'day',
    dateRange: ['2005-09-01', '2005-10-08']
  }]
};

const cubejsApi = cubejs(
  "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE1NTExODQ0NDksImV4cCI6MTU1MTI3MDg0OX0.KLkKp2pRnw9ZlrwMGkoBlpdgGy4eol7258aKVwJLPuM",
  { apiUrl: API_URL + "/cubejs-api/v1" }
);

const App = () => (
  <div style={{ textAlign: 'center', fontFamily: 'sans-serif' }}>
   <h1>Payments Count</h1>
    <QueryRenderer
      query={query}
      cubejsApi={cubejsApi}
      render={({ resultSet, error }) =>
        (resultSet && renderChart(resultSet)) ||
          (error && error.toString()) || <span>Loading...</span>
      }
    />
</div>
);

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

如果一切正常，您应该会看到类似于下图的折线图:

[![](img/77f3690a503017da63d315667fdefc74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fvKEleRv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/h4SlFFYORT6Kb0u7ofCo)

## 基准

此时，我们的`payment_big`表中有一个未优化的 100 万条记录的数据集。我们将使用无服务器日志来查看当 Cube.js 默认写入这些处理日志时，在 Aurora MySQL 端处理查询需要多长时间。要启用日志尾部，我们将使用:

```
$ serverless logs -t -f cubejsProcess 
```

如果我们请求一个月的数据，我们将得到 176 秒的处理延迟:

```
2019-02-27T12:47:45.384Z    794618d5-5eb3-40ce-88f3-cce6d75786f1    Performing query completed:
{
    "queueSize": 2,
    "duration": 175900,
    "queryKey": [
        "SELECT\n DATE_FORMAT(CONVERT_TZ(payments.payment_date, @@session.time_zone, '+00:00'), '%Y-%m-%dT00:00:00.000Z') `payments.date_date`, count(payments.payment_id) `payments.count`\n FROM\n test.payment_big AS payments\n WHERE (payments.payment_date >= TIMESTAMP(convert_tz(?, '+00:00', @@session.time_zone)) AND payments.payment_date <= TIMESTAMP(convert_tz(?, '+00:00', @@session.time_zone))) GROUP BY 1 ORDER BY 1 ASC LIMIT 10000",
        [
            "2005-09-01T00:00:00Z",
            "2005-09-30T23:59:59Z"
        ],
        []
    ]
} 
```

对于一个 100 兆数据点的未优化表来说，这还不算太坏。我们给`payment_date`加一个指标，看看能不能影响这个时间:

```
CREATE INDEX payment_big_date ON payment_big (payment_date) 
```

带有 index 的相同查询将在 31 秒内处理，这很好，但仍然不够快，不足以构建出色的用户体验。我们能做的是为这个表引入预聚合。为此，我们只需添加:

```
preAggregations: {
 main: {
   type: `rollup`,
   measureReferences: [count],
   timeDimensionReference: date,
   granularity: `day`,
   partitionGranularity: `day`
 }
} 
```

到`schema/Payments.js`这样我们就可以得到:

```
cube(`Payments`, {
 sql: `select * from test.payment_big`,

 joins: {
   Customers: {
     sql: `${CUBE}.customer_id = ${Customers}.customer_id`,
     relationship: `belongsTo`
   }
 },

 measures: {
   count: {
     type: `count`
   },

   totalAmount: {
     sql: `amount`,
     type: `sum`
   }
 },

 dimensions: {
   id: {
     sql: `payment_id`,
     type: `number`,
     primaryKey: true
   },

   date: {
     sql: `payment_date`,
     type: `time`
   }
 },

 preAggregations: {
   main: {
     type: `rollup`,
     measureReferences: [count],
     timeDimensionReference: date,
     granularity: `day`,
     partitionGranularity: `day`
   }
 }
}); 
```

它将为每天的数据创建一个汇总表，并逐步刷新它。这里`partitionGranularity`指示 Cube.js 创建每天数据的汇总表。汇总表本身将包含粒度为`day`的`count`度量和`date`维度。事实上，这里的每个汇总表将只包含一行，其中包含特定日期计算的`count`。让我们部署它，看看它如何改变查询处理时间:

```
$ serverless deploy -v 
```

如果我们请求相同的数据间隔，Cube.js 将首先为每天构建预聚合，每天需要 1.5 秒:

```
2019-02-27T13:33:19.267Z    11477db2-f66e-4278-9103-eefbbc513be3    Performing query completed:
{
    "queueSize": 1,
    "duration": 1578,
    "queryKey": [
        [
            "CREATE TABLE stb_pre_aggregations.payments_main20050928 AS SELECT\n DATE_FORMAT(CONVERT_TZ(payments.payment_date, @@session.time_zone, '+00:00'), '%Y-%m-%dT00:00:00.000Z') `payments.date_date`, count(payments.payment_id) `payments.count`\n FROM\n test.payment_big AS payments\n WHERE (payments.payment_date >= TIMESTAMP(convert_tz(?, '+00:00', @@session.time_zone)) AND payments.payment_date <= TIMESTAMP(convert_tz(?, '+00:00', @@session.time_zone))) GROUP BY 1",
            [
                "2005-09-28T00:00:00Z",
                "2005-09-28T23:59:59Z"
            ]
        ],
        [
            [
                {
                    "current_hour": "2019-02-27T13:00:00.000Z"
                }
            ]
        ]
    ]
} 
```

然后它将查询所有预聚合表的并集，耗时不到 200 毫秒:

```
2019-02-27T13:33:23.647Z    a4162f29-570d-495f-8ca4-34600869d8e7    Performing query completed:
{
    "queueSize": 1,
    "duration": 134,
    "queryKey": [
        "SELECT `payments.date_date`  `payments.date_date`, sum(`payments.count`) `payments.count` FROM (SELECT * FROM stb_pre_aggregations.payments_main20050901 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050902 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050903 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050904 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050905 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050906 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050907 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050908 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050909 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050910 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050911 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050912 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050913 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050914 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050915 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050916 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050917 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050918 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050919 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050920 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050921 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050922 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050923 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050924 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050925 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050926 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050927 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050928 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050929 UNION ALL SELECT * FROM stb_pre_aggregations.payments_main20050930) as partition_union  WHERE (`payments.date_date` >= CONVERT_TZ(TIMESTAMP(convert_tz(?, '+00:00', @@session.time_zone)), @@session.time_zone, '+00:00') AND `payments.date_date` <= CONVERT_TZ(TIMESTAMP(convert_tz(?, '+00:00', @@session.time_zone)), @@session.time_zone, '+00:00')) GROUP BY 1 ORDER BY 1 ASC LIMIT 10000",
        [
            "2005-09-01T00:00:00Z",
            "2005-09-30T23:59:59Z"
        ],
       ...
} 
```

这种方法允许我们将分析工作负载分割成小的可重用块，以避免使用增量更新策略的数据库突发。

## 结论

我们只使用了一个测试数据集，其结果与我们在生产工作负载中看到的非常吻合。MySQL 能够很好地处理每个表中几十亿个数据点的分析工作负载。在这种情况下，使用适当组织的查询编排，可以实现亚秒级的查询时间和几秒的洞察时间延迟。

尽管无服务器的 Aurora MySQL 能够在压力测试时处理巨大的工作负载，但我们发现工作负载路由算法还不够智能，无法将查询路由到负载最少的节点，这在某些情况下会导致分析查询时部分数据库中断。对于生产环境，我们建议您使用提供了读取副本的 Aurora MySQL 或[并行查询功能](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-mysql-parallel-query.html)，以便根据设计拆分您的 OLTP 和 OLAP 工作负载。