# 欢迎学习 SQL 3:建立关系和组合数据集

> 原文：<https://dev.to/hackersandslackers/welcome-to-sql-3-building-relationships-and-combining-data-sets-4b04>

[![Welcome to SQL 3: Building Relationships and Combining Data Sets](img/62b6c688348c03adcd1e5eab064b4bdb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7pDyvWjC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackers.nyc3.digitaloceanspaces.com/posts/2019/02/SQLpt3-3.jpg)

如果到目前为止，您对本系列中的 SQL 感到疏远，请不要担心:我们将发现关系数据库的神奇之处...*亲戚关系。*调暗灯光，放上你喜欢的马文·盖曲目；我们将在另一个层面上建立联系。

我发现现有的解释数据库关系(特别是连接)的尝试在解释这些概念时已经彻底失败了。我们都习惯于看到的维恩图对于从未见过连接发生的人来说毫无意义，即使这样，它们真的描述了正在发生的事情吗？我很想做一些快速的动画作为替代，但很可能我会满足于像我们其他人一样平庸的东西。

## 关系数据库在行动

到目前为止，虽然我们已经介绍了 SQL，但是我们仍然没有进行“讨论”哦，天啊，不，不是那个说话的人；我指的是必不可少的*两个桌子如何相互关联的例子*谈话。这个演讲不那么尴尬，但它绝对不会让你为生活中更美好的事情做好准备。开玩笑，数据*是*生活中更美好的部分。或者至少在我心中是这样。让我们不要在那上面逗留太久。

让我们来看看用来说明数据关系的最常见的场景:**客户**对**订单**的困境。比方说，我们决定开辟一个**有机纯素古酮羽衣甘蓝 Voltron 5000** 健康食品市场，以迎合高端客户:自命不凡的有钱混蛋。恰好“有钱的混蛋”市场非常容易接受客户关系中的最佳实践，所以我们启动了 CRM 来跟踪我们的最佳客户。这种记录有助于我们假装记住客户的姓名和个性:

### 客户表

| 身份证明（identification） | 名字 | 姓氏 | 电子邮件 | 性别 | 状态 | 电话 |
| --- | --- | --- | --- | --- | --- | --- |
| 653466635-9 | 蒂莫西娅 | 克拉特 | [tcrat0@bandcamp.com](mailto:tcrat0@bandcamp.com) | 女性的 | 华盛顿 | 206-220-3752 |
| 418540868-4 | 烧水用水壶 | 富格尔啤酒花 | [kfuggle1@cafepress.com](mailto:kfuggle1@cafepress.com) | 女性的 | 加利福尼亚 | 661-793-1372 |
| 857532654-6 | 漂亮的 | 桑默兰 | [bsommerland2@soundcloud.com](mailto:bsommerland2@soundcloud.com) | 男性的 | 北卡罗来纳州 | 919-299-0715 |
| 563295938-4 | 红色 | 很少 | [rseldon3@addthis.com](mailto:rseldon3@addthis.com) | 男性的 | 印第安纳 | 765-880-7420 |
| 024844147-7 | 玛丽卡 | 殷勤地 | [mgallatly4@loc.gov](mailto:mgallatly4@loc.gov) | 女性的 | 纽约 | 718-126-1462 |
| 900992907-8 | 夏琳 | 麦克马斯特 | [smcmaster5@gmpg.org](mailto:smcmaster5@gmpg.org) | 女性的 | 内华达州 | 775-376-0931 |
| 329211747-X | 格罗弗 | 行 | [gokey6@weather.com](mailto:gokey6@weather.com) | 男性的 | 得克萨斯州 | 915-913-0625 |
| 656608031-7 | 法利 | 勇气 | [fpluck7@buzzfeed.com](mailto:fpluck7@buzzfeed.com) | 男性的 | 得克萨斯州 | 432-670-8809 |
| 906380018-5 | 萨姆纳 | 狗鱼 | [spickerellb@bloglovin.com](mailto:spickerellb@bloglovin.com) | 男性的 | 科罗拉多州 | 719-239-5042 |

另一方面，我们需要跟踪库存和售出的商品。既然我们已经在刷信用卡并获取所有这些个人客户数据，为什么不将购买与忠诚的客户联系起来呢？因此，我们有一个类似这样的事务列表:

### 订单表

| item _ id | customer _ id | item _ purchased | first _ name | last _ name | amount | date _ purchased |
| 82565290-530d-4272-9c8b-38dc 0 BC 7426 a | 653466635-9 | Creme De Menthe Green | Timo thea | Crat | $ 8.57 | 5/13/18 |
| 9 CFA 5 f 5c-6a9c-4400-8f0f-f826 beer-fru Li | Grover | Okey | $ 4.05 | 10/1/18 |
| 4e7c 8548-340 f-4 e89-a7f 1-95173 DCC 6 e 53 | 656608031-7 | Boogies | Grover | Okey | $ 1.97 | 12/17/18 |
| 65261 e94-494d-48cc-8d5a-642 AE 6921 foam | Grover | Okey | $ 1.84 | 11/28/18 |
| 1 bfd ca 0 f-d54a-4845-bb F5-982813 ab 4 a 65 | 656608031-7 |亚利桑那绿茶| Grover | gau Ford | $ 0.22 | 5/23/18 |
| d20d 7 add-bad 4-4559-8896-d4f 6d 05 aa 3 DD | 90633591Ml | sum ner | Tortoishell | $ 7.98 | 10/11/18 |
| 12134510-bc6c-4bd 7-b733-b 549 a 61 edaa 3 | 906380018-5 | Pasta-Cappellini；干|萨姆纳|洗| $ 0.31 | 11/13/18 |
| 80f 1957 c-df4d-40dc-b9c 4-2c 3939 DD 0865 | 906380018-5 |雷米红浆果浸剂|萨姆纳|皮斯卡里| $ 1.25 | 12/31/18 |
| a75f 7593-3312-43 E4-a604-434055dry | sum ner | pie grome | $ 1.36 | 12/11/18 |
| C5 b 87 ee 3-da94-41 B1-973 a-ef 544 a3 FFB 6 f | 906380018-5 | Calypso-草莓柠檬水| sum ner | pie grome | $ 7.71 | 2/21/19 |
| e383c 58 b-d8da-40ac-AFD 6-7ee 629 DCpaste | Mohammed | Reed | $ 2.77 | 10/21/18 |
| d 88 CCD 5b-0 ACB-4144-ace b-C4 B4 b 46 d3b 17 | 656608031-7 | Cheese-font ina | Mohammed | Reed | $ 4.24 | 7/14/18 |
| 659 df 773-719 c-447 e-a1 a9-4577 DC 9 c 6885 | 6685 雷司令；半干| Farly | putch | $ 4.23 | 4/15/18 |
| c 448 db 87-1246-494 a-ba E4-DCE b8 e 8 a 7 AE | 656608031-7 |甜瓜-蜜露| Farly | putch | $ 1.00 | 9/10/18 |
| 725 c171 a-452d-45ef-9f 23-73ef 20109 b90

自然，顾客购买的不止一件商品；他们买了很多东西。尤其是底部那个**远拔**的家伙——相当不幸的自动生成的名字。

作为独立的表，**客户**和**订单**表各自至少有一个直接的用途。**客户**表帮助我们进行消费者人口统计分析，而**订单**表确保我们在赚钱，而不是被抢劫。虽然很重要，但这两项功能都不是特别具有革命性:自 70 年代以来，这种基本的记录保存水平一直是几乎所有企业的核心。

组合数据的能力使我们能够获得更有意义的见解。我们可以奖励忠诚的客户，根据他们的偏好迎合个人的需求，甚至可能将过去 4 个月中每周二和周四 Pluck 先生在哪里和何时出现的个人数据出售给出价最高的数据经纪人(提示:他在我们的商店)。

多亏了关系数据库，我们既不会局限于单个的单块表，也不会被我们前面设置的表的约束所束缚。关联数据很简单，只要我们有一个通过关联数据的*方法。下面是将订单表中的**外键**与**客户**表中的**主键**进行匹配的视图:*

<figure>[![Welcome to SQL 3: Building Relationships and Combining Data Sets](img/c2a32d1b4c30ecb1033fa0e6a1fa1ba2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3HBbIUpH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hackers.nyc3.digitaloceanspaces.com/posts/2019/02/orders4.gif) 

<figcaption>订单的外键引用客户 ID</figcaption>

</figure>

上面说明了我们已经刷过一点的东西:外键关联。主键和外键对于描述表之间的关系以及执行 SQL 连接是必不可少的。不再赘述，让我们加入一些数据。

## 加入多组数据

“连接”多组数据就是将多个表合并成一个表。

这种合并的方式是由我们使用的四种连接表的方法中的哪一种决定的:**内连接**、**右连接**、**左连接**和**外连接**(左连接和右连接有点相同，但无论如何)。无论*连接*的类型如何，所有连接都有以下共同点:

*   行比较:我们寻找表 A 中的列值与表 B 中的列值相匹配的行。
*   列的合并:任何连接的目的都是得到一个包含来自两个表的列的表。

### 左&右归附

`LEFT`和`RIGHT`连接涵盖了无数的用例。通过一点创造性，左/右连接可以帮助解决我们可能没有预料到的问题。术语“**左**”和“**右**”指的是从左向右阅读时我们想要连接的表格。当通过`LEFT JOIN`连接表时，查询中的第一个*表将是“左”表。或者，`RIGHT JOIN`是指*最后一个*表。*

当我们说“要连接的表”时，我们指定哪个表的键值将是我们合并的“权威”。在 a `LEFT MERGE`、*中，**表 A** 中的所有*记录将在合并后继续存在:

*   对于在**表 B** 中有匹配的行，这些行将被“扩展”以包括**表 B** 中的数据。这意味着从**表 B** 添加到**表 A** 的新列将包含所有关联行的数据。
*   对于存在于**表 A** 中但在**表 B** 中没有匹配的行，这些行不受影响:它们将包含与连接前相同的数据，新列中的值留空。
*   存在于**表 B** 中而不存在于**表 A** 中的密钥将被丢弃。这些连接的目的是丰富主表的数据。

下面是一个实际左连接的例子，我用它来驱动我们“项目”页面上的看板模块。左侧表格是 JIRA 问题的表格，右侧表格是基于问题的自定义设置的集合，例如问题类型的自定义图标和颜色。看看这些数据是如何关联的，以及是什么让它们进入最终表格:

<figure>[![Welcome to SQL 3: Building Relationships and Combining Data Sets](img/110a36af97f10caa8b3671b08c7aac66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YytpZTLk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hackers.nyc3.digitaloceanspaces.com/posts/2019/02/tables15.gif) 

<figcaption>左边表格上的按键决定哪一行留下或离开。</figcaption>

</figure>

`LEFT JOIN`查询的结构如下:

```
SELECT 
  table_1.*, table_2.*
FROM
  t1
    LEFT JOIN
  t2 ON t1.column_name = t2.column_name; 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个实际值的例子:

```
SELECT first_name, last_name, order_date, order_amount
FROM customers c
LEFT JOIN orders o
ON c.customer_id = o.customer_id; 
```

Enter fullscreen mode Exit fullscreen mode

将此与一个**右连接:**

```
SELECT first_name, last_name, order_date, order_amount
FROM customers c
RIGHT JOIN orders o
ON c.customer_id = o.customer_id 
```

Enter fullscreen mode Exit fullscreen mode

### 内部连接(或交叉连接)

内部联接是联接数据集的最保守的方法。与`LEFT`或`RIGHT`连接不同，在**内部连接中没有权威表:**只有在*所有*表中包含匹配项的行将在连接中存活。其他所有行将被忽略:

```
SELECT table_1.column_name(s), table_2.column_name(s), 
FROM table1
INNER JOIN table2
ON table1.column_name = table2.column_name; 
```

Enter fullscreen mode Exit fullscreen mode

因为内部联接只作用于在所有受影响的表中匹配的行，所以内部联接通常包含最“完整”的数据集(满足值的列数最多)，但包含的行数最少。

## 外部联接

**外部连接**实际上有几种不同的风格。一般来说，外部连接最大化了执行连接后保留的数据量。

### 左(或右)外连接

乍一看，您可能会看到左/右外部联接的结果，并误认为它们与纯左/右联接完全相同。嗯，你实际上一点都不会错！没错，我在撒谎:连接类型之间本质上没有区别(因此我们提到它们的时间毫无价值)。

### 全外连接

在完全外部连接中，所有的列和行都将被连接到结果输出中，不管这些行是否与我们指定的键匹配。你会问，我们为什么要指定一个键呢？匹配键上的行仍然会组合与所有涉及的表相似的行(如果在合并过程中确实没有具有共同点的行，您应该问问自己为什么要首先合并两组不相关的数据)。

结果有点乱。我将在这里借用熊猫文献中的一个插图:

<figure>[![Welcome to SQL 3: Building Relationships and Combining Data Sets](img/a4096eef0c602ab3c28ea634395c6524.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b5cjBAsx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackers.nyc3.digitaloceanspaces.com/posts/2019/02/merging_merge_on_key_dup.png) 

<figcaption>来源:[https://pandas . pydata . org/pandas-docs/stable/user _ guide/merging . html](https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html)</figcaption>

</figure>

虽然 B 列看起来没有受到什么影响，但是看看它周围发生了什么:标记为 **A_x** 和 **A_y** 的列是连接的结果。外部连接已经创建了一个表，在该表中存在列 B 中键的每种可能的值组合。因此，我们新表中的行数实际上是表 A 的 ***长度*** * ***表 B 的*长度。**

我个人很少使用**外连接**，但这只是我的看法。

```
SELECT column_name(s)
FROM table1
FULL OUTER JOIN table2
ON table1.column_name = table2.column_name; 
```

Enter fullscreen mode Exit fullscreen mode

## 场景:从多个连接创建一个新表

到目前为止，我们只看了两个表同时连接的例子。事实上，我们可以一次合并任意多的表！回到 JIRA 的例子，下面是我用来创建支持定制看板的最终表的实际查询:

```
CREATE TABLE jira
AS
SELECT
    jira_issues.*,
    jira_issuetypes.issuetype_url,
    jira_issuetypes.issuetype_color,
    jira_epiccolors.epic_color
FROM
    jira_issues
    LEFT JOIN jira_issuetypes ON jira_issues.issuetype = jira_issuetypes.issuetype
    LEFT JOIN jira_epiccolors ON jira_issues.epic_name = jira_epiccolors.epic_name; 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 PostgreSQL，视图是保存连接结果的好方法，无需添加额外的表。不要用`CREATE TABLE`，试着用`CREATE VIEW` :

```
CREATE TABLE jira
AS SELECT
  jira_issues.*,
  jira_issuetypes.issuetype_url,
  jira_issuetypes.issuetype_color,
  jira_epiccolors.epic_color
FROM
  jira_issues
  LEFT JOIN jira_issuetypes ON jira_issues.issuetype = jira_issuetypes.issuetype
  LEFT JOIN jira_epiccolors ON jira_issues.epic_name = jira_epiccolors.epic_name; 
```

Enter fullscreen mode Exit fullscreen mode

## 工会&工会所有

思考`JOIN` s 的一个好方法是水平扩展我们的数据集*。那么，`UNION`是一种垂直组合数据*的方式。* **Unions** 组合具有相同结构的数据集:它们只是创建一个包含两个表中的行的表。`UNION`运算符可以组合两个或多个 SELECT 语句的结果集，只要:*

 **   UNION 中的每个 SELECT 语句必须具有相同的列数。
*   这些列还必须具有相似的数据类型。
*   每个 SELECT 语句中的列顺序也必须相同。

### 工会

```
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2; 
```

Enter fullscreen mode Exit fullscreen mode

### (与何处结合)

我们还可以通过 **where** 语句:
为**联合**添加逻辑

```
SELECT City, Country FROM Customers
WHERE Country='Germany'
UNION
SELECT City, Country FROM Suppliers
WHERE Country='Germany'
ORDER BY City; 
```

Enter fullscreen mode Exit fullscreen mode

### 工会所有

一个有趣的区别是`UNION`和`UNION ALL`的出现。在这两个操作中，`UNION`是更“智能”的操作:如果在两个 SELECT `queries`中都存在相同的行，那么`UNION`会知道只给我们一行以避免重复。另一方面，`UNION ALL`*是否返回重复项:这导致了更快的查询，并且对于那些想知道两个`SELECT`语句中的内容的人来说可能是有用的:* 

```
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2; 
```

Enter fullscreen mode Exit fullscreen mode

### 联盟所有(与哪里)

就像`UNION`一样，我们可以通过 **where** 语句:
将逻辑添加到 **union all**

```
SELECT City, Country FROM Customers
WHERE Country='Germany'
UNION ALL
SELECT City, Country FROM Suppliers
WHERE Country='Germany'
ORDER BY City; 
```

Enter fullscreen mode Exit fullscreen mode

## 前方更有 SQL

我希望可视化**加入**和**联合**的工作方式可以帮助 SQL 新手减少摩擦。我很难相信人类在没有亲眼目睹这些概念发生的情况下能够完全理解它们，这就引出了一个问题:为什么有人会在不知道好处的情况下探索解释得如此糟糕的东西？

如果你觉得这些指南有用，欢迎向我抱怨，让它们继续出现。在我们的系列文章中，我们还有更多 SQL:请继续关注我们对聚合值和更多内容的探索！**