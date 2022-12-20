# 在吉拉迁移数据库时出现不正确的字符串值问题。

> 原文：<https://dev.to/amezousan/jira-database-migration-4b2h>

# 问题

我在吉拉使用 H2 数据库(嵌入式数据库),但这导致了性能问题，消耗了大量的 CPU。

> 嵌入式数据库随时可能失败，它不像 MySQL 那样安全。这只是一个测试用的基本数据库。当您的吉拉实例增长时，您也会遇到性能问题。

引自:[那么嵌入式数据库有什么问题呢？](https://community.atlassian.com/t5/Confluence-questions/So-whats-wrong-with-the-embedded-database/qaq-p/707696)

当我试图将吉拉数据库从 H2 迁移到 MySQL 时，我得到一个错误，“不正确的字符串值:' \xF0\x9F\x98\x96...由于多字节问题。

## 我的环境

*   `$ lsb_release -a`:Ubuntu 14 . 04 . 5 lt
*   `$ mysqld -V`见 5.5.62-0ubuntu0.14.04.1
*   吉拉版本 7.12.1

# 起因

MySQL 似乎不能很好地处理多字节字符。

# 分辨率

用 PostgreSQL 代替 MySQL。这是因为使用 PostgreSQL 会减少多字节问题。 <sup id="fnref1">[1](#fn1)</sup> 迁移到 PostgreSQL 最简单的方法如下:

> 1.  Export your data as an XML backup. For more information, see Backing up data.
> 2.  Create a new database on the new database server to store JIRA's data. For instructions on database creation, please refer to the corresponding database configuration guide in the section "Connecting JIRA to Database".
> 3.  Shut down your JIRA server. Back up your JIRA home directory and JIRA installation directory.
> 4.  Delete the dbconfig.xml file in JIRA home directory.
> 5.  Restart JIRA, you should see the first step of JIRA installation wizard, configure your database connection.
> 6.  Configure JIRA's connection to the new database (created in step 2 above), and then click "Next" button.
> 7.  On the Application Properties setting page, click the "Import your existing data" link and restore your data from the XML backup created in step 1 above.

引自:[将 JIRA 的数据迁移到不同类型的数据库服务器](https://confluence.atlassian.com/adminjiraserver070/switching-databases-749382658.html)

# 引用

*   [在用 MySQL 更新问题或导入 JIRA 应用程序中的数据时，由于编码出现 SQL 异常](https://confluence.atlassian.com/jirakb/sql-exception-while-updating-issues-or-importing-data-in-jira-applications-with-mysql-due-to-encoding-134480131.html)
*   在 Confluence 中恢复 XML 备份时抛出“字符串值不正确”错误

* * *

1.  我在多字节的东西上花了很多时间，但是很多人建议在导入吉拉的数据时将你的数据的字符代码转换成特定的字符代码。我尝试了所有的方法，但是所有的方法都不能解决我的问题。 [↩](#fnref1)