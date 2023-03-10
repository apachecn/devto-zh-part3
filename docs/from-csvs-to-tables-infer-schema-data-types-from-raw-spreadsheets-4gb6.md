# 从 CSV 到表:从原始电子表格推断模式数据类型

> 原文：<https://dev.to/hackersandslackers/from-csvs-to-tables-infer-schema-data-types-from-raw-spreadsheets-4gb6>

[![From CSVs to Tables: Infer Schema Data Types From Raw Spreadsheets](img/b37a5954f0a2546abf6508293b0d22fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1meiwnj---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res-2.cloudinary.com/hackers-and-slackers/image/upload/f_auto%2Cq_auto/img/schema.jpg)

回到去年 8 月(大约 8 个月前)，我凌晨 4 点趴在办公桌前，不顾一切地在第二天早上登机前发了一篇文章。这篇文章的标题是 ***创建数据库模式:机器人的工作，或者熊猫*** 。当时我的目的是解决一个常见的问题:用原始数据创建数据库表，而不需要显式设置每一列的数据类型这一讨厌的过程。我有一些线索让我相信我找到了答案...男孩，我错了。

从表面上看，这项任务似乎有些合理。当然，我们可以发现数据总是整数的列，或者匹配日期的预期格式，对吗？如果有的话，我们将退回到**文本**或 **varchar** 然后就到此为止。见鬼，即使是 MongoDB 的 Compass 仅仅通过上传一个 CSV 文件也做得很好...到目前为止，这一定是由第三方库处理的一些琐碎的任务。

出于这样或那样的原因，寻找这个问题的解决方案几乎总是一无所获。如果软件开发人员的应用程序只在自定义模型上运行，他们可能不需要动态生成的表。全职数据科学家可以接触到大量昂贵的工具，这些工具似乎声称具有这种功能，但这一切似乎都是如此...无法接近。

## 这不是熊猫的工作吗？

从我的经验来看，没有。熊猫确实给人们带来了希望，但似乎并没有很好地完成任务。让我们从数据集开始，这样你就能明白我的意思了。这里有一堆假身份，我将用它们来模拟我在处理真实数据时所经历的结果:

| 身份证明（identification） | 开始 | 你在说什么 | 电子邮件 | 西方人名的第一个字 | 姓 | 标题 | 部门 | 位置 | 国家 | 类型 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| One hundred million thirty-five thousand four hundred and thirty-five | 2015-12-11T09:16:20.722-08:00 | 3/22/67 | [gretchen rmorrow @ day fast . com](mailto:GretchenRMorrow@jourrapide.com) | 格雷琴(女子名ˌ等于 Margaret) | 翌日 | 电厂运行员 | 实物产品 | 百年灵自助餐厅 | 联合王国 | 雇员 |
| One hundred million fifty-six thousand four hundred and thirty-five | 2015-12-15T10:11:24.604-08:00 | 6/22/99 | [ElizabethLSnow@armyspy.com](mailto:ElizabethLSnow@armyspy.com) | 伊丽莎白一世 | 雪 | 氧气治疗师 | 实物产品 | 甲级投资 | 美利坚合众国 | 雇员 |
| One hundred million thirty-seven thousand nine hundred and fifty-five | 2015-12-16T14:31:32.765-08:00 | 5/31/74 | [albertmpeterson @ un mot . com](mailto:AlbertMPeterson@einrot.com) | 艾伯特 | 彼得森 | 心理学家 | 实物产品 | 基层庭院服务 | 美利坚合众国 | 雇员 |
| One hundred million thirty-five thousand four hundred and thirty-five | 2016-01-20T11:15:47.249-08:00 | 9/9/69 | [JohnMLynch@dayrep.com](mailto:JohnMLynch@dayrep.com) | 约翰 | 以私刑处死 | 环境水文学家 | 实物产品 | 瓦卡莫的家乡 | 美利坚合众国 | 雇员 |
| One hundred million fifty-seven thousand six hundred and fifty-seven | 2016-01-21T12:45:38.261-08:00 | 4/9/83 | [TheresaJCahoon @ teleworm . us](mailto:TheresaJCahoon@teleworm.us) | 特丽萨 | 卡洪 | 私人厨师 | 实物产品 | 卡拉食品公司 | 美利坚合众国 | 雇员 |
| One hundred million fifty-six thousand seven hundred and forty-seven | 2016-02-01d 11:25:39.317-08:00 | 6/26/98 | [KennethHPayne@dayrep.com](mailto:KennethHPayne@dayrep.com) | 肯尼思(男子名) | 佩恩（姓氏） | 中心局操作员 | 前线 | 麦格纳咨询公司 | 美利坚合众国 | 雇员 |
| One hundred million thirty-five thousand four hundred and thirty-five | 2016-02-01T11:28:11.953-08:00 | 4/16/82 | [雷夫鱼@斑点.胡](mailto:LeifTSpeights@fleckens.hu) | 叶小开 | 斯贝茨 | 员工发展总监 | 前线 | 里维拉物业维护 | 美利坚合众国 | 雇员 |
| One hundred million thirty-five thousand four hundred and thirty-five | 2016-02-01T12:21:01.756-08:00 | 8/6/80 | [JamesSRobinson @ teleworm . us](mailto:JamesSRobinson@teleworm.us) | 詹姆斯 | 鲁宾逊 | 调度办事员 | 前线 | 折扣家具陈列柜 | 美利坚合众国 | 雇员 |
| One hundred million seventy-four thousand six hundred and eighty-eight | 2016-02-01T13:29:19.147-08:00 | 12/14/74 | [annadmoberty @ day rapid . com](mailto:AnnaDMoberly@jourrapide.com) | 安娜；安那（anna 旧时印度货币名） | 莫贝里 | 剧作家 | 实物产品 | 巫师 | 美利坚合众国 | 雇员 |
| One hundred million six hundred and sixty-five thousand seven hundred and seventy-eight | 2016-02-04T14:40:05.223-08:00 | 9/13/66 | [MarjorieBCrawford@armyspy.com](mailto:MarjorieBCrawford@armyspy.com) | 马乔里（女子名） | 克劳馥 | 法院、市政和执照办事员 | 实物产品 | 意外收获下降 | 联合王国 | 雇员 |
| One hundred million eight hundred and seventy-six thousand eight hundred and seventy-six | 2016-02-24T12:39:25.872-08:00 | 12/19/67 | [莱尔克](mailto:LyleCHackett@fleckens.hu) | 莱尔 | 哈克特 | 机身机械师 | 实物产品 | 无限投资计划 | 美利坚合众国 | 雇员 |
| One hundred million six hundred and fifty-eight thousand five hundred and sixty-five | 2016-02-29T15:52:12.933-08:00 | 11/17/83 | [marjdensmore @ day fast . com](mailto:MaryJDensmore@jourrapide.com) | 玛丽 | Densmore | 雇主关系代表 | 前线 | 抢先一步的房地产经纪人 | 美利坚合众国 | 雇员 |
| One hundred million seven hundred and sixty-six thousand five hundred and forty-seven | 2016-03-01T12:32:53.357-08:00 | 10/1/87 | [是一个二进制的@ army py . com](mailto:CindyRDiaz@armyspy.com) | 辛迪(女子名ˌCynthia 的昵称) | 迪亚斯 | 学生事务管理员 | 实物产品 | AG 先生的 | 美利坚合众国 | 雇员 |
| One hundred million forty-five thousand six hundred and seventy-seven | 2016-03-02T12:07:44.264-08:00 | 8/16/65 | [安德里亚斯特伦@ un lot . com](mailto:AndreaTLigon@einrot.com) | 安德里亚 | Ligon | 铁路工程师 | 中心增长 | 罗宾逊家具 | 美利坚合众国 | 雇员 |

这里有一些有趣的数据类型:**整数**，**时间戳**，**日期**，**字符串**....而这些只是前四列！让我们把这个东西加载到一个数据帧中，看看这样我们能得到什么信息:

```
import pandas as pd

csv = 'data/fake.csv'

workers_df = pd.read_csv(csv, header=0, encoding='utf-8')
meta = workers_df.info(verbose=True)
print(meta) 
```

用熊猫的`info()`应该能行！这将返回列及其数据类型的列表:

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 22 entries, 0 to 21
Data columns (total 11 columns):
id 14 non-null float64
initiated 14 non-null object
hiredate 14 non-null object
email 14 non-null object
firstname 14 non-null object
lastname 14 non-null object
title 14 non-null object
department 14 non-null object
location 14 non-null object
country 14 non-null object
type 14 non-null object
dtypes: float64(1), object(10)
memory usage: 2.0+ KB
None 
```

...或者不是。这是什么垃圾？我们的 11 列中只有一列标识了数据类型，并且它被错误地列为 **float** ！好吧，也许熊猫对此没有秘密的俏皮话。那么谁知道呢？

## py spark 呢？

我们转向 Apache 的老数据科学产品系列只是时间问题。Hadoop，Spark，Kafka...它们都有一股特别的霉味，尝起来就像“我觉得我现在应该用 Java 写东西。”注意:他们*确实*希望你用 Java 编写。同病相怜。

尽管如此， **PySpark** *确实支持将数据作为 Python 中的数据帧读取，并且还附带了难以捉摸的推断模式的能力。在本地安装 Hadoop 和 Spark 对于解决这个特殊问题来说还是有点糟糕。Cue **[Databricks](https://databricks.com/)** :一家很久以前从 Apache 团队分离出来的公司，提供免费的集成了——你猜对了——Spark 的云笔记本。*

通过 Databricks，我们可以上传 CSV 文件，并通过旋转免费笔记本将其加载到数据框中。源代码大概是这样的:

```
# File location and type
file_location = "/FileStore/tables/fake.csv"
file_type = "csv"

# CSV options
infer_schema = "true"
first_row_is_header = "true"
delimiter = ","

df = spark.read.format(file_type) \
  .option("inferSchema", infer_schema) \
  .option("header", first_row_is_header) \
  .option("sep", delimiter) \
  .load(file_location)

display(df) 
```

让我们看看输出结果:

```
df:pyspark.sql.dataframe.DataFrame
id:integer
initiated:timestamp
hiredate:string
email:string
firstname:string
lastname:string
title:string
department:string
location:string
country:string
type:string 
```

还不错！我们正确地将我们的 ID 从浮点数“升级”为整数，并且我们还设法获得了正确的时间戳。如果我们事先声明了格式，稍加修改，我们甚至可以得到正确的日期。

[![From CSVs to Tables: Infer Schema Data Types From Raw Spreadsheets](img/2bab46d42e22906d1c78f692eb41cf4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DN2zONP2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res-4.cloudinary.com/hackers-and-slackers/image/upload/f_auto%2Cq_auto/img/Screen-Shot-2019-01-22-at-8.41.30-PM.png) 

<figcaption>一看 Databricks 的笔记本界面。</figcaption>

### 然而，这仍然有点糟糕

即使我们可以在笔记本中解决我们的问题，我们仍然没有解决用例:我想要一个嵌入式解决方案来从 CSV 创建表...只要我想！我想在编写任何应用程序时，在没有任何警告的情况下，立即完成这一任务。我不想安装 Hadoop，让 Java 错误通过我的终端返回给我。永远不要让我在我的终端上看到 Java。呃:

```
py4j.protocol.Py4JJavaError: An error occurred while calling o43.count.
: java.lang.IllegalArgumentException: Unsupported class file major version 55
        at org.apache.xbean.asm6.ClassReader.<init>(ClassReader.java:166)
        at org.apache.xbean.asm6.ClassReader.<init>(ClassReader.java:148)
        at org.apache.xbean.asm6.ClassReader.<init>(ClassReader.java:136)
        at org.apache.xbean.asm6.ClassReader.<init>(ClassReader.java:237)
        at org.apache.spark.util.ClosureCleaner$.getClassReader(ClosureCleaner.scala:49)
        at org.apache.spark.util.FieldAccessFinder$$anon$3$$anonfun$visitMethodInsn$2.apply(ClosureCleaner.scala:517)
        at org.apache.spark.util.FieldAccessFinder$$anon$3$$anonfun$visitMethodInsn$2.apply(ClosureCleaner.scala:500)
        at scala.collection.TraversableLike$WithFilter$$anonfun$foreach$1.apply(TraversableLike.scala:733)
        at scala.collection.mutable.HashMap$$anon$1$$anonfun$foreach$2.apply(HashMap.scala:134)
        at scala.collection.mutable.HashMap$$anon$1$$anonfun$foreach$2.apply(HashMap.scala:134)

        at scala.collection.mutable.HashTable$class.foreachEntry(HashTable.scala:236)
        at scala.collection.mutable.HashMap.foreachEntry(HashMap.scala:40)
        at scala.collection.mutable.HashMap$$anon$1.foreach(HashMap.scala:134)
        at scala.collection.TraversableLike$WithFilter.foreach(TraversableLike.scala:732)
        at org.apache.spark.util.FieldAccessFinder$$anon$3.visitMethodInsn(ClosureCleaner.scala:500)
        at org.apache.xbean.asm6.ClassReader.readCode(ClassReader.java:2175)
        at org.apache.xbean.asm6.ClassReader.readMethod(ClassReader.java:1238)
        at org.apache.xbean.asm6.ClassReader.accept(ClassReader.java:631) 
```

## Python 的“表模式”库

谢天谢地，至少还有一个人分享了这个愿望。这就把我们带到了 [tableschema](https://github.com/frictionlessdata/tableschema-py) ，这是一个不太完美但也许足够好的库，可以像某种野蛮的数据牛仔一样处理数据。让我们试一试:

```
import csv
from tableschema import Table

data = 'data/fake.csv'
schema = infer(data, limit=500, headers=1, confidence=0.85)
print(schema) 
```

如果我们的数据集特别大，我们可以使用`limit`属性将样本大小限制在第一个 **X** 行数。另一个很好的特性是`confidence`属性:一个 0-1 的比率，允许在推断过程中出现类型转换错误。以下是返回的结果:

```
{
  "fields": [{
    "name": "id",
    "type": "integer",
    "format": "default"
  }, {
    "name": "initiated",
    "type": "string",
    "format": "default"
  }, {
    "name": "hiredate",
    "type": "date",
    "format": "default"
  }, {
    "name": "email",
    "type": "string",
    "format": "default"
  }, {
    "name": "firstname",
    "type": "string",
    "format": "default"
  }, {
    "name": "lastname",
    "type": "string",
    "format": "default"
  }, {
    "name": "title",
    "type": "string",
    "format": "default"
  }, {
    "name": "department",
    "type": "string",
    "format": "default"
  }, {
    "name": "location",
    "type": "string",
    "format": "default"
  }, {
    "name": "country",
    "type": "string",
    "format": "default"
  }, {
    "name": "type",
    "type": "string",
    "format": "default"
  }],
  "missingValues": [""]
} 
```

嘿，那对我来说够好了！现在让我们自动化这狗屎了。

## 用我们的新模式在 SQLAlchemy 中创建一个表

我要在你面前扔一堆。这里有一个怪物类:

```
from sqlalchemy import create_engine
import config
import pandas as pd
import psycopg2
from tableschema import Table, infer, Schema
from functions.recursivejson import extract_values
from sqlalchemy.types import Integer, Text, Date

class CreateTablesFromCSVs:
    """Infer a table schema from a CSV."""

    __uri = config.PG_URI
    __engine = create_engine(__uri, convert_unicode=True, echo=True)
    __data = 'data/fake.csv'

    @classmethod
    def get_data(cls):
        """Pull latest data."""
        test_df = pd.read_csv(cls.__data, header=0, encoding='utf-8')
        return test_df

    @classmethod
    def get_schema_from_csv(cls, csv):
        """Infers schema from CSV."""
        table = Table(csv)
        table.infer(limit=500, confidence=0.55)
        schema = table.schema.descriptor
        names = cls.get_column_names(schema, 'name')
        datatypes = cls.get_column_datatypes(schema, 'type')
        schema_dict = dict(zip(names, datatypes))
        return schema_dict

    @classmethod
    def get_column_names(cls, schema, key):
        """Get names of columns."""
        names = extract_values(schema, key)
        return names

    @classmethod
    def get_column_datatypes(cls, schema, key):
        """Convert schema to recognizable by SQLAlchemy."""
        values = extract_values(schema, key)
        for i, value in enumerate(values):
            if value == 'integer':
                values[i] = Integer
            elif value == 'string':
                values[i] = Text
            elif value == 'date':
                values[i] = Date
        return values

    @classmethod
    def create_new_table(cls, data, schema):
          """Create new table from CSV and generated schema."""
          workday_table.to_sql('faketable',
                               con=cls.__engine,
                               schema='testschema',
                               if_exists='replace',
                               chunksize=300,
                               dtype=schema)

data = CreateTablesFromCSVs.get_schema_from_csv()
schema = CreateTablesFromCSVs.get_schema_from_csv(data)
CreateTablesFromCSVs.create_new_table(data, schema) 
```

首先值得一提的是，我正在从我的个人秘密库中导入一个函数来从 JSON 对象中提取值。我在之前已经[谈过了。](https://hackersandslackers.com/extract-data-from-complex-json-python/)

让我们来分解这个类:

*   将我们的 CSV 读入熊猫数据帧。
*   开始构建一个模式，SQLAlchemy 可以用它来构建一个表。
*   简单地提取列名作为我们模式的一半。
*   `get_column_datatypes()`手动替换我们从 **tableschema** 收到的数据类型名称，并用 SQLAlchemy 数据类型替换它们。
*   使用 Pandas 和 SQLAlchemy 之间的完美结合在我们的数据库中创建一个表，并映射正确的数据类型。

### 有发展潜力，有成长空间

虽然 **tableschema** 有时可以工作，但它并不完美。我们完成的基础仍然存在:我们现在有了一个可靠的公式，如果我们相信我们的模式是准确的，我们将如何动态地创建模式。

等到下一次我们引入 Google BigQuery 时就知道了。