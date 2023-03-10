# 你可以用 SQL 来做，不要再为它写额外的代码了

> 原文：<https://dev.to/geshan/you-can-do-it-in-sql-stop-writing-extra-code-for-it-lok>

“SQL、Lisp 和 Haskell 是我见过的唯一一种花更多时间思考而不是打字的编程语言。”—菲利普·格林斯本

即使思考比输入 SQL(结构化查询语言)更多，我们软件工程师也只是把它作为一种提取数据的方式。

> 我们通常不利用 SQL 的数据处理能力，而是对代码进行必要的修改。

这在从事 web 应用的软件工程师中非常普遍。我们忽略的另一件事是，如果我们直接在 SQL 中操作，提取的数据对于任何编程语言都是相同的格式。这篇文章旨在让你了解你可能知道但通常不使用的 SQL 的强大功能。

[![Tea Lights image from [Pixabay](https://pixabay.com/en/tea-lights-candles-light-prayer-2223898/)](img/77722ebd597406cace8c04cadda3b428.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--v3BpngoC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2A5TyHwM3Q_6HSA4In.jpg) *茶灯图片来自 [Pixabay](https://pixabay.com/en/tea-lights-candles-light-prayer-2223898/)* 

## TLDR；

> 使用 SQL 进行数学运算，如求和、平均等。利用它对一个或多个关系值进行分组，比如获取产品类别。利用 SQL 进行字符串操作，比如使用 CONCAT_WS 连接名字和姓氏。利用 SQL 按自定义优先级公式排序。下面的例子…

## 举个例子

通过一个例子来解释 SQL 的强大功能会更容易。以下是退款微服务的 MYSQL 中的两个表的基本模式:

[![](img/65b0c0e28448f68ff5d7c57ef5fa171a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--odaQ9o0W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2ASaZrIrqQW0LDPDsn.png)

有 2 笔退款和 7 笔相关付款作为示例[数据](http://sqlfiddle.com/#!9/b242d/5)。

## 一些假设

对于退款微服务示例模式和应用，做出以下假设:

1.  退款微服务和数据结构存储 fk_item(订购/交付商品的 id)，但不是硬外键。

2.  物品可以现金或信用的方式获得退款。

3.  只要余额能够支付每笔现金和信用点数的申请退款金额，就可以多次退款。例如，项目以现金支付 50 英镑，以信用卡支付 50 英镑。2 退款 20 现金 20 信用都可以。因此，在这些交易之后，该项目的余额将为 10 现金和 10 信用(50–20–20)。

4.  每次退款可以有多个项目付款。每笔付款可以是现金或信用卡。

5.  所有金额都以美分存储，因此它们是整数。

现在让我们使用一些 SQL 功能。您可以找到在 [SQL Fiddle](http://sqlfiddle.com/#!9/b242d/5) 上运行相关查询的例子。

## 用 SQL 做数学运算

作为软件工程师，假设我们需要找到某个项目的总现金和退款金额，我们会怎么做？我们将运行如下代码:

```
SELECT fk_item, fk_refund, amount, is_cash 
FROM payment WHERE fk_item=2001; 
```

Enter fullscreen mode Exit fullscreen mode

对于当前数据，它将给出如下 3 行:

[![](img/0f199bf21632071652201229e6fde73a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RlYUjuVn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2ADA4sIRUh4Fwig2Yk.png)

对于这三行，我们将对它们进行循环。如果是现金，将其累加到 cashBalance 变量，如果不是，则将其累加到 creditBalace 变量。相比之下，在 SQL 中这样做要容易得多(可能更快)，比如:

```
SELECT fk_item, SUM(amount) AS total_paid, IF(is_cash = 1, 'cash', 'credit') as type 
FROM payment WHERE fk_item = 2001 GROUP BY fk_item, is_cash; 
```

Enter fullscreen mode Exit fullscreen mode

导致:

[![](img/71494fa67420629da06187fbb44a5e66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--abbLsL5y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AbibYiq9NyhbIZB7B.png)

现在结果很简单，如果你需要项目的总退款，只需将 GROUP BY 更改为 fk_item 即可。对于 2 个和 3 个记录来说，这没有什么意义。如果该商品有 20 次退款，那么第一个循环解决方案就是编写更多的代码而没有收益。像 sum 一样，也可以使用其他 SQL 函数。简单的数学运算，如[求和](https://www.w3schools.com/sql/func_mysql_sum.asp)、相乘、[求平均值](https://www.w3schools.com/sql/func_mysql_avg.asp)等都可以用 SQL 轻松实现。这意味着不再有循环。

## 使用 GROUP_CONCAT 获取相关的 1:m 关系值

[Group concat](http://www.mysqltutorial.org/mysql-group_concat/) 是 SQL 数据库中一个健壮的操作。当您需要从一对多关系中获取数据时，这是非常有用的。例如，您想要获取一篇博客文章的所有标签，或者想要获取一个产品的所有类别。关于这个退款示例，一个项目可以退款多次。因此，我们将获得与项目 id 相关的所有退款。为了得到这个，我们将只运行 1 个查询，并且在代码中没有任何循环，如下所示:

```
SELECT fk_item, GROUP_CONCAT(DISTINCT fk_refund) refund_ids 
FROM payment WHERE fk_item = 2001; 
```

Enter fullscreen mode Exit fullscreen mode

这导致:

[![](img/a9441610689bd9a06e8cea2a848bedd9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QO2mt9KW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AiPClLYAg9JIVh2LY.png)

现在我们知道项目 2001 已经退了两次 2 退款。这将很容易分解退款 id，并进行任何相关的操作。

## 字符串操作

许多[字符串操作](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html)任务，比如子串、连接、改变大小写和字符串比较都可以在 SQL 中完成。通过这个例子，我将展示 CONCAT_WS 的用法。它用分隔符连接。它还可以用于选择名字和姓氏，中间有空格。

> 在有可选中间名的情况下，COALESCE 可与 CONCAT_WS 一起使用。那是你要探索的东西:)。

在本例中，我将选择退款 _nr 及其相关原因:

```
SELECT CONCAT_WS("-", refund_nr, reason) AS refund_nr_with_reason 
FROM refund; 
```

Enter fullscreen mode Exit fullscreen mode

导致:

[![](img/f21504a22204096c10c6e000c9671c08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K0DZHIUa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AsOABbdMzH1hrt1h3.png)

例如，如果需要在贷方票据文档上显示，则不需要额外的代码来再次连接这些值。SQL 又让它变得简单了一步。

## 用自定义公式排序

所有的软件工程师都知道你可以根据一列进行排序。但是如果给你一个自定义的优先级公式来排序，你会怎么做？很可能再次求助于代码和循环来排序。因此，让我们为上面的示例设置优先级公式规则:

1.  高级客户退款获得最高优先级(我们将它的优先级设为 9999999999)

2.  除了高级客户，现金退款的优先级为金额* 25，而信用的优先级为金额* 20。

根据上述规则，我们决定优先处理 50000(美分)以上的高级客户和优先级。然后其他退款就流程了。让我们优先退款如下:

```
SELECT r.refund_nr, r.reason, p.fk_item, p.amount, p.is_cash,
IF(p.premium_customer = 1, 9999999999, p.amount * (IF(is_cash = 1, 25, 20))) AS priority 
FROM refund AS r INNER JOIN payment AS p ON r.id = p.fk_refund 
HAVING priority > 50000 
ORDER BY priority DESC 
```

Enter fullscreen mode Exit fullscreen mode

结果如下:

[![](img/c5fe380dc8355fca68bb893f0f518479.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DFvkEgHc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AoveiUhK9eNTKR4aw.png)

如果在 SQL 中正确使用 IF，通过定制的优先级公式进行排序要比试图在代码中使用循环容易得多。请注意，更小的金额，如 7.5 (750 美分)和 9.0 (900 美分)具有最高优先级，因为这些退款金额与高级客户相关。

> 作为一名软件工程师，使用 SQL 的强大功能让您的生活变得更加轻松。

您可以尝试这个例子，并在 [SQL fiddle](http://sqlfiddle.com/#!9/b242d/5) 上运行您的查询。

## 结论

作为一名软件工程师，SQL 还有其他一些技巧可以帮助你。比如，UPDATE with INSERT using ON DUPLICATE KEY UPDATE。每当您想用循环代码对数据库中的数据进行操作时，请三思。这个故事的主要观点是:

> 利用 SQL 的力量编写更少的代码，因为“最好的代码是从未编写过的代码”。如果没有写下来，就没有必要维护它。

* * *

*最初发表于[geshan.com.np](https://geshan.com.np/blog/2018/12/you-can-do-it-in-sql/)。*