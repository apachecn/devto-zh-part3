# 在 Athena 上使用拼花地板以节省 AWS 的资金

> 原文：<https://dev.to/cloudforecast/using-parquet-on-athena-to-save-money-on-aws-3fac>

*最初发表于[cloudforecast.io/blog](https://cloudforecast.io/blog/)T3】*

为了向您展示如何优化您的 Athena 查询并节省资金，我们将使用运输统计局([bts.gov](bts.gov))的“2018 航班准点表现”数据集。在使用 Athena 时，我们还将从数据集中删除一些关于美国✈️queried 机场的有趣事实。

本帖中使用的所有数据集和查询都可以在我们的 [Github repo](https://github.com/CloudForecast/blog-posts) 中找到。

**让我们从一些术语开始**

> **Amazon Athena** 是一种交互式查询服务，使用标准 SQL 可以轻松分析亚马逊 S3 的数据。定价基于每次查询扫描的数据量。
> 
> **Apache Parquet** 是一种列存储格式，适用于 Hadoop 生态系统中的任何项目，无论选择何种数据处理框架、数据模型或编程语言。

如果定价基于扫描的数据量，您应该始终使用以下技术之一优化数据集以处理最少量的数据:压缩、分区和使用列式文件格式。我们将展示压缩和使用列格式的好处。

使用压缩将减少 Athena 扫描的数据量，也将减少您的 S3 存储。这是一个双赢的 AWS 法案。支持的格式: *GZIP、LZO、SNAPPY(拼花)和 ZLIB。*

列格式不是使用行级别的方法，而是按列存储数据。这允许 Athena 只查询和处理所需的列，而忽略其余的。如果您想了解更多关于列的知识，请查看 Maxim Zaks 的文章 [Wikipedia](%5Bhttps://en.wikipedia.org/wiki/RCFile%5D(https://en.wikipedia.org/wiki/RCFile)) 和/或“[面向列的数据之美](https://towardsdatascience.com/the-beauty-of-column-oriented-data-2945c0c9f560)

#### 熟悉数据

在使用“2018 航班准点性能”数据集时，我们将使用三种不同类型的文件来比较处理后数据的性能:CSV、GZip 和 Parquet 文件。

从运输统计局下载的文件是简单的 CSV 文件，有 23 列(如航班日期、航空公司、航班号、始发地、目的地、延误、取消、...)

相同的数据，3 个版本:

*   **Raw CSV:**
    *   *描述*:没有压缩，只是一组普通的 CSV 文件
    *   文件:12 ~55MB 文件(每月一个)
    *   总数据集大小:~666MBs
*   **GZip CSV** :
    *   *描述*:使用 GZip 压缩的简单 CSV 文件。
    *   *文件* : 12 ~10MB 的 Gzipped CSV 文件(每月一个)。
    *   *总数据集大小*:约 126MBs
*   **压缩拼花地板**:
    *   *描述*:我们用 Spark 把 CSV 文件转换成拼花。同样的过程也可以用 [AWS 胶水](https://aws.amazon.com/glue/)来完成
    *   *文件:* 12 ~8MB 拼花文件使用默认压缩([爽快](https://github.com/google/snappy))。
    *   *总数据集大小:*~ 84mb

在我们的 [Github repo](https://github.com/CloudForecast/blog-posts/tree/master/athena-parquet) 上找到三个数据集版本。

#### 创建各种表格

由于各种格式和/或压缩是不同的，每个`CREATE`语句需要向 Athena 指出它应该使用哪种格式/压缩。

##### 原始 CSV

```
CREATE EXTERNAL TABLE IF NOT EXISTS flights.raw_data (

    `year` SMALLINT,

    `month` SMALLINT,

    `day_of_month` SMALLINT,

    `flight_date` STRING,

    `op_unique_carrier` STRING,

    `flight_num` STRING,

    `origin` STRING,

    `destination` STRING,

    `crs_dep_time` STRING,

    `dep_time` STRING,

    `dep_delay` DOUBLE,

    `taxi_out` DOUBLE,

    `wheels_off` STRING,

    `arr_delay` DOUBLE,

    `cancelled` DOUBLE,

    `cancellation_code` STRING,

    `diverted` DOUBLE,

    `air_time` DOUBLE,

    `carrier_delay` DOUBLE,

    `weather_delay` DOUBLE,

    `nas_delay` DOUBLE,

    `security_delay` DOUBLE,

    `late_aircraft_delay` DOUBLE

)

ROW FORMAT DELIMITED

FIELDS TERMINATED BY ','

ESCAPED BY '\"'

LINES TERMINATED BY '\n'

LOCATION 's3://INSERT_BUCKET_NAME/raw'

TBLPROPERTIES (

    'skip.header.line.count'='1',

    'serialization.null.format'=''

); 
```

##### 
  
gzip CSV

Athena 自动检测 gzip 格式(基于“.gz”后缀)，这样我们可以重用上面的查询。完整查询[见此处](https://github.com/CloudForecast/blog-posts/tree/master/athena-parquet)。

##### 拼花文件使用上口

使用`STORED AS PARQUET`和`"parquet.compress"="SNAPPY"`，Athena 将能够完美地处理我们的数据。

```
CREATE EXTERNAL TABLE IF NOT EXISTS flights.parquet_snappy_data (

    `year` SMALLINT,

    `month` SMALLINT,

    `day_of_month` SMALLINT,

    `flight_date` STRING,

    `op_unique_carrier` STRING,

    `flight_num` STRING,

    `origin` STRING,

    `destination` STRING,

    `crs_dep_time` STRING,

    `dep_time` STRING,

    `dep_delay` DOUBLE,

    `taxi_out` DOUBLE,

    `wheels_off` STRING,

    `arr_delay` DOUBLE,

    `cancelled` DOUBLE,

    `cancellation_code` STRING,

    `diverted` DOUBLE,

    `air_time` DOUBLE,

    `carrier_delay` DOUBLE,

    `weather_delay` DOUBLE,

    `nas_delay` DOUBLE,

    `security_delay` DOUBLE,

    `late_aircraft_delay` DOUBLE

)

STORED AS PARQUET

LOCATION 's3://INSERT_BUCKET_NAME/parquet'

tblproperties ("parquet.compress"="SNAPPY") 
```

#### 
  
挑选几个查询来玩

##### 查询# 1:2018 年美国热门航线以及为其提供航班的航空公司数量。

<sub>**剧透提醒**:芝加哥奥黑尔国际机场到纽约*拉瓜迪亚*有 6 家独特的航空公司提供路线</sub>

```
SELECT 

    origin, 

    destination, 

    count(*) as total_flights, 

    count(distinct op_unique_carrier) as uniq_airlines

FROM flights.parquet_snappy_data

GROUP BY origin, destination

ORDER BY total_flights DESC

LIMIT 1 
```

##### 
  
查询#2:为避免任何重大延误而飞离的最佳机场(> 30 分钟)

<sub>**剧透:檀香山国际机场的航班延误率不到 4%，给你一个游览夏威夷的理由。纽瓦克机场平均有 16.2%的航班延误(> 30 分钟)。**</sub>

```
SELECT

    origin,

    percent_delayed as "% Delayed (> 30)",

    total_flights as "Total Flights"

FROM (

    SELECT

        origin,

        DENSE_RANK() OVER (ORDER BY percent_delayed desc) AS worst_rank,

        DENSE_RANK() OVER (ORDER BY percent_delayed asc) AS top_rank,

        percent_delayed,

        total_flights

    FROM (

        SELECT

            origin,

            sum(CAST (dep_delay > 30 as DOUBLE)) / count(*) as percent_delayed,

            count(*) as total_flights

        FROM flights.parquet_snappy_data

        GROUP BY 1

        HAVING COUNT(*) > 10000

    ) as t1

) as t2

WHERE top_rank <= 1

OR worst_rank <= 3 
```

#### 
  
对比表演

对于这一类别，我将每个查询运行 10 次，然后计算平均值。我们将使用*原始*数据集作为基线。

*   **查询#1**
    *   *Raw*:2.60 秒扫描 665.71 MB。每个查询 0.00333 美元
    *   *GZip*:2.08 秒扫描 125.54 MB。每个查询 0.00063 美元(节省 81%)。
    *   *拼花*:1.26 秒扫描 8.29 MB。每次查询 0.000050 美元( **-节省 98%**)。
*   **查询#2**
    *   *Raw*:2.60 秒扫描 665.71 MB。每个查询 0.00333 美元。
    *   *GZip*:2.08 秒扫描 125.54 MB。每个查询 0.00063 美元(节省 81%)。
    *   *拼花*:0.81 秒扫描 8.29 MB。每次查询 0.000062 美元( **-节省 98%**)。

#### 结论

由于 Parquet 的列格式，Athena 只读取查询中需要的列。这使得查询时间减少了超过 50+% ，查询价格减少了 98%**。**

*如果你对 Athena、Parquet 或 CloudForecast 有任何疑问，欢迎通过电子邮件[Francois @ cloud forecast . io](mailto:francois@cloudforecast.io)或 Twitter: [@francoislagier](https://twitter.com/francoislagier) 联系我。***