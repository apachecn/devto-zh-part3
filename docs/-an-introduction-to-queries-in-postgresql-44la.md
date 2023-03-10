# PostgreSQL 中查询的介绍

> 原文：<https://dev.to/digitalocean/-an-introduction-to-queries-in-postgresql-44la>

### 简介

数据库是许多网站和应用程序的关键组成部分，是如何在互联网上存储和交换数据的核心。数据库管理的一个最重要的方面是从数据库中检索数据的实践，无论是在特定的基础上，还是作为编码到应用程序中的过程的一部分。从数据库中检索信息有几种方法，但最常用的方法之一是通过命令行提交*查询*来执行。

在关系数据库管理系统中，*查询*是用来从表中检索数据的任何命令。在结构化查询语言(SQL)中，几乎总是使用`SELECT`语句进行查询。

在本指南中，我们将讨论 SQL 查询的基本语法以及一些更常用的函数和运算符。我们还将练习使用 PostgreSQL 数据库中的一些样本数据进行 SQL 查询。

PostgreSQL ，通常简称为“Postgres”，是一个采用面向对象方法的关系数据库管理系统，这意味着信息可以在 PostgreSQL 模式中表示为对象或类。PostgreSQL 与标准 SQL 非常接近，尽管它也包含一些其他关系数据库系统中没有的特性。

## 先决条件

一般来说，本指南中介绍的命令和概念可以在运行任何 SQL 数据库软件的任何基于 Linux 的操作系统上使用。然而，它是专门针对运行 PostgreSQL 的 Ubuntu 18.04 服务器编写的。要进行设置，您需要以下内容:

*   拥有 sudo 权限的非 root 用户的 Ubuntu 18.04 机器。这可以使用我们的[Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04?utm_source=devto&utm_medium=display&utm_campaign=DO_Dev_Awareness_Cold_Devto_2019)初始服务器设置指南来设置。
*   计算机上安装的 PostgreSQL。要获得设置的帮助，请阅读我们指南的“安装 PostgreSQL”部分，了解如何在 Ubuntu 18.04 上安装和使用 PostgreSQL。

有了这个设置，我们可以开始教程。

## 创建样本数据库

在开始使用 SQL 进行查询之前，我们将首先创建一个数据库和几个表，然后用一些示例数据填充这些表。这将使您在以后开始查询时获得一些实践经验。

对于我们将在本指南中使用的示例数据库，想象以下场景:

你和你的几个朋友一起庆祝你的生日。每一次，小组成员前往当地的保龄球馆，参加一场友谊比赛，然后每个人都前往你的住处，在那里你为过生日的人准备最喜欢的饭菜。

现在这个传统已经延续了一段时间，你决定开始追踪这些锦标赛的记录。此外，为了使计划晚餐更容易，你决定创建一个记录，记录你朋友的生日以及他们最喜欢的主菜、配菜和甜点。您决定通过在 PostgreSQL 数据库中记录这些信息来锻炼您的数据库技能，而不是将这些信息保存在物理分类帐中。

首先，作为您的 **postgres** 超级用户:
打开 PostgreSQL 提示符

```
sudo -u postgres psql 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**如果您遵循了先决条件教程中关于[在 Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04?utm_source=devto&utm_medium=display&utm_campaign=DO_Dev_Awareness_Cold_Devto_2019) 上安装 PostgreSQL 的所有步骤，您可能已经为您的 PostgreSQL 安装配置了一个新角色。在这种情况下，您可以使用以下命令连接到 Postgres 提示符，用您自己的用户名:
替换`sammy`

```
sudo -u sammy psql 
```

Enter fullscreen mode Exit fullscreen mode

接下来，通过运行:
创建数据库

```
CREATE DATABASE birthdays; 
```

Enter fullscreen mode Exit fullscreen mode

然后通过键入:
选择该数据库

```
\c birthdays 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在这个数据库中创建两个表。我们将使用第一个表来跟踪您的朋友在保龄球馆的记录。下面的命令将创建一个名为`tourneys`的表，其中包含您每个朋友的`name`、他们赢得的锦标赛次数(`wins`)、他们的历史记录`best`以及他们穿多大号的保龄球鞋(`size` ):

```
CREATE TABLE tourneys ( 
name varchar(30), 
wins real, 
best real, 
size real 
); 
```

Enter fullscreen mode Exit fullscreen mode

一旦您运行了`CREATE TABLE`命令并用列标题填充它，您将收到以下输出:

```
Output
CREATE TABLE 
```

Enter fullscreen mode Exit fullscreen mode

用一些示例数据填充`tourneys`表:

```
INSERT INTO tourneys (name, wins, best, size) 
VALUES ('Dolly', '7', '245', '8.5'), 
('Etta', '4', '283', '9'), 
('Irma', '9', '266', '7'), 
('Barbara', '2', '197', '7.5'), 
('Gladys', '13', '273', '8'); 
```

Enter fullscreen mode Exit fullscreen mode

您将收到以下输出:

```
Output
INSERT 0 5 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在同一个数据库中创建另一个表，我们将使用它来存储关于您朋友最喜欢的生日餐的信息。下面的命令创建一个名为`dinners`的表，其中包含您的每个朋友的`name`、他们的`birthdate`、他们最喜欢的`entree`、他们最喜欢的`side`菜以及他们最喜欢的`dessert` :

```
CREATE TABLE dinners ( 
name varchar(30), 
birthdate date, 
entree varchar(30), 
side varchar(30), 
dessert varchar(30) 
); 
```

Enter fullscreen mode Exit fullscreen mode

同样，对于该表，您将收到确认该表已创建的反馈:

```
Output
CREATE TABLE 
```

Enter fullscreen mode Exit fullscreen mode

用一些示例数据填充这个表:

```
INSERT INTO dinners (name, birthdate, entree, side, dessert) 
VALUES ('Dolly', '1946-01-19', 'steak', 'salad', 'cake'), 
('Etta', '1938-01-25', 'chicken', 'fries', 'ice cream'), 
('Irma', '1941-02-18', 'tofu', 'fries', 'cake'), 
('Barbara', '1948-12-25', 'tofu', 'salad', 'ice cream'), 
('Gladys', '1944-05-28', 'steak', 'fries', 'ice cream'); 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
INSERT 0 5 
```

Enter fullscreen mode Exit fullscreen mode

一旦该命令成功完成，您就完成了数据库的设置。接下来，我们将回顾一下`SELECT`查询的基本命令结构。

## 理解选择报表

正如简介中提到的，SQL 查询几乎总是以`SELECT`语句开始。`SELECT`在查询中用于指定结果集中应该返回表中的哪些列。查询也几乎总是包含`FROM`，它用于指定语句将查询哪个表。

通常，SQL 查询遵循以下语法:

```
SELECT column_to_select FROM table_to_select WHERE certain_conditions_apply; 
```

Enter fullscreen mode Exit fullscreen mode

举例来说，下面的语句将从`dinners`表中返回整个`name`列:

```
SELECT name FROM dinners; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
  name   
---------
 Dolly
 Etta
 Irma
 Barbara
 Gladys
(5 rows) 
```

Enter fullscreen mode Exit fullscreen mode

您可以从同一个表中选择多个列，用逗号分隔它们的名称，如下:

```
SELECT name, birthdate FROM dinners; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
  name   | birthdate  
---------+------------
 Dolly   | 1946-01-19
 Etta    | 1938-01-25
 Irma    | 1941-02-18
 Barbara | 1948-12-25
 Gladys  | 1944-05-28
(5 rows) 
```

Enter fullscreen mode Exit fullscreen mode

您可以在`SELECT`运算符后面加上一个星号(`*`)，作为表示表中所有列的占位符，而不是命名一个特定的列或一组列。以下命令返回`tourneys`表中的每一列:

```
SELECT * FROM tourneys; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
  name   | wins | best | size 
---------+------+------+------
 Dolly   |    7 |  245 |  8.5
 Etta    |    4 |  283 |    9
 Irma    |    9 |  266 |    7
 Barbara |    2 |  197 |  7.5
 Gladys  |   13 |  273 |    8
(5 rows) 
```

Enter fullscreen mode Exit fullscreen mode

`WHERE`在查询中用于筛选符合指定条件的记录，任何不符合该条件的行将从结果中删除。一个`WHERE`子句通常遵循以下语法:

```
. . . WHERE column_name comparison_operator value 
```

Enter fullscreen mode Exit fullscreen mode

`WHERE`子句中的比较运算符定义了指定列应该如何与值进行比较。以下是一些常见的 SQL 比较运算符:

| 操作员 | 它的作用 |
| --- | --- |
| `=` | 平等测试 |
| `!=` | 不平等测试 |
| `<` | 测试小于 |
| `>` | 测试大于 |
| `<=` | 测试小于或等于 |
| `>=` | 测试大于或等于 |
| `BETWEEN` | 测试一个值是否在给定的范围内 |
| `IN` | 测试行的值是否包含在一组指定的值中 |
| `EXISTS` | 给定指定条件，测试行是否存在 |
| `LIKE` | 测试值是否与指定的字符串匹配 |
| `IS NULL` | 测试`NULL`值 |
| `IS NOT NULL` | 测试除`NULL`以外的所有值 |

例如，如果您想查找 Irma 的鞋号，您可以使用以下查询:

```
SELECT size FROM tourneys WHERE name = 'Irma'; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
 size 
------
    7
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

SQL 允许使用通配符，在`WHERE`子句中使用时尤其方便。百分号(`%`)表示零个或多个未知字符，下划线(`_`)表示单个未知字符。如果您试图在一个表中查找一个特定的条目，但不确定该条目到底是什么，那么这些就很有用。举例来说，假设您忘记了几个朋友最喜欢的主菜，但您确定这道主菜以“t”开头。您可以通过运行以下查询找到它的名称:

```
SELECT entree FROM dinners WHERE entree LIKE 't%'; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
 entree  
-------
 tofu
 tofu
(2 rows) 
```

Enter fullscreen mode Exit fullscreen mode

基于上面的输出，我们看到我们已经忘记的主菜是`tofu`。

有时，您可能会使用具有相对较长或难以阅读的名称的列或表的数据库。在这些情况下，您可以通过使用`AS`关键字创建别名来使这些名称更易读。用`AS`创建的别名是临时的，只在创建它们的查询期间存在:

```
SELECT name AS n, birthdate AS b, dessert AS d FROM dinners; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
    n    |     b      |     d     
---------+------------+-----------
 Dolly   | 1946-01-19 | cake
 Etta    | 1938-01-25 | ice cream
 Irma    | 1941-02-18 | cake
 Barbara | 1948-12-25 | ice cream
 Gladys  | 1944-05-28 | ice cream
(5 rows) 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们已经告诉 SQL 将`name`列显示为`n`，将`birthdate`列显示为`b`，将`dessert`列显示为`d`。

到目前为止，我们所学的例子包括 SQL 查询中一些更常用的关键字和子句。这些对于基本的查询很有用，但是如果您试图基于您的数据执行计算或导出一个*标量值*(单个值，而不是一组多个不同的值)，它们就没什么用了。这就是聚合函数发挥作用的地方。

## 聚合函数

通常，在处理数据时，您不一定想看到数据本身。相反，你想要的是关于数据的信息。SQL 语法包括许多函数，允许您通过发出一个`SELECT`查询来解释或运行数据计算。这些被称为*集合函数*。

`COUNT`函数计算并返回符合特定标准的行数。例如，如果你想知道你有多少朋友喜欢豆腐作为他们的生日主菜，你可以发出这个查询:

```
SELECT COUNT(entree) FROM dinners WHERE entree = 'tofu'; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
 count 
-------
     2
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

`AVG`函数返回一列的平均值。使用我们的示例表，您可以通过以下查询找到您的朋友的平均最佳分数:

```
SELECT AVG(best) FROM tourneys; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output  
 avg  
-------
 252.8
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

`SUM`用于求出给定列的总和。例如，如果你想知道这些年来你和你的朋友玩了多少游戏，你可以运行这个查询:

```
SELECT SUM(wins) FROM tourneys; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output 
 sum 
-----
  35
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`AVG`和`SUM`函数只有在使用数字数据时才能正常工作。如果您试图在非数值数据上使用它们，将会导致错误或者仅仅是`0`，这取决于您使用的 RDBMS:

```
SELECT SUM(entree) FROM dinners; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
ERROR:  function sum(character varying) does not exist
LINE 1: select sum(entree) from dinners;
               ^
HINT:  No function matches the given name and argument types. You might need to add explicit type casts. 
```

Enter fullscreen mode Exit fullscreen mode

`MIN`用于查找指定列内的最小值。您可以使用这个查询来查看到目前为止最差的保龄球记录(根据获胜次数):

```
SELECT MIN(wins) FROM tourneys; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output 
 min 
-----
   2
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

类似地，`MAX`用于查找给定列中的最大数值。下面的查询将显示最好的总体保龄球记录:

```
SELECT MAX(wins) FROM tourneys; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
 max 
-----
  13
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

与`SUM`和`AVG`不同，`MIN`和`MAX`函数可用于数字和字母数据类型。当在包含字符串值的列上运行时，`MIN`函数将按字母顺序显示第一个值:

```
SELECT MIN(name) FROM dinners; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
   min   
---------
 Barbara
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

同样，当在包含字符串值的列上运行时，`MAX`函数将按字母顺序显示最后一个值:

```
SELECT MAX(name) FROM dinners; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
 max  
------
 Irma
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

除了本节描述的以外，聚合函数还有许多用途。当与`GROUP BY`子句一起使用时，它们特别有用，这将在下一节与其他几个影响结果集排序方式的查询子句一起讨论。

## 操纵查询输出

除了`FROM`和`WHERE`子句之外，还有几个其他子句用于操作`SELECT`查询的结果。在本节中，我们将解释一些更常用的查询子句并提供示例。

除了`FROM`和`WHERE`之外，最常用的查询子句之一是`GROUP BY`子句。它通常在对一列执行聚合函数，但与另一列中的匹配值相关时使用。

例如，假设你想知道你有多少朋友喜欢你做的三道主菜。您可以通过以下查询找到该信息:

```
SELECT COUNT(name), entree FROM dinners GROUP BY entree; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
 count | entree  
-------+---------
     1 | chicken
     2 | steak
     2 | tofu
(3 rows) 
```

Enter fullscreen mode Exit fullscreen mode

`ORDER BY`子句用于对查询结果进行排序。默认情况下，数值按升序排序，文本值按字母顺序排序。为了说明这一点，下面的查询列出了`name`和`birthdate`列，但是按照出生日期对结果进行排序:

```
SELECT name, birthdate FROM dinners ORDER BY birthdate; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
  name   | birthdate  
---------+------------
 Etta    | 1938-01-25
 Irma    | 1941-02-18
 Gladys  | 1944-05-28
 Dolly   | 1946-01-19
 Barbara | 1948-12-25
(5 rows) 
```

Enter fullscreen mode Exit fullscreen mode

注意，`ORDER BY`的默认行为是按升序对结果集进行排序。要颠倒这种情况并使结果集按降序排序，请用`DESC` :
关闭查询

```
SELECT name, birthdate FROM dinners ORDER BY birthdate DESC; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
  name   | birthdate  
---------+------------
 Barbara | 1948-12-25
 Dolly   | 1946-01-19
 Gladys  | 1944-05-28
 Irma    | 1941-02-18
 Etta    | 1938-01-25
(5 rows) 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，`WHERE`子句用于根据特定条件过滤结果。但是，如果您将`WHERE`子句与聚合函数一起使用，它将返回一个错误，就像下面尝试查找哪一方是您的至少三个朋友的最爱的情况一样:

```
SELECT COUNT(name), side FROM dinners WHERE COUNT(name) >= 3; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
ERROR:  aggregate functions are not allowed in WHERE
LINE 1: SELECT COUNT(name), side FROM dinners WHERE COUNT(name) >= 3... 
```

Enter fullscreen mode Exit fullscreen mode

将`HAVING`子句添加到 SQL 中，以提供类似于`WHERE`子句的功能，同时也与聚合函数兼容。将这两个子句的区别理解为`WHERE`适用于单个记录，而`HAVING`适用于组记录是很有帮助的。为此，任何时候你发布一个`HAVING`条款，这个`GROUP BY`条款也必须存在。

下面的例子是另一个尝试，找出哪些配菜是你的至少三个朋友的最爱，尽管这个例子将返回一个没有错误的结果:

```
SELECT COUNT(name), side FROM dinners GROUP BY side HAVING COUNT(name) >= 3; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
 count | side  
-------+-------
     3 | fries
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

聚合函数对于汇总给定表中特定列的结果非常有用。但是，在很多情况下，需要查询多个表的内容。在下一节中，我们将介绍一些实现这一点的方法。

## 查询多个表

通常，一个数据库包含多个表，每个表保存不同的数据集。SQL 提供了几种不同的方法来对多个表运行单个查询。

`JOIN`子句可用于在一个查询结果中组合两个或多个表中的行。它通过查找表之间的相关列来实现这一点，并在输出中对结果进行适当的排序。

包含`JOIN`子句的语句通常遵循以下语法:

```
SELECT table1.column1, table2.column2
FROM table1
JOIN table2 ON table1.related_column=table2.related_column; 
```

Enter fullscreen mode Exit fullscreen mode

请注意，因为`JOIN`子句比较多个表的内容，所以前面的示例通过在列名前面加上表名和句点来指定从哪个表中选择每一列。对于任何查询，您可以像这样指定应该从哪个表中选择一列，尽管在从单个表中选择时没有必要，就像我们在前面几节中所做的那样。让我们通过一个例子使用我们的样本数据。

想象一下，你想给你的每个朋友买一双保龄球鞋作为生日礼物。因为关于朋友的生日和鞋号的信息保存在不同的表中，所以可以分别查询这两个表，然后比较每个表的结果。不过，使用`JOIN`子句，您可以通过一个查询找到您想要的所有信息:

```
SELECT tourneys.name, tourneys.size, dinners.birthdate 
FROM tourneys 
JOIN dinners ON tourneys.name=dinners.name; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
  name   | size | birthdate  
---------+------+------------
 Dolly   |  8.5 | 1946-01-19
 Etta    |    9 | 1938-01-25
 Irma    |    7 | 1941-02-18
 Barbara |  7.5 | 1948-12-25
 Gladys  |    8 | 1944-05-28
(5 rows) 
```

Enter fullscreen mode Exit fullscreen mode

本例中使用的`JOIN`子句，没有任何其他参数，是一个*内部* `JOIN`子句。这意味着它选择两个表中具有匹配值的所有记录，并将它们打印到结果集中，而不匹配的记录被排除在外。为了说明这个想法，让我们在每个表中添加一个新行，这个表在另一个表中没有对应的条目:

```
INSERT INTO tourneys (name, wins, best, size) 
VALUES ('Bettye', '0', '193', '9'); 
```

Enter fullscreen mode Exit fullscreen mode

```
INSERT INTO dinners (name, birthdate, entree, side, dessert) 
VALUES ('Lesley', '1946-05-02', 'steak', 'salad', 'ice cream'); 
```

Enter fullscreen mode Exit fullscreen mode

然后，使用`JOIN`子句:
重新运行前面的`SELECT`语句

```
SELECT tourneys.name, tourneys.size, dinners.birthdate 
FROM tourneys 
JOIN dinners ON tourneys.name=dinners.name; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
  name   | size | birthdate  
---------+------+------------
 Dolly   |  8.5 | 1946-01-19
 Etta    |    9 | 1938-01-25
 Irma    |    7 | 1941-02-18
 Barbara |  7.5 | 1948-12-25
 Gladys  |    8 | 1944-05-28
(5 rows) 
```

Enter fullscreen mode Exit fullscreen mode

注意，因为`tourneys`表没有 Lesley 的条目，而`dinners`表没有 Bettye 的条目，所以这些记录没有出现在输出中。

不过，可以使用一个 *outer* `JOIN`子句从其中一个表中返回所有记录。外部的`JOIN`子句被写成`LEFT JOIN`、`RIGHT JOIN`或`FULL JOIN`。

一个`LEFT JOIN`子句返回“左”表中的所有记录，只返回右表中的匹配记录。在外部连接的上下文中，左边的表是由`FROM`子句引用的表，右边的表是在`JOIN`语句之后引用的任何其他表。

再次运行前面的查询，但是这次使用一个`LEFT JOIN`子句:

```
SELECT tourneys.name, tourneys.size, dinners.birthdate 
FROM tourneys 
LEFT JOIN dinners ON tourneys.name=dinners.name; 
```

Enter fullscreen mode Exit fullscreen mode

该命令将返回左表中的每条记录(在本例中为`tourneys`)，即使它在右表中没有对应的记录。只要右表中没有匹配的记录，它就会作为空值或`NULL`返回，这取决于您的 RDBMS:

```
Output
  name   | size | birthdate  
---------+------+------------
 Dolly   |  8.5 | 1946-01-19
 Etta    |    9 | 1938-01-25
 Irma    |    7 | 1941-02-18
 Barbara |  7.5 | 1948-12-25
 Gladys  |    8 | 1944-05-28
 Bettye  |    9 | 
(6 rows) 
```

Enter fullscreen mode Exit fullscreen mode

现在再次运行查询，这次使用一个`RIGHT JOIN`子句:

```
SELECT tourneys.name, tourneys.size, dinners.birthdate 
FROM tourneys 
RIGHT JOIN dinners ON tourneys.name=dinners.name; 
```

Enter fullscreen mode Exit fullscreen mode

这将返回右表中的所有记录(`dinners`)。因为莱斯利的出生日期记录在右表中，但在左表中没有她的对应行，所以`name`和`size`列将作为该行中的空值返回:

```
Output
  name   | size | birthdate  
---------+------+------------
 Dolly   |  8.5 | 1946-01-19
 Etta    |    9 | 1938-01-25
 Irma    |    7 | 1941-02-18
 Barbara |  7.5 | 1948-12-25
 Gladys  |    8 | 1944-05-28
         |      | 1946-05-02
(6 rows) 
```

Enter fullscreen mode Exit fullscreen mode

注意，左和右连接可以写成`LEFT OUTER JOIN`或`RIGHT OUTER JOIN`，尽管该子句的`OUTER`部分是隐含的。同样，指定`INNER JOIN`将产生与只写`JOIN`相同的结果。

对于一些 RDBMS 发行版，包括 PostgreSQL，还有第四个名为`FULL JOIN`的 join 子句。A `FULL JOIN`将返回每个表中的所有记录，包括任何空值:

```
SELECT tourneys.name, tourneys.size, dinners.birthdate 
FROM tourneys 
FULL JOIN dinners ON tourneys.name=dinners.name; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
  name   | size | birthdate  
---------+------+------------
 Dolly   |  8.5 | 1946-01-19
 Etta    |    9 | 1938-01-25
 Irma    |    7 | 1941-02-18
 Barbara |  7.5 | 1948-12-25
 Gladys  |    8 | 1944-05-28
 Bettye  |    9 | 
         |      | 1946-05-02
(7 rows) 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**在撰写本文时，MySQL 和 MariaDB 都不支持`FULL JOIN`子句。

作为使用`FULL JOIN`查询多个表中所有记录的替代方法，您可以使用`UNION`子句。

`UNION`操作符的工作方式与`JOIN`子句略有不同:与使用单个`SELECT`语句将来自多个表的结果作为唯一列打印不同，`UNION`将两个`SELECT`语句的结果合并到一个列中。

为了举例说明，运行以下查询:

```
SELECT name FROM tourneys UNION SELECT name FROM dinners; 
```

Enter fullscreen mode Exit fullscreen mode

这个查询将删除任何重复的条目，这是`UNION`操作符的默认行为:

```
Output
  name   
---------
 Irma
 Etta
 Bettye
 Gladys
 Barbara
 Lesley
 Dolly
(7 rows) 
```

Enter fullscreen mode Exit fullscreen mode

要返回所有条目(包括重复条目)，请使用`UNION ALL`运算符:

```
SELECT name FROM tourneys UNION ALL SELECT name FROM dinners; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
  name   
---------
 Dolly
 Etta
 Irma
 Barbara
 Gladys
 Bettye
 Dolly
 Etta
 Irma
 Barbara
 Gladys
 Lesley
(12 rows) 
```

Enter fullscreen mode Exit fullscreen mode

结果表中列的名称和数量反映了第一个`SELECT`语句查询的列的名称和数量。注意，当使用`UNION`从多个表中查询多个列时，每个`SELECT`语句必须查询相同数量的列，各个列必须具有相似的数据类型，并且每个`SELECT`语句中的列必须以相同的顺序排列。以下示例显示了如果在查询不同列数的两个`SELECT`语句中使用`UNION`子句可能会产生的结果:

```
SELECT name FROM dinners UNION SELECT name, wins FROM tourneys; 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
ERROR:  each UNION query must have the same number of columns
LINE 1: SELECT name FROM dinners UNION SELECT name, wins FROM tourne... 
```

Enter fullscreen mode Exit fullscreen mode

查询多个表的另一种方式是通过使用*子查询*。子查询(也称为*内部*或*嵌套查询*)是包含在另一个查询中的查询。当您试图根据一个单独的聚合函数的结果来过滤查询结果时，这是非常有用的。

为了说明这个想法，假设你想知道你的哪些朋友比芭芭拉赢得更多的比赛。不用查询 Barbara 赢了多少场比赛，然后运行另一个查询来查看谁赢了更多的比赛，您可以用一个查询来计算这两者:

```
SELECT name, wins FROM tourneys 
WHERE wins > (
SELECT wins FROM tourneys WHERE name = 'Barbara'
); 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
  name  | wins 
--------+------
 Dolly  |    7
 Etta   |    4
 Irma   |    9
 Gladys |   13
(4 rows) 
```

Enter fullscreen mode Exit fullscreen mode

此语句中的子查询只运行了一次；它只需要从与`name`列中的`Barbara`相同的行中的`wins`列中找到值，并且子查询和外部查询返回的数据相互独立。但是，在有些情况下，外部查询必须首先读取表中的每一行，并将这些值与子查询返回的数据进行比较，以便返回所需的数据。在这种情况下，子查询被称为*相关子查询*。

以下语句是相关子查询的一个示例。此查询旨在找出您的哪些朋友赢得的游戏比穿相同鞋码的朋友平均赢得的游戏多:

```
SELECT name, size FROM tourneys AS t 
WHERE wins > (
SELECT AVG(wins) FROM tourneys WHERE size = t.size
); 
```

Enter fullscreen mode Exit fullscreen mode

为了完成查询，它必须首先从外部查询中收集`name`和`size`列。然后，它将该结果集中的每一行与内部查询的结果进行比较，从而确定具有相同鞋号的个人的平均获胜次数。因为您只有两个鞋码相同的朋友，所以结果集中只能有一行:

```
Output
 name | size 
------+------
 Etta |    9
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，子查询可用于查询多个表的结果。为了用最后一个例子来说明这一点，假设你想为该团体有史以来最好的保龄球手举办一次惊喜晚宴。您可以通过下面的查询找到您的哪个朋友有最好的保龄球记录，并返回他们最喜欢的饭菜:

```
SELECT name, entree, side, dessert 
FROM dinners 
WHERE name = (SELECT name FROM tourneys 
WHERE wins = (SELECT MAX(wins) FROM tourneys)); 
```

Enter fullscreen mode Exit fullscreen mode

```
Output
  name  | entree | side  |  dessert  
--------+--------+-------+-----------
 Gladys | steak  | fries | ice cream
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，该语句不仅包括一个子查询，还包含该子查询中的一个子查询。

## 结论

发出查询是数据库管理领域中最常执行的任务之一。有许多数据库管理工具，如 [phpMyAdmin](https://www.phpmyadmin.net/) 或 [pgAdmin](https://www.pgadmin.org/) ，允许您执行查询并可视化结果，但从命令行发出`SELECT`语句仍然是一个广泛使用的工作流，它也可以为您提供更大的控制。

如果您是使用 SQL 的新手，我们鼓励您使用我们的 [SQL 备忘单](https://www.digitalocean.com/community/tutorials/how-to-manage-sql-database-cheat-sheet?utm_source=devto&utm_medium=display&utm_campaign=DO_Dev_Awareness_Cold_Devto_2019)作为参考，并查看[官方 PostgreSQL 文档](https://www.postgresql.org/docs/10/static/index.html)。此外，如果您想了解更多关于 SQL 和关系数据库的知识，您可能会对以下教程感兴趣:

*   [了解 SQL 和 NoSQL 数据库以及不同的数据库模型](https://www.digitalocean.com/community/tutorials/understanding-sql-and-nosql-databases-and-different-database-models?utm_source=devto&utm_medium=display&utm_campaign=DO_Dev_Awareness_Cold_Devto_2019)
*   [如何在 Ubuntu 18.04 上用 PostgreSQL 10 设置逻辑复制](https://www.digitalocean.com/community/tutorials/how-to-set-up-logical-replication-with-postgresql-10-on-ubuntu-18-04?utm_source=devto&utm_medium=display&utm_campaign=DO_Dev_Awareness_Cold_Devto_2019)
*   [如何保护 PostgreSQL 免受自动攻击](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-against-automated-attacks?utm_source=devto&utm_medium=display&utm_campaign=DO_Dev_Awareness_Cold_Devto_2019)

* * *

[![CC 4.0 License](img/4662fd03838b335557c183fd57c6b02c.png)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

*本作品根据[知识共享署名-非商业性使用-类似共享 4.0 国际许可证](http://creativecommons.org/licenses/by-nc-sa/4.0/)* 进行许可