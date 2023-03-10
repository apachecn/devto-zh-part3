# 征服含有咖啡因的亚马逊雅典娜与梅蒂斯三件套

> 原文：<https://dev.to/hrbrmstr/conquering-caffeinated-amazon-athena-with-the-metis-trio-of-packages-1mgb>

我必须在这篇文章的开头提出一个假设，如果你正在用亚马逊雅典娜做任何事情，你应该认真考虑使用他们的免费 ODBC 驱动程序,因为这是将它们连接到 DBI 和 tidyverse-wise 的最简单的方法。我在之前的帖子中已经[说过这么多了。如果您不知道重新打包所提供的 Linux ODBC 驱动程序以在您的 Linux 风格上工作的咒语，请在评论中留言。](https://rud.is/b/2018/04/20/painless-odbc-dplyr-connections-to-amazon-athena-and-apache-drill-with-r-odbc/)

然而 …

有时候——比方说，当您试图在 kubernetes 集群中建立一个 R 服务，将 Athena 中的数据连接到 R 中的分析和可视化时 ODBC 驱动程序可能是一个障碍而不是帮助，而 JDBC 是阻力最小的途径。

当然，有 in-CRAN `AWR.Athena`包，但它是一个相当受约束和低功能的 RJDBC shim，它完成了基本的工作，但没有更多的。

输入:

*   [`metis.jars`](https://git.sr.ht/~hrbrmstr/metis-jars)|[GL](https://gitlab.com/hrbrmstr/metis-jars)|[GH](https://github.com/hrbrmstr/metis-jars)
*   [`metis`](https://git.sr.ht/~hrbrmstr/metis)|[GL](https://gitlab.com/hrbrmstr/metis)|[GH](https://github.com/hrbrmstr/metis)而且，
*   [`metis.tidy`](https://git.sr.ht/~hrbrmstr/metis-tidy)|[GL](https://gitlab.com/hrbrmstr/metis-tidy)|[GH](https://github.com/hrbrmstr/metis-tidy)

三个软件包，旨在当 ODBC 不可用时，使 R 连接到 Amazon Athena 变得超级简单。

### 为什么是三包？

对于初学者来说，`metis` -trio 有着巨大的希望，其中的一个关键部分是将罐子分成一个包(`metis.jars`)并将实际功能分成其他包(`metis`和`metis.tidy`)。我们将会看到 CRAN 尝试如何进行，因为 JAR 包的重量足以保证一个`NOTE`。驱动程序的打包减少了你预加载 JAR(本地加载或者加载到 Docker 镜像中)或者像`AWR.Athena`一样执行包启动的下载舞蹈的需要(我仍然不明白为什么除了 _(ツ)_/之外，它没有像 CRAN 那样做)。

`metis.jars`也有三个助手功能，做一些(基本的)有趣的事情:

```
library(metis.jars)

simba_driver_version()
## [1] "02.00.06.1008"

athena_supported_types()
## [1] "BOOLEAN" "TINYINT" "SMALLINT" "INT" "INTEGER"  
## [6] "BIGINT" "REAL" "FLOAT" "DOUBLE" "DECIMAL"  
## [11] "DATE" "TIMESTAMP" "BINARY" "VARBINARY" "CHAR"     
## [16] "VARCHAR" "STRING" "ARRAY" "MAP" "ROW"      
## [21] "STRUCT"   

metis_jar_path()
## [1] "/Library/Frameworks/R.framework/Versions/3.5/Resources/library/metis.jars/java/AthenaJDBC42_2.0.6.jar" 
```

Enter fullscreen mode Exit fullscreen mode

第一种使用`rJava`接口直接查询版本(因为亚马逊似乎一年更新两次 Simba JAR)。通过将 JAR 分离到一个单独的包中，可以更频繁地对其他两个兄弟包进行更新，而不会占用 CRAN 的磁盘空间。`metis.jars`也被版本化到包含的 JAR 中，所以配置管理对人们来说会更容易。

第二个 type-lister 函数的原因是，亚马逊有望增加对所有 Presto 数据类型的支持，尤其是`IPADDRESS`。它再次执行 JDBC 驱动程序自省来收集支持的类型。

最后，第三个函数从`metis`包甚至*你自己的*接口包中抽象出 JAR 位置，如果你选择依赖它的话。

### 好吧，但为什么不只是两个呢？

与`AWR.Athena`相比，`metis`包是 DBI 包装器的一个功能更强的 RJDBC 超类。它做的一件事，它的克兰表亲不能正确处理`BIGINT`的:

```
library(metis)

dbConnect(
  metis::Athena(),
  Schema = "sampledb",
  AwsCredentialsProviderClass = "com.simba.athena.amazonaws.auth.PropertiesFileCredentialsProvider",
  AwsCredentialsProviderArguments = path.expand("~/.aws/athenaCredentials.props")
) -> con

dbGetQuery(con, "
SELECT
  CAST('chr' AS CHAR(4)) achar,
  CAST('varchr' AS VARCHAR) avarchr,
  CAST(SUBSTR(timestamp, 1, 10) AS DATE) AS tsday,
  CAST(100.1 AS DOUBLE) AS justadbl,
  CAST(127 AS TINYINT) AS asmallint,
  CAST(100 AS INTEGER) AS justanint,
  CAST(100000000000000000 AS BIGINT) AS abigint,
  CAST(('GET' = 'GET') AS BOOLEAN) AS is_get,
  ARRAY[1, 2, 3] AS arr1,
  ARRAY['1', '2, 3', '4'] AS arr2,
  MAP(ARRAY['foo', 'bar'], ARRAY[1, 2]) AS mp,
  CAST(ROW(1, 2.0) AS ROW(x BIGINT, y DOUBLE)) AS rw,
  CAST('{\"a\":1}' AS JSON) js
FROM elb_logs
LIMIT 1
") %>% 
  dplyr::glimpse()
## Observations: 1
## Variables: 13
## $ achar <chr> "chr "
## $ avarchr <chr> "varchr"
## $ tsday <date> 2014-09-29
## $ justadbl <dbl> 100.1
## $ asmallint <int> 127
## $ justanint <int> 100
## $ abigint <S3: integer64> 100000000000000000
## $ is_get <lgl> TRUE
## $ arr1 <chr> "1, 2, 3"
## $ arr2 <chr> "1, 2, 3, 4"
## $ mp <chr> "{bar=2, foo=1}"
## $ rw <chr> "{x=1, y=2.0}"
## $ js <chr> "\"{\\\"a\\\":1}\"" 
```

Enter fullscreen mode Exit fullscreen mode

~~很快~~ Athena 数组、地图、行和 JSON 就像 Athena 驱动程序中的字符，它们的格式如此糟糕，以至于完全支持它们的`list`列几乎没有希望。*但是*，你可以用`metis`得到真正的大整数，同时完全支持所有其他当前的 Athena 类型。

可能是旧的、独立的`metis`包用户的人需要注意一些事情。

第一，`dbConnect()`有突破性的变化。在更高级的`athena_jdbc()`函数中仍然存在的 snake_case 名称已经没有了。作为对这种痛苦的交换，你现在拥有所有雅典娜 JDBC 连接属性的完全命名奇偶校验，并且可以更容易地使用替代凭证提供者，这是`metis`的堂兄[完全不能为你](https://github.com/nfultz/AWR.Athena/blob/master/R/athena.R#L77)做的，这在上面的例子和软件包自述文件中有所说明。

`metis`包也使得查看*所有*可用的 Athena 连接属性的文档变得更加容易，因为它有一个包含所有这些属性的描述性表格的小插图(这里呈现为)。

还有对“流式 API”(TLDR:更快的结果集下载)的初步支持，但在本周 AWS 政策调整之前，这不会得到充分测试。

### 明白了。但是，说真的，为什么不是两个呢？

尽管很棒(包括基本的 Docker 映像支持),`tidyverse`在编译时间和依赖性方面并不是没有开销，这两者在 Linux 系统和一些 Docker 环境中尤其令人痛苦。您完全可以使用一些精心制作的 SQL 和 JDBC，映像越薄，就越容易部署和扩展。

但是！tidyverse 是如此的有用，以至于确保对 Athena 的流畅支持是至关重要的。就其本身而言，`metis`电线高达`dplyr` / `dbplyr`好，但是通过(在`metis.tidy`中)提供一些增强的`db_data_type()`支持(主要针对`BIGINT`)和一些额外的💙)对于我们这些继续在非 R 上下文中盲目使用 R-only 动词如`grep()`或`as.POSIXct()`的人来说，我们可以升级`interactive()`使用和 tid yverse-infined 服务使用:

```
library(metis.tidy)
library(dbplyr)
library(dplyr)

metis::dbConnect(
  metis::Athena(),
  Schema = "sampledb",
  AwsCredentialsProviderClass = "com.simba.athena.amazonaws.auth.PropertiesFileCredentialsProvider",
  AwsCredentialsProviderArguments = path.expand("~/.aws/athenaCredentials.props")
) -> con

elb_logs <- tbl(con, "elb_logs")

filter(elb_logs, grepl("20", elbresponsecode)) %>%
  mutate(
    tsday = as.Date(substring(timestamp, 1L, 10L)),
    host = url_extract_host(url),
    proto_version = regexp_extract(protocol, "([[:digit:]\\.]+)"),
  ) %>%
  select(tsday, host, receivedbytes, requestprocessingtime, proto_version) %>%
  head(1) %>%
  glimpse()
## Observations: ??
## Variables: 5
## Database: AthenaConnection
## $ tsday <date> 2014-09-29
## $ host <chr> "www.abcxyz.com"
## $ receivedbytes <S3: integer64> 0
## $ requestprocessingtime <dbl> 9.5e-05
## $ proto_version <chr> "1.1" 
```

Enter fullscreen mode Exit fullscreen mode

### 鳍

这种彻底重构的一个相当大的推动力是需要在无服务器的上下文中使用 R at $DAYJOB 中的雅典娜 JDBC 接口。因此，如果我/我们需要，其他人也可能需要。所有这三个包都经过了测试(与我个人的 Athena 设置一起工作，该设置很容易复制，因为它只是启用 Athena 时得到的默认模式和表)，通过了 CRAN 检查，并且在您阅读本文时将在真实的生产环境中运行。

请注意，我确实有这三个朋友的 CRAN 计划，但所有三个包需要同时进入，我需要得到测试，并在提交之前证明测试在特拉维斯是活的。现在是功能请求、问题报告或问题的时候了。在 SourceHut 的(sr.ht) API 完成之前，这些贡献最好留给 GitLab(最好)或 GitHub(如果你必须继续为那些损害你自由的大型跨国公司收钱的话)。

### POSTSCRIPT

重访`metis`的另一个原因是[这个 R 崩溃`rJava`问题](https://github.com/s-u/RJDBC/issues/71)实际上是一个 Simba Athena 实现问题(JDBC 驱动中的操作系统信号， *rly* ？)

本`Rprofile`条目:

```
options(
  "java.parameters" = c(getOption("java.parameters", default = NULL), "-Xrs")
) 
```

Enter fullscreen mode Exit fullscreen mode

在`rJava`更新之前一直是一个可靠的解决方法。请注意，如果`metis.jars`检测到您的设置存在风险，它会在加载时发出警告。