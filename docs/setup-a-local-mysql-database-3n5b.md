# 设置本地 MySQL 数据库

> 原文：<https://dev.to/ladvien/setup-a-local-mysql-database-3n5b>

最后两篇文章是面向 SQL 的，但是，如果我们不给你一种方法来练习对你有意义的数据，它们中的信息会很快消失。让我们面对现实吧，尽管随机调查员工的工资很有趣，但这对你来说毫无意义。

本文将向您展示如何在您的 PC 上安装 MySQL 服务器，连接到它，从 CSV 加载数据，并查询这些数据。有很多事情要做，让我们开始吧。

## 本地 MySQL 服务器设置

这三种操作系统在如何设置 MySQL 服务器的本地副本上有一点不同。不幸的是，Windows 是最复杂的。无论如何，请随意跳到适当的部分

*   [窗户](https://ladvien.com/data-analytics-mysql-localhost-setup/#windows)
*   [Mac](https://ladvien.com/data-analytics-mysql-localhost-setup/#mac)

## 窗口

首先，下载 MySQL MSI 安装程序。

*   [MySQL 服务器 Windows 安装程序](https://dev.mysql.com/downloads/installer/)

[![mysql-windows-installer-download](img/a580639eedf7a69b5c30bb046399cf1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hyXb7PzI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_43.png)

下载完成后，打开文件。

[![mysql-windows-installer-download](img/af1cc327637f43b0a1393a230e38e411.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wavrqegn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_44.png)

如果系统提示您“升级”，请说“是”

[![mysql-windows-installer-download](img/9ce5fe3f83630777b74066a35660509e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QStyCBAw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_45.png)

安装程序有点混乱，但是不用担心，默认情况下一切都很好。

点击 MySQL 服务器，然后添加按钮。添加“MySQL 服务器”和“连接器/OBDC x64”然后点击“下一步”您将看到安装摘要，单击“执行”并等待下载完成，然后安装向导开始。

正如我所说的，大多数安装向导问题我们将保留为默认设置。

[![](img/bdaae0f9879c85df3ef63e4a538aed63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aI6_3evp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://ladvien.cimg/mysql-server-install-mac.gif)

一旦完成，你应该已经在你的 Mac 上安装了 MySQL 服务器。跳到最后一部分进行测试。

## 测试您的本地 SQL 服务器

继续打开 MySQL Workbench，让我们连接到这个新的本地服务器。

点击“新连接”图标，除了“连接名称”之外，一切保持默认，在此输入`localhost`。
[![mysql-windows-installer-download](img/1cb687ba3debc52a0fb808ef93126559.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qhRTQyBU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_52.png)

双击新连接，并输入您在安装过程中创建的密码。瞧吧！

让我们运行一个命令来确保一切正常。

```
SHOW databases; 
```

Enter fullscreen mode Exit fullscreen mode

你应该看到:
[![mysql-windows-installer-download](img/0f965e9a4be92c3fd47276b851ada381.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wZ5xSS3h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_53.png)

## 加载 CSV

创建数据库

```
CREATE DATABASE name_of_your_database; 
```

Enter fullscreen mode Exit fullscreen mode

在我们创建表之前，确保我们使用的是创建的数据库。

```
USE name_of_your_database 
```

Enter fullscreen mode Exit fullscreen mode

现在，在开始加载 CSV 之前，我们需要复习一些枯燥的内容。抱歉，我会尽量简短。

## 数据类型

在 SQL 中，每个字段都有一个“数据类型”你可以把数据类型想象成数据上的一个标签，告诉计算机如何读取它们。

最终，计算机无法理解任何人类的语言。它必须将所有内容转换成`0`和`1`才能理解它的。如果这种转换完全由计算机完成，它可能会看到一个单词并说，“哦，是的，这是那些法语单词中的一个”，而实际上它是英语，因此，到`0`和`1` s 的转换是不正确的。

您可能在电子表格中遇到过这种情况。如果你打开一个电子表格，看到这样的内容

[![xkcd-types](img/99b0a55de386a2fa20831ca6c3aaa185.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9p4zt12E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_59.png)

我实际提供给电子表格的数据是:

| 邮政编码 |
| --- |
| Seventy-five thousand four hundred and forty-four |
| 06579 |

注意`6579`前面的零，这是因为计算机说，“哦，这些数据看起来像数字——既然人类没有告诉我其他的，我就把它们当成数字。嗯，去掉一个数字的前导零是完全合法的。”

我希望所有的数据类型都这么简单，然而，上面的例子就是这么简单。我们可以试着跳过许多数据类型的细微差别，把重点放在我们可能会看到最多的三种数据类型上:

*   日期
*   时间
*   INT(整数的缩写)
*   漂浮物
*   CHAR(字符的缩写)

以下是上述数据在电子表格中的示例:

| 日期 | 时间 | （同 Internationalorganizations）国际组织 | 漂浮物 | 茶 |
| --- | --- | --- | --- | --- |
| 2019-10-01 | 2019-10-01 12:01:22 | forty-two | Forty-two point four | 一切的答案。 |

#### 日期

日期非常简单，它们以数字的形式存储年、月和日。然而，当我们检索这个数字时，它被转换成上面列出的人类可读的格式。

#### 时间

时间和`DATE`一模一样，但也包括小时、分钟、秒(有时是毫秒)。

#### INT

一个`INT`存储一个不大于`2,147,483,647`的数。然而，`INT`不能做的一件事是存储部分数字。例如，如果我们试图在一个`INT`字段中存储`0.5`，它可能会被转换成`1`。

#### 浮动

在`INTS`失败的地方填上。也就是说，`FLOAT`商店只能达到你指定的精度。例如，如果我们试图用两个精度点将一个`0.5`存储在一个`FLOAT`中，我们会没事的。然而，如果我们试图将`0.4567`存储在一个只有两个精度点的`FLOAT`中，那么它将被转换为`0.46`，或者被舍入。

#### 字符

`CHAR`意在存储人类可读的文本。当您将数据放入一个`CHAR`字段时，SQL 程序知道这是人类可读的信息，根本不会试图去理解它。它让它保持原样。这就是为什么`CHARS`被称为“字面量”它们也被称为“字符串”，因为计算机把它们看成是一串串在一起的字符。

### SQL 数据类型

在 SQL 中有许多数据类型，但是，有些你可能永远都不需要使用。SQL 与电子表格有一点不同，它想提前知道创建字段所需的大小。

#### CHAR 重访

这主要会影响我们处理`CHAR`的时候。当 SQL 程序创建一个`CHAR`字段时，它想知道将进入该字段的最大字符数。

例如:

*   CHAR(19)可以保存以下内容:`<-------19-------->`
*   CHAR(5)可以保存以下内容:`<-5->`

一个重要的注意事项是，如果您在一个`CHAR(5)`字段中输入一个字符，那么 SQL 程序将用一个`NULL`来填充其他四个字符。简而言之，一个`CHAR`场将*永远*爆满。

#### VARCHAR

还有另一种类型的字符字段，它允许你输入比开始时决定的更多或更少的数据。`VARCHAR`数据类型代表“可变字符”字段。它允许你在 MySQL 上存储多达`65,535`个字符。这是大约 3 页的文本。

#### VARCHAR 与 CHAR

为什么有`CHAR`呢？我们不应该总是用`VARCHAR`做一切事情以防万一吗？嗯，通常，但不总是。

通常，当你设计一个数据库时，你希望它尽可能的高效(我的意思是，它将会是一个成功的商业产品，对吗？).人类试图填充到字段中的数据的最大大小对 SQL 程序很重要，因为它试图以这样一种方式存储数据，即最大限度地减少所使用的空间并最大限度地提高检索数据的效率。

简而言之，`CHAR`对您的数据库有一些好处。以社会安全号码为例，如果你的数据库必须存储这些数据，那么它可能应该是一个`CHAR`，因为这些数据在历史上是 9 个字符(如果你包括破折号，就是 11 个字符)。

突击测验，我们为什么不存储一个社会安全号码作为一个`INT`？

## 创建表格

好了，我已经给你上了一堂数据类型速成课，让你了解这一点。

我们将:

1.  创建一个名为`tasksDB`的数据库
2.  活动`tasksDB`
3.  在`tasksDB`上创建一个表，设置字段数据类型
4.  然后将 CSV 导入此表
5.  最后，我们将编写一个针对该表的查询

准备好了。？我们开始吧！

### 创建数据库

打开工作台，输入并运行以下命令:

```
CREATE DATABASE tasksDB;

SHOW databases;

USE tasksDB;

SELECT * FROM tasks;

LOAD DATA INFILE './task.csv'  INTO TABLE tasks
FIELDS TERMINATED BY ','
ENCLOSED BY '"' 
LINES TERMINATED BY '\n';

SELECT * FROM tasks; 
```

Enter fullscreen mode Exit fullscreen mode

```
CREATE TABLE IF NOT EXISTS tasks (
    task_id INT AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    start_date DATE,
    due_date DATE,
    status TINYINT NOT NULL,
    priority TINYINT NOT NULL,
    description TEXT,
    PRIMARY KEY (task_id)
)  ENGINE=INNODB; 
```

Enter fullscreen mode Exit fullscreen mode

[https://super user . com/questions/1354368/MySQL-error-in-loading-CSV-file-data-into-table](https://superuser.com/questions/1354368/mysql-error-in-loading-csv-file-data-into-table)