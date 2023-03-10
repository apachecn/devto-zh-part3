# 欢迎学习 SQL 2:使用数据值

> 原文：<https://dev.to/hackersandslackers/welcome-to-sql-2-working-with-data-values-f7d>

[![Welcome to SQL 2: Working With Data Values](img/c846919bc3c454d7ed8a67cce9a6004b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HogOWxO6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackers.nyc3.cdn.digitaloceanspaces.com/posts/2019/02/SQLpt2-1-1.jpg)

现在我们已经了解了[创建数据库和表](https://dev.to/hackersandslackers/welcome-to-sql-modifying-databases-and-tables-l7m-temp-slug-5356263)的基本原理，我们可以开始研究 SQL 交互的实质内容了:**选择**、**更新**，以及**删除**数据。

我们将从这些查询的基本结构开始，然后用足够的细节来分解强大的操作，使您变得危险。

## 从表格中选择数据

如前所述，SQL 操作有一个相当严格的操作顺序，子句必须遵守这个顺序才能进行有效的查询。我们将从剖析一个常见的 SELECT 语句开始:

```
SELECT
  column_name_1,
  column_name_2
FROM
  schema_name.table_name
WHERE
  column_name_1 = "Value"; 
```

Enter fullscreen mode Exit fullscreen mode

这可能是选择查询最常见的结构。首先，我们用逗号分隔列出我们想要选择的列的名称。要接收*所有的*列，我们可以简单地说`SELECT *`。

这些列需要来自某个地方，所以我们指定接下来要引用的表。这要么采用`FROM table_name`(非 PostgreSQL)，要么采用`FROM schema_name.table_name` (PostgreSQL)的形式。理论上，这里的分号会产生一个有效的查询，但是我们通常希望选择满足特定标准的行。

这就是`WHERE`子句的用武之地:只有为我们的`WHERE`条件返回**“true”**的行将被返回。在上面的例子中，我们正在验证一个字符串是否与`"Value"`完全匹配。

### 抵消和限制我们查询中的结果

选择数据时，`OFFSET`和`LIMIT`的组合有时很关键。如果我们从一个有几十万行的数据库中进行选择，那么一次获取所有的行将会浪费大量的系统资源；相反，我们可以让应用程序或 API 对结果进行分页。

`LIMIT`后面跟一个整数，本质上是说“返回不超过 X 个结果。”

`OFFSET`后面还跟一个整数，表示返回结果的数字起点，也称为:“返回第 x 个结果:
之后出现的所有结果”

```
SELECT
 *
FROM
 table_name
LIMIT 50 OFFSET 0; 
```

Enter fullscreen mode Exit fullscreen mode

上面返回了前 50 个结果。如果我们想在应用程序端构建分页的结果，我们可以像这样构造查询:

```
from SQLAlchemy import engine, session

# Set up a SQLAlchemy session
Session = sessionmaker()
engine = create_engine('sqlite:///example.db')
Session.configure(bind=engine)
sess = Session()

# Appication variables
page_number = 3
page_size = 50
results_subset = page_number * results limit

# Query
session.query(TableName).limit(page_size).offset(results_subset) 
```

Enter fullscreen mode Exit fullscreen mode

这样的应用程序可以在用户每次点击下一页时将`page_number`加 1，然后适当地修改我们的查询以返回下一页结果。

`OFFSET`的另一个用途可能是从一个失败的脚本停止的地方开始。如果我们将整个数据库写入一个 CSV 并遇到一个故障。我们可以通过将`OFFSET`设置为等于 CSV 中的行数来从脚本停止的地方开始，以避免再次运行整个脚本。

### 排序结果

现在最后要考虑的是使用`ORDER BY`子句对结果进行排序。我们可以根据任何指定的列对结果进行排序，并说明我们希望结果是升序(`ASC`)还是降序(`DESC` ):

```
SELECT
  *
FROM
  schema_name.table_name
WHERE
  column_name_1 = "Value"
ORDER BY
  updated_date DESC
LIMIT 50 OFFSET 10; 
```

Enter fullscreen mode Exit fullscreen mode

## 复杂的 SELECT 语句

当然，我们可以用比精确匹配更深入的`WHERE`逻辑来选择行。这些操作中最通用的一个是`LIKE`。

### 使用 Regex 与 LIKE

`LIKE`可能是选择带有字符串值的列的最强大的方法。有了`LIKE`，我们可以利用正则表达式构建高度复杂的逻辑。先说一些我最喜欢的:

```
SELECT
  *
FROM
  people
WHERE
  name LIKE "%Wade%"; 
```

Enter fullscreen mode Exit fullscreen mode

将两边带有百分号的字符串传递给`LIKE`，本质上是一个“**包含**的语句。`%`相当于一个通配符，因此将`%`放在我们字符串的两边将返回 true，不管这个人的名字、中间名或姓氏是 **Wade** 。查看`%`的其他有用组合:

*   **a %**查找任何以“a”开头的值。
*   **%a:** 查找任何以“a”结尾的值。
*   **%**
*   **_ r %**查找第二个位置有“r”的任何值。
*   **a_%_%:** 查找以“a”开头且长度至少为 3 个字符的任何值。
*   **a%o:** 查找任何以“a”开头，以“o”结尾的值。

### 寻找不像的值

与`LIKE`相反的当然是`NOT LIKE`，它运行相同的条件，但是返回与`LIKE` :
相反的真/假值

```
SELECT
  *
FROM
  people
WHERE
  name NOT LIKE "%Wade%"; 
```

Enter fullscreen mode Exit fullscreen mode

### 带日期时间列的条件句

日期时间列对于选择数据非常有用。与普通字符串不同，我们可以通过分别使用`MONTH(column_name)`、`DAY(column_name)`和`YEAR(column_name)`轻松地从日期时间中提取月、日和年的数值。例如，在包含日期时间为`2019-01-26 05:42:34`的列上使用`MONTH()`将返回`1`，即一月。因为这些值以整数的形式返回，所以在日期范围内查找结果很容易:

```
SELECT 
  * 
FROM 
  posts 
WHERE YEAR(created_at) < 2018; 
```

Enter fullscreen mode Exit fullscreen mode

### 查找具有空值的行

`NULL`是一种特殊的数据类型，本质上表示“缺少某些东西”，因此 no conditional 永远不会*等于* `NULL`。相反，我们找到值为`IS NULL` :
的行

```
SELECT 
  * 
FROM 
  posts 
WHERE author IS NULL; 
```

Enter fullscreen mode Exit fullscreen mode

对于熟悉数据类型验证的人来说，这并不奇怪。

当然，与此相反的是`NOT NULL` :

```
SELECT 
  * 
FROM 
  posts 
WHERE author IS NOT NULL; 
```

Enter fullscreen mode Exit fullscreen mode

## 插入数据

一个`INSERT`查询创建一个新行，并且相当简单:我们声明我们想要插入数据的列，然后是要插入到所述列中的值:

```
INSERT INTO table_name (column_1, column_2, column_3)
VALUES ("value1", "value2", "value3"); 
```

Enter fullscreen mode Exit fullscreen mode

许多事情都可能导致插入失败。首先，值的数量必须与我们指定的列数相匹配；如果我们不这样做，我们提供的值要么太少，要么太多。

其次，值必须考虑列的数据类型。如果我们试图将一个整数插入到一个**日期时间**列中，我们将会收到一个错误。

最后，我们必须考虑表的键和约束。如果存在指定某些列不能为空或者必须是唯一的键，则也必须考虑这些键。

作为一个简单的技巧，如果我们将值插入到一个表的所有列中，我们可以跳过明确列出列名的部分:

```
INSERT INTO table_name
VALUES ("value1", "value2", "value3"); 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个使用真实数据的插入查询的快速示例:

```
INSERT INTO friends (id, name, birthday) 
VALUES (1, 'Jane Doe', '1990-05-30'); 
```

Enter fullscreen mode Exit fullscreen mode

## 更新记录:基础知识

更新行是有趣的地方。这里有太多我们可以做的事情，所以让我们一步一步来

```
UPDATE table_name 
SET column_name_1 = 'value' 
WHERE column_name_2 = 'value'; 
```

Enter fullscreen mode Exit fullscreen mode

这再简单不过了:在与我们的条件匹配的行中，列的值。注意`SET`总是在`WHERE`之前。下面是使用真实数据的相同查询:

```
UPDATE celebs 
SET twitter_handle = '@taylorswift13' 
WHERE id = 4; 
```

Enter fullscreen mode Exit fullscreen mode

## 更新记录:有用的逻辑

### 使用 CONCAT 连接字符串

您会发现，根据已经存在于行中的数据来更新行是一种常见的做法:换句话说，清理或修改数据。一个很棒的字符串操作符是`CONCAT()`。`CONCAT("string_1", "string_2")`将所有的字符串连接起来传递给一个单独的字符串。

下面是一个使用`CONCAT()`和`NOT LIKE`来确定哪些文章节选不以标点符号结尾的真实例子。如果节选没有以标点符号结尾，我们在结尾加上一个句号:

```
UPDATE
  posts
SET 
  custom_excerpt = CONCAT(custom_excerpt, '.')
WHERE
  custom_excerpt NOT LIKE '%.'
  AND custom_excerpt NOT LIKE '%!'
  AND custom_excerpt NOT LIKE '%?'; 
```

Enter fullscreen mode Exit fullscreen mode

### 使用替换

在 SQL 中工作，就像在几乎所有编程语言中一样。我们传递`REPLACE()`三个值:

1.  要修改的字符串。
2.  字符串中将被替换的子字符串。
3.  替换的价值。

我们可以用`REPLACE()`做很多聪明的事情。这是一个将博客文章的特色图片更改为包含“retina image”后缀的示例:

```
UPDATE
  posts
SET
  feature_image = REPLACE(feature_image, '.jpg', '@2x.jpg'); 
```

Enter fullscreen mode Exit fullscreen mode

### 场景:基于日期的文件夹结构

前几天，我在处理一个涉及更换 cdn 的噩梦般的情况时，遇到了一个有趣的练习。它涉及到了我们到目前为止已经讨论过的所有内容，并且很好地展示了 SQL 本身可以实现的功能。

为数百篇文章移动数百张图片的挑战来自文件结构的形式。Ghost 喜欢把图片保存在有日期的文件夹结构里，像 **2019/02/image.jpg** 。我们以前的 CDN 根本没有遵守这一点，所以在一个文件夹中有所有图像的转储。不理想。

幸运的是，我们可以利用帖子的元数据来辨别这种文件结构。因为图片是在文章创建时添加到文章中的，所以我们可以使用文章表中的 **created_at** 列来找出日期正确的文件夹:

```
UPDATE
  posts
SET
  feature_image = CONCAT("https://cdn.example.com/posts/", 
        YEAR(created_at),
         "/", 
         LPAD(MONTH(created_at), 2, '0'), 
         "/",
         SUBSTRING_INDEX(feature_image, '/', - 1)
     ); 
```

Enter fullscreen mode Exit fullscreen mode

让我们来分解一下`CONCAT`中的内容:

*   我们新 CDN 的基本 URL。
*   `YEAR(created_at)`:从我们的帖子创建日期中提取年份(对应于一个文件夹)。
*   `LPAD(MONTH(created_at), 2, '0')`:使用 **MONTH(created_at)** 返回一位数的前几个月，但是我们的文件夹结构希望月份总是两位数的(即: **2018/01/** 而不是 **2018/1/** )。我们可以在这里使用`LPAD()`来“填充”我们的日期，这样月份总是两位数长，较短的日期将被填充数字 0。
*   我们通过查找现有图片 URL 中最后一个斜杠之后的所有内容来获取每篇文章图片的文件名。

每个图像的结果现在看起来像这样:

```
https://cdn.example.com/posts/2018/02/image.jpg 
```

Enter fullscreen mode Exit fullscreen mode

## 删除记录

让我们用最后一种类型的查询来结束今天，删除行:

```
DELETE FROM celebs 
WHERE twitter_handle IS NULL; 
```

Enter fullscreen mode Exit fullscreen mode