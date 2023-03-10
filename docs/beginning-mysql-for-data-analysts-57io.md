# 面向数据分析师的 MySQL 入门

> 原文：<https://dev.to/ladvien/beginning-mysql-for-data-analysts-57io>

我通常写黑客、机器人或机器学习，但我想我应该开始写关于数据分析的想法，这是我这些天支付账单的方式。我想从 MySQL 系列开始，因为我有一些朋友，我觉得它会有助于进入这个领域。但是，我最终会扩展这个系列，以包括可视化、分析，也许还有机器学习。我希望这些文章能帮助人们从在 Excel 中手工生成报告转向编写脚本来自动完成这些枯燥的工作。正如我喜欢说的，“知道编码给你数据超能力！”

我是一名专业的数据分析师，但是，如果我对任何事情都有信心，那就是我的理解有漏洞。也就是说，这些文章可能包含错误。如果你发现了一个，请在评论中告诉我，我会很快修复它。

还有，我很固执己见。我确信这些观点会在我的作品中得到体现。当我注意到它们的时候，我会提供一个警告和理由来解释我为什么持有这个观点。

最后一件事，这些文章将关注**立即可用的技术**。老实说，如果你在完成一篇文章时没有一项新技能，或者至少是对现有技能的肯定，我相信我让你失望了。不要误会我的意思，我计划深入研究所需的技能，但我相信只有当你有一个思维框架来支撑它们时，这些技能才是有用的。

好吧！我们开始吧！

# SQL

开始学习数据分析时，结构化查询语言(SQL)是一个很好的起点。它是一种*好*确定的数据语言，[从 70 年代](https://en.wikipedia.org/wiki/SQL)就已经存在了。SQL 的目的是使个人能够以高效和可预测的方式从数据库中检索数据。然而，如今 SQL 被用于更多方面，比如抽象、分析和语义变化。

它看起来像什么？下面是一个 SQL 查询的例子:

```
SELECT *
FROM employees AS e
LEFT JOIN salaries AS s
    ON e.emp_no = s.emp_no
WHERE e.emp_no = 10004; 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码被称为查询。这是一个我们想要得到答案的问题，用一种机器能理解的语言写出来。在这种情况下，运行这个查询应该会返回回答问题所需的所有数据。这才是 SQL 真正的意义所在。写出一个问题并从数据库中获得答案。

虽然！我们还不打算深入这些细节。现在，让我们建立一个练习环境，在这里我们可以学习*应用*概念以及概念本身。

# 这么多 SQL

我很想告诉你 SQL 很简单。这不是，嗯，至少掌握起来不简单。这很复杂——每天我都学到一些新东西(这是我喜欢它的一个原因)。它的复杂性之一是有不同版本的 SQL 方言。在这里，我们称“方言”为对同一事物进行编码的略微不同的方式。

一些最常见的是:

| 来源/供应商 | 俗名(辩证法) |
| --- | --- |
| ANSI/ISO 标准 | SQL/PSM |
| MariaDB | SQL/PSM，PL/SQL |
| 微软/赛贝斯 | T-SQL |
| 关系型数据库 | SQL/PSM |
| 神谕 | PL/SQL |
| 一种数据库系统 | PL/pgSQL |

让我们把它弄得更混乱一点。SQL 指的是语言，但我们通常指的是供应商或来源的 SQL 方言。因此，即使 MySQL 和 MariaDB 在很大程度上使用相同的方言“SQL / PSM”，我们也不是用它们的通用名，而是用它们的源名来称呼它们。因此，“我编写 MySQL 查询。”或者，“在工作中，我使用 PostgresSQL。”

那么你关注哪一个呢？

我们必须从某个地方开始。我选择了 MySQL，因为我在工作中使用它的孪生兄弟 MariaDB。首先，这是一种很好的 SQL 方言，因为它被许多潜在雇主所使用。

| 来源 | 公司使用 |
| --- | --- |
| 关系型数据库 | 58.7% |
| SQL Server | 41.2% |
| 一种数据库系统 | 32.9% |
| MongoDB | 25.9% |
| SQLite | 19.7% |
| 雷迪斯 | 18.0% |
| 弹性搜索 | 14.1% |

*[来源:Stackoverflow 2018 开发者调查。](https://insights.stackoverflow.com/survey/2018/#technology-_-databases)T3】*

此时，你可能会说，“太棒了？我不知道这意味着什么。”别担心！将此页加入书签，稍后再回来。现在，让我们开始设置一个实践 MySQL 环境。

*   最后一个注意事项，如果你要去参加一个工作面试，这是一个很好的技巧，等到你听到他们怎么读“SQL”，然后说他们是怎么做的。由于“正确”的发音是“Ess-cue-ell”，然而，我认识的大多数专业人士都把它发音为“sequel”(我也是)。

[![sql-pronunciation](img/c17a21cfdeac97a441649604fc15ecdc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A_K-uHtK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/pronounce-sq-camps.png)

# 设置 MySQL

这些说明假设您使用的是 Windows。如果没有，不要担心，大多数仍然适用，但你可以跳过一些步骤！

好的，我们将要安装 MySQL Workbench。这个程序将允许我们编写 SQL 查询，将它们发送到数据库，返回并查看结果。

## 准备安装 MySQL Workbench(仅限 Windows)

如果您使用的是 Windows，您需要安装 MySQL Workbench 在 Windows 上使用的软件。

*   [面向 Visual Studio 2015 的 Visual C++可再发行版](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

[![](img/58ab64ef3890ef912f8caec970709cfd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RGJUUuSd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://ladvien.cimg/connecting_to_server.gif)

我们将连接到我在远程电脑上设置的数据库。连接到远程计算机是与 SQL 数据库交互的最常见方式，但是，稍后我将向您展示如何使用 CSV 构建您自己的数据库。这将在您的本地电脑上托管。

好了，回到设置远程连接。单击“MySQL 连接”旁边的圆圈和加号图标这将弹出一个屏幕，显示连接信息。

输入以下内容:

```
Connection name: maddatum.com
Hostname: maddatum.com
Username: the username I've provided you 
```

Enter fullscreen mode Exit fullscreen mode

请不要害羞，如果你需要一个用户名，请发电子邮件给我。我很乐意给你做一个。

输入连接信息后，点击“确定”。你应该回到“欢迎”屏幕，但现在，会有一个名为“maddatum.com”的连接列表。
[![our-sql-connection](img/f020b4ca0eb1f241b89fc13b3e47aa8d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--SgItukGt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_17.png)

双击它。您很可能会收到以下警告。
[![sql-connection-warning](img/9ad6b231f8cc8ef949286a9ab82cb4f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qenzd-uZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_15.png) 
点击“无论如何都要继续”(如果有选项，勾选“不再显示此消息”)。

如果连接成功，您应该会看到如下屏幕:
[![our-sql-connection](img/30962fe8c5076da7fcff2d00fc20bf64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WQ268-98--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_18.png)

## 显示/使用数据库

好吧！让我们开始行动吧。在我们开始执行查询之前，让我指出用户界面中的一些东西:
[![mysql-workbench-interface](img/c979231057453748bd7d76f3447b2951.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9nrV-oLc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_26.png)

### 写查询

这个区域是您编写查询的地方。每个查询都应该以一个`;`结束，否则 MySQL Workbench 将会混淆并试图将两个查询混在一起。

### 查看结果

这是显示您发送给 SQL will 服务器的任何命令的结果的区域。通常，它是一个包含您在查询中请求的数据的表

### 数据库消息

如果您写的查询不正确，这里是您可以发现的地方，因为数据库会发送一条消息让您知道。此外，数据库会告诉您何时成功返回了查询结果，返回了多少个结果，以及检索这些结果花费了多长时间。当你试图让一个查询*更快*时很有用。

# 逛逛 MySQL

让我们向数据库发送一个查询。在查询区输入:

```
SHOW databases; 
```

Enter fullscreen mode Exit fullscreen mode

现在，用你的鼠标选择这些文本，点击上面的照明(执行)图标。
[![show-databases-command](img/b667536897499e8d53a8fbf15778fdd3.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--DVvLafam--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_27.png)

这将返回在此服务器上找到的所有数据库的列表。您应该在`View Results`区域看到这个。每台 SQL server 上可以有多个数据库，这种情况经常发生。现在我们想把重点放在`employees`数据库上。
T3![show-databases-command](img/424c6934c0ffb4a2c9d1ae7a48b4f925.png)T5】

选择数据库类型`USE`，然后选择数据库的名称。在我们的例子中，它将是:

```
USE employees; 
```

Enter fullscreen mode Exit fullscreen mode

现在，突出显示文本并点击执行按钮。

[![show-databases-command](img/5143a8c25f972e5b6c59ff21edb75a14.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tmU4yk-6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_29.png)

这将在数据库信息中显示以下内容:

```
13:21:55    USE employees   0 row(s) affected   0.0031 sec
13:21:55    Error loading schema content    Error Code: 1146 Table 'performance_schema.user_variables_by_thread' doesn't exist 
```

Enter fullscreen mode Exit fullscreen mode

不要担心这个错误，那是我仓促设置的结果。重要的消息是`USE employees`消息。这意味着您现在已经连接到了`employees`数据库。您在此会话中编写的任何查询都将被发送到这个特定的数据库。

但是，现在怎么办？我们不知道数据库里有什么。不用担心，我们有一个命令来查看在这个数据库中找到的表。如果您不熟悉术语“桌子”，不要担心。只需将表格想象成一个电子表格。这有点复杂，我们稍后将进一步研究它们的结构。但是，现在，电子表格的类比是有效的。

要查看该数据库包含的所有表，请执行命令:

```
SHOW tables; 
```

Enter fullscreen mode Exit fullscreen mode

这应该会返回下面的表名
[![show-databases-command](img/5d8a9070245104b592006bd94809c72d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sfuyASam--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_30.png)

现在，你知道下一个问题了，“但是我怎么知道表格里有什么？”

您可以使用`DESCRIBE`命令来获得关于一个表的更多信息。让我们来看看`departments`表。

键入并执行:

```
DESCRIBE departments; 
```

Enter fullscreen mode Exit fullscreen mode

这应该会返回:

[![show-databases-command](img/0575af9a0d0744796dcd6c08e8ae34e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eGC4SvyJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_31.png) 
这里的`Field`列给出了`departments`表中所有`fields`的名称。什么是场？与 table 一样，我们稍后将更深入地研究它们。但是现在，将字段想象成电子表格中的命名列。

# 我们先查询一下！

现在我们知道了数据库、表和字段名，让我们编写第一个查询！

仍在查询区键入并执行:

```
SELECT departments.dept_no, departments.dept_name
FROM departments 
```

Enter fullscreen mode Exit fullscreen mode

这将返回名为`departments`的表格(电子表格)的字段(列)`dept_no`和`dept_name`的所有条目。你做到了！你是个 SQL 'er。
[![show-databases-command](img/4d6796f042cdbe2fb1a711e5217d6b2e.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--gltHNyop--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/data-analytics-series/mysql_setup_32.png)

# 接下来是什么？

好戏还在后头！我们将学习更多关于 SQL 的知识，它的各个部分，以及它们的专有名称。我们还将深入研究不同数据部分的“正确的”SQL 名称。我们会写更多的查询。

请在评论中随意提问。我会尽快回复他们。