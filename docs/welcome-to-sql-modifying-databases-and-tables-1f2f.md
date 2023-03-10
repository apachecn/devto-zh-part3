# 欢迎使用 SQL:修改数据库和表

> 原文：<https://dev.to/hackersandslackers/welcome-to-sql-modifying-databases-and-tables-1f2f>

[![Welcome to SQL: Modifying Databases and Tables](img/6e129390b39a9bf6a6d962e15a5f7e37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IhPNdNNq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackers.nyc3.cdn.digitaloceanspaces.com/posts/2019/02/SQLpt1.jpg)

SQL:我们都假装自己是这方面的专家，多亏了 StackOverflow，我们才得以逃脱。加上我们在 90 年代学习如何编码的丰富经验，PHPMyAdmin 和 LAMP stacks 的实地工作基本上使我们成为专家。继续为你的简历写下一个胜利。

SQL 出现的时间比我们的职业生涯还要长，那么为什么现在要开始一个关于它的系列文章呢？当然有足够多的文档，我们可以随时在谷歌上搜索细节来编写查询？朋友们，这正是问题所在。不管我们手头有什么工具，有些技能最好用心学习和练习。SQL 就是这些技能之一。

当然，SQLAlchemy 可能会保护我们免于编写原始查询。考虑到 SQL 只是我们经常使用的许多查询语言中的一种(除了 NoSQL、GraphQL、JQL 等等)。)，成为 SQL 专家真的那么关键吗？简而言之，是的:关系数据库不仅存在，而且将查询中的*思维*作为第二语言巩固了一个人对数据细节的理解。谷歌搜索我们想要运行的每个查询可能会完成工作，但我们会剥夺自己对正在发生的事情的更好理解。也就是说，加入更多的 SQL 教程总是值得的:这是你应该从里到外了解的事情之一。

## 关系数据库术语

我讨厌信息材料一开始就覆盖明显的术语定义。在正常情况下，我觉得这是陈词滥调，没有帮助，而且有损作者的可信度；但这不是正常情况。在 SQL 中，词汇通常有多种含义，这取决于上下文，甚至取决于您使用的是哪种风格的数据库。鉴于这一事实，个人完全有可能(也很常见)积累关系数据库的经验，同时完全误解基本概念。让我们确保这不会发生:

*   **数据库**:每个数据库实例都在最高级别上被分成*数据库*。是的，数据库是数据库的集合——我们已经有了一个良好的开端。
*   **模式**:在 PostgreSQL(和其他数据库)中，*模式*是一组表和其他对象，包括视图、关系等。模式是一种组织数据的方式。模式意味着所有属于它的数据在某种形式上都是相关的，即使只是概念上的。注意，术语*模式*有时根据上下文来描述其他概念。
*   **表**:关系数据库的肉和土豆。表格由行和列组成，它们保存着我们甜蜜的数据。列最好被认为是“属性”，而行是由所述属性的值组成的条目。一列中的所有值必须共享相同的数据类型。
    *   **密钥**:密钥用于帮助我们组织和优化数据，以及对进入的数据进行某些限制(例如，用户账户的电子邮件地址必须是*唯一的*)。键还可以帮助我们记录条目，自动确保值的唯一性，并提供链接多个数据表的桥梁。
    *   **主键**:每行数据的识别标签。关系数据库中的每条记录的主键都是不同的；必须提供值，并且这些值在行之间必须是唯一的。
    *   **外键**:启用主数据库表和其他相关数据库之间的数据搜索和操作。
*   **Objects** :存在于模式中的任何东西(包括关系)的总称(有点 PostgreSQL 特有)。
    *   **视图(PostgreSQL)** :视图以类似于表格的方式显示数据，区别在于视图不*存储*数据。视图是以查询形式从其他表中提取的数据的快照；考虑视图的一个好方法是将它们视为“虚拟表”
    *   **Functions (PostgreSQL)** :与为重用而保存的数据进行交互的逻辑。

在 MySQL 中，**模式**与**数据库**同义。在 MySQL 中，这些关键字甚至可以交换使用模式和数据库。因此，使用`CREATE SCHEMA`与代替`CREATE DATABASE`具有相同的效果。

## 导航和创建数据库

我们必须从某个地方开始，所以最好从数据库管理开始。无可否认，这将是我们将要讨论的最没用的东西。浏览数据库的行为最适合 GUI。

### 显示数据库

如果您通过命令行 shell 访问您的数据库(出于某种原因)，第一件合乎逻辑的事情就是列出可用的数据库:

```
SHOW DATABASES;

+--------------------+
| Database |
+--------------------+
| classicmodels |
| information_schema |
| mysql |
| performance_schema |
| sys |
+--------------------+
5 rows in set (0.00 sec) 
```

Enter fullscreen mode Exit fullscreen mode

### 使用数据库

既然我们已经列出了可以连接到的数据库，我们可以探究每个数据库包含的内容。为此，我们必须指定要连接到哪个数据库，也就是“use”

```
db> USE database_name;
Database changed 
```

Enter fullscreen mode Exit fullscreen mode

### 创建数据库

创建数据库很简单。创建数据库时，一定要注意*字符集*:这将决定数据库能够接受哪些类型的字符。例如，如果我们试图将特殊编码的字符插入到一个简单的 utf-8 数据库中，这些字符不会像我们预期的那样出现。

```
CREATE DATABASE IF NOT EXISTS database_name
CHARACTER SET utf-8
[COLLATE collation_name] 
```

Enter fullscreen mode Exit fullscreen mode

额外收获:创建一个数据库并显示结果的简写如下:

```
SHOW CREATE DATABASE database_name; 
```

Enter fullscreen mode Exit fullscreen mode

## 创建和修改表格

在自动化数据导入时，通过 SQL 语法创建表可能很关键。当创建一个表时，我们还要设置列名、类型和键:

```
CREATE TABLE [IF NOT EXISTS] table_name (
   column_name_1 [COLUMN_DATA_TYPE] [KEY_TYPE] [KEY_ATTRIBUTES] DEFAULT [DEFAULT_VALUE],
   column_name_2 [COLUMN_DATA_TYPE] [KEY_TYPE] [KEY_ATTRIBUTES] DEFAULT [DEFAULT_VALUE],
   PRIMARY KEY (column_name_1)
) ENGINE=[ENGINE_TYPE]; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们希望在查询中包含验证，可以在创建表时指定`IF NOT EXISTS`。如果存在，仅当指定名称的表不存在时，才会创建该表。

在创建我们的每一列时，我们可以为每一列指定许多事情:

*   **数据类型(必选):**可以保存到该列单元格中的数据(如整数、文本等)。
*   **键类型:**为列创建一个键。
*   **关键属性:**任何与关键相关的属性，如自动递增。
*   **默认值:**如果在表中创建的行没有传递到当前列的值，则该值指定为`DEFAULT`
*   **主键:**允许将任何先前指定的列设置为表的主键。

MySQL 表可以有一个通过`ENGINE=[engine_type]`指定的“存储引擎”,它决定了表如何解释数据的核心逻辑。将此字段留空默认为 InnoDB，几乎可以肯定的是，不去管它是没问题的。如果你感兴趣，你可以在这里找到更多关于 MySQL 引擎的信息。

下面是一个实际的`CREATE TABLE`查询的例子:

```
CREATE TABLE IF NOT EXISTS awards (
   id INTEGER PRIMARY KEY AUTO_INCREMENT,
   recipient TEXT NOT NULL,
   award_name TEXT DEFAULT 'Grammy',
   PRIMARY KEY (id)
) ENGINE=INNODB; 
```

Enter fullscreen mode Exit fullscreen mode

### 管理现有表格的关键字

如果我们没有在创建表时指定我们的键，我们总是可以在事后指定。SQL 表可以接受以下键类型:

*   **主键:**唯一标识表中记录的一个或多个字段/列。它不能接受空的、重复的值。
*   **候选关键字:**候选关键字有点像未提交的主键组；这些键只接受唯一的值，如果需要的话，*可能会用*代替主键，但不是真正的主键。与主键不同，每个表中可能存在多个候选键。
*   **备选键:**指单个候选键(能够满足主键 id 需求的备选键)。
*   **复合/复合键:**由多列的值组合定义；其总和将总是产生唯一的值。一个表中可以有多个候选键。每个候选键都可以作为主键。
*   **唯一键:**表中一个或多个字段/列的集合，唯一标识数据库表中的记录。类似于主键，但它只能接受一个空值，并且不能有重复值。
*   **外键:**外键表示作为*另一个表的*主键的字段。外键对于建立表之间的关系很有用。虽然外键在作为主表的父表中是必需的，但在旨在与另一个表相关的表中，外键可以为 null 或空。

让我们来看一个示例查询，在这个查询中，我们向一个表添加一个键，并分解各个部分:

```
ALTER TABLE table_name
ADD FOREIGN KEY foreign_key_name (column_name)
REFERENCES parent_table(columns)
ON DELETE { NO ACTION | CASCADE | SET NULL | SET DEFAULT }
ON UPDATE { NO ACTION | CASCADE | SET NULL | SET DEFAULT } 
```

Enter fullscreen mode Exit fullscreen mode

`ALTER TABLE`用于对一个表的结构进行任何修改，无论是修改列还是键。

在这个例子中，我们`ADD`了一个恰好是`FOREIGN KEY`的键。虽然键总是引用列，但键本身必须有自己的名称，以区分列的数据和键的概念逻辑。我们将我们的键命名为`foreign_key_name`，并用`(column_name)`指定该键将作用于哪一列。因为这是一个外键，我们需要指定我们希望它与哪个表的*主键*相关联。`REFERENCES parent_table(primary_key_column)`声明该表中的外键对应于名为`parent_table`的表中名为`primary_key_column`的列中保存的值。

语句`ON DELETE`和`ON UPDATE`是分别在父表的主键被删除或更新时发生的动作。`ON DELETE CASCADE`如果相应的主键消失，将导致表的外键被删除。

### 添加列

添加列遵循我们在创建表时使用的语法。一个有趣的附加特性是能够将新列放在先前存在的列之前或之后:

```
ALTER TABLE table
ADD COLUMN column_name [DATA_TYPE] [FIRST|AFTER existing_column]; 
```

Enter fullscreen mode Exit fullscreen mode

### 突击测验

下面的语句使用了到目前为止我们所学的关于修改和创建表结构的所有元素。你能看出这里发生了什么事吗？

```
CREATE TABLE vendors(
    vdr_id int not null auto_increment primary key,
    vdr_name varchar(255)
)ENGINE=InnoDB;

ALTER TABLE products 
ADD COLUMN vdr_id int not null AFTER cat_id;

ALTER TABLE products
ADD FOREIGN KEY fk_vendor(vdr_id)
REFERENCES vendors(vdr_id)
ON DELETE NO ACTION
ON UPDATE CASCADE; 
```

Enter fullscreen mode Exit fullscreen mode

## 丢弃数据

危险区域:这是我们开始把事情搞砸的地方。删除列或表会导致数据完全丢失:无论何时看到“删除”这个词，都要害怕。

如果你确定你知道你在做什么，并且想要删除一个表格列，可以这样做:

```
ALTER TABLE table
DROP column; 
```

Enter fullscreen mode Exit fullscreen mode

删除表格会破坏表格结构以及其中的所有数据:

```
DROP TABLE table_name; 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，截断表将清除表中的数据，但保留表本身:

```
TRUNCATE TABLE table_name; 
```

Enter fullscreen mode Exit fullscreen mode

### 删除外键

像表和列一样，我们也可以放下键:

```
ALTER TABLE table_name 
DROP FOREIGN KEY constraint_name; 
```

Enter fullscreen mode Exit fullscreen mode

## 使用视图(特定于 PostgreSQL)

最后，让我们探索创建视图的行为。PostgreSQL 可以处理三种类型的视图:

*   **简单视图**:代表底层表数据的虚拟表。简单视图是可自动更新的:系统允许在视图上使用 INSERT、UPDATE 和 DELETE 语句，就像在普通表上一样。
*   物化视图(Materialized Views):PostgreSQL 将视图的概念扩展到下一个层次，允许视图‘物理地’存储数据，我们称这些视图为物化视图。实体化视图缓存复杂查询的结果，然后允许您定期刷新结果。
*   **递归视图**:递归视图不深究复杂就有点难解释(但是很酷！)递归报告的功能。我不会深入细节，但是这些视图能够表示深入多层的关系。这里有一个快速的味道，如果你；你很好奇:

**样本** `RECURSIVE` **查询:**

```
WITH RECURSIVE reporting_line AS (
 SELECT
 employee_id,
 full_name AS subordinates
 FROM
 employees
 WHERE
 manager_id IS NULL
 UNION ALL
 SELECT
 e.employee_id,
 (
 rl.subordinates || ' > ' || e.full_name
 ) AS subordinates
 FROM
 employees e
 INNER JOIN reporting_line rl ON e.manager_id = rl.employee_id
) SELECT
 employee_id,
 subordinates
FROM
 reporting_line
ORDER BY
 employee_id; 
```

Enter fullscreen mode Exit fullscreen mode

**输出:**

```
 employee_id | subordinates
-------------+--------------------------------------------------------------
           1 | Michael North
           2 | Michael North > Megan Berry
           3 | Michael North > Sarah Berry
           4 | Michael North > Zoe Black
           5 | Michael North > Tim James
           6 | Michael North > Megan Berry > Bella Tucker
           7 | Michael North > Megan Berry > Ryan Metcalfe
           8 | Michael North > Megan Berry > Max Mills
           9 | Michael North > Megan Berry > Benjamin Glover
          10 | Michael North > Sarah Berry > Carolyn Henderson
          11 | Michael North > Sarah Berry > Nicola Kelly
          12 | Michael North > Sarah Berry > Alexandra Climo
          13 | Michael North > Sarah Berry > Dominic King
          14 | Michael North > Zoe Black > Leonard Gray
          15 | Michael North > Zoe Black > Eric Rampling
          16 | Michael North > Megan Berry > Ryan Metcalfe > Piers Paige
          17 | Michael North > Megan Berry > Ryan Metcalfe > Ryan Henderson
          18 | Michael North > Megan Berry > Max Mills > Frank Tucker
          19 | Michael North > Megan Berry > Max Mills > Nathan Ferguson
          20 | Michael North > Megan Berry > Max Mills > Kevin Rampling
(20 rows) 
```

Enter fullscreen mode Exit fullscreen mode

### 创建视图

创建一个简单的视图就像编写一个标准查询一样简单！所有需要做的就是在查询前添加`CREATE VIEW view_name AS`，这将为我们创建一个保存的位置，以便我们总是返回并引用该查询的结果:

```
CREATE VIEW comedies AS
    SELECT *
    FROM films
    WHERE kind = 'Comedy'; 
```

Enter fullscreen mode Exit fullscreen mode

## 走出去，开始 SQLing

我强烈鼓励任何人养成*总是*手工编写 SQL 查询的习惯。有了合适的 GUI，自动补全可以成为你最好的朋友。

明确地强迫自己编写查询，而不是复制粘贴任何东西，迫使我们去实现，比如 SQL 的操作顺序。事实上，这个查询拥有正确的语法...

```
SELECT *
FROM table_name
WHERE column_name = 'Value'; 
```

Enter fullscreen mode Exit fullscreen mode

...而这个没有:

```
SELECT *
WHERE column_name = 'Value'
FROM table_name; 
```

Enter fullscreen mode Exit fullscreen mode

掌握 SQL 的微妙之处是极快和几乎毫无头绪之间的区别。好消息是，你将开始发现这些概念并不像他们曾经看起来的那样令人生畏，所以从“糟糕的数据工程师”到“专家”是一个容易的胜利，如果不这样做将是愚蠢的。

下一次我们实际处理数据的地方是在 **SQL:续集**中，等级为 PG-13。