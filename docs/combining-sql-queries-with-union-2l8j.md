# 将 SQL 查询与 UNION 结合起来

> 原文：<https://dev.to/alexgascon/combining-sql-queries-with-union-2l8j>

[![](img/fc7146a7e42d04810894bd515f0e0b19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kuxbioem--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2hrbgdbiyina94vpj9wz.png)

### 简介

昨天，我在更新我目前公司的一个项目中的一些 Ruby gems，并注意到一个叫做 [active_record_union](https://github.com/brianhempel/active_record_union) 的项目。我必须承认，我从未听说过 SQL 的`UNION`操作，所以 gem 对我产生了很大的好奇心。这就是为什么我决定获取更多的相关信息，并写下这篇学习！

### SQL UNION

SQL Union 的目的是将几个查询的结果组合成一个查询。例如，假设我们的数据库中有下面的表，代表用户。

| 使用者辩证码 | 国家 | 年龄 |
| --- | --- | --- |
| one | 美国 | Twenty-one |
| Two | 德国 | Nineteen |
| three | 日本 | Twenty-seven |
| four | 西班牙 | Twenty |

假设我们想要搜索两种类型的用户:

*   23 岁以上的用户
*   位于美国的用户

为此，您需要以下两个查询:

*   `SELECT * FROM Users WHERE Country = "United States"`
*   `SELECT * FROM Users WHERE Age > 23`

然而，这将需要两次 DB 调用；通常，您希望使用尽可能少的 DB 调用来获得结果，因此这不是最佳解决方案。此外，使用不同的查询需要对结果进行手动处理，以便将它们组合成一个结果(因为每个查询都返回一个结果集)

这就是`UNION`发挥作用的地方。使用`UNION`可以将两个查询合并成一个查询，因此在一个 DB 操作中可以获得相同的结果。

```
SELECT * FROM Users WHERE Country = "United States"
UNION
SELECT * FROM Users WHERE Age > 23 
```

| 使用者辩证码 | 国家 | 年龄 |
| --- | --- | --- |
| one | 美国 | Twenty-one |
| three | 日本 | Twenty-seven |

通过这样做，我们将只需要一个 DB 操作，并且我们将在相同的结果中得到所有匹配的用户，而不需要做任何手动组合。

#### 但是，我就不能用 OR 吗？

大概你看到上一节的时候也问过自己这个问题。您是对的:您也可以通过使用几个与`OR`相关联的条件将两个查询合并成一个，就像这样:

```
SELECT * FROM Users WHERE (Country = "United States") OR (Age > 23) 
```

然而，**的关键在于每个操作**的表现。如果在用于搜索的列中有索引，那么使用`UNION`可能会更好。

使用`OR`将使 SQL 操作只对其中一列使用索引，甚至根本不使用它们。另一方面，使用`UNION`可以分别解析两个查询，为每个查询使用最佳的查询计划，这可能意味着使用每个列的索引(如果它们有索引的话)。您可以在以下链接中找到更详细的解释:

*   [SQL 性能联合 vs 或](https://stackoverflow.com/questions/13750475/sql-performance-union-vs-or)
*   [OR vs UNION](https://www.postgresql.org/message-id/004f01c34c9d%24ce9edcc0%242766f30a%40development.greatgulfhomes.com)

### 不同表的并集

另外，`UNION`允许获得多个表的结果，而不是局限于一个简单的表。例如，假设现在我们有两个表:一个用于客户，一个用于供应商:

| CustomerID | 国家 |
| --- | --- |
| one | 美国 |
| Two | 德国 |
| three | 美国 |
| four | 西班牙 |

| 供应商 ID | 国家 |
| --- | --- |
| one | 美国 |
| Two | 法国 |

如果我们想获得美国所有的客户或供应商，我们可以这样做:

```
SELECT 'Customer' AS Type, CustomerID AS ID FROM Customers WHERE Country = "United States"
UNION
SELECT 'Supplier', SupplierID FROM Suppliers WHERE Country = "United States" 
```

我们会得到这样的结果:

| 类型 | 身份证明 |
| --- | --- |
| 顾客 | one |
| 顾客 | three |
| 供应者 | one |

请注意，在第二个查询中，我们甚至不需要指定列名！由于列在相同的位置，`UNION`自动知道必须使用哪个列名。

*(重要的是要考虑到这只有在两个操作返回相同数量的列，并且类型相似的情况下才有可能)*。

### 工会所有

我没有提到的是，当组合查询结果时，`UNION`做了一个额外的操作来删除重复的行。回到第一个例子，如果我们不是搜索年龄大于 23 岁的用户，而是搜索年龄大于 20 岁的用户，这将非常有用:用户 1 同时满足两个条件，因此他将出现在两个结果中。但是为了防止我们有重复的数据，`UNION`会过滤结果并只返回他的行一次。

然而，让我们假设我们完全确定不会有重复。例如，如果我们有一个如下所示的表:

| 使用者辩证码 | 国家 | 大陆 |
| --- | --- | --- |
| one | 美国 | 美国 |
| Two | 德国 | 欧洲 |
| three | 日本 | 亚洲 |
| four | 西班牙 | 欧洲 |

这一次，让我们假设我们想要检索:

*   国家/地区为美国的用户
*   亚洲大陆的用户

我们绝对肯定，这两个群体之间绝不会有巧合。所以，让`UNION`搜索重复是浪费时间。

对于那些情况，我们可以使用`UNION ALL`操作:它也组合了几个查询，但是不检查结果中是否有重复。因此，下面的查询:

```
SELECT * FROM Users WHERE Country = "United States"
UNION ALL
SELECT * FROM Users WHERE Continent = "Asia" 
```

将返回结果:

| 使用者辩证码 | 国家 | 大陆 |
| --- | --- | --- |
| one | 美国 | 美国 |
| three | 日本 | 亚洲 |

实际上，这比使用简单的`UNION`要快得多(快多少取决于结果的大小)。

但是，这也要求您对操作更加小心，因为如果您组合两个可能有重复结果的查询...

```
SELECT * FROM Users WHERE Country = "Spain"
UNION ALL
SELECT * FROM Users WHERE Continent = "Europe" 
```

| 使用者辩证码 | 国家 | 大陆 |
| --- | --- | --- |
| four | 西班牙 | 欧洲 |
| Two | 德国 | 欧洲 |
| four | 西班牙 | 欧洲 |

事实上，你会得到重复的结果。

## 总结

如果您需要按多个条件进行搜索，并且希望从与相应列相关联的索引中获得尽可能多的收益，SQL `UNION`语句是一个非常有用的资源。如果您想合并两个具有相似结构的表的结果(例如，如果您正在使用具体的表继承，并且只需要公共数据)，这也非常有用。

然而，它也有它的缺点:您需要注意，如果您正在检索大量的行，重复删除步骤可能会使查询变慢一点；但是好的一面是，您可以使用`UNION ALL`来跳过这一部分。当然，您需要完全确定在不同的查询中不会有任何重复的数据。正如本大叔所说:*“权力越大，责任越大”*