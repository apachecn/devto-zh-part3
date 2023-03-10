# Django:为什么你想为你的多对多关系建立额外的数据库级索引

> 原文：<https://dev.to/karolyi/django-why-you-might-want-to-set-up-extra-db-level-indexes-for-your-manytomany-relationships-1ecg>

*前言:这是 Django `1.11.17`和 MariaDB `1.3.11`。*

*免责声明:我绝不是 MariaDB 的专业人士，但就像所有事情一样，我努力去更好地了解它。欢迎对这个问题提出建议。*

在我当前的一个项目中，数据库显著增长，我在页面中点击，查看 SQL 查询速度。其中之一是多对多的关系，当提供了一个巨大的数据集时，速度在很大程度上变慢了。

这个模型叫做`SeatReservation`，上面有一个`AppliedDiscount` `ManyToMany`键。大概是这样:

```
class TestModel(Model):
    pass

class SeatReservation(Model):
    ...
    test_model = ManyToManyField(to=TestModel)
    applied_discounts = ManyToManyField(to=AppliedDiscount)
    ... 
```

现在，在为`AppliedDiscount`关系生成的表上，将创建两个索引:

```
CREATE TABLE `module_seatreservation_applied_discounts` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `seatreservation_id` int(11) NOT NULL,
  `applieddiscount_id` int(11) NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `unique_key` (`seatreservation_id`,`applieddiscount_id`),
  KEY `constraint_1` (`applieddiscount_id`),
  CONSTRAINT `constraint_1` FOREIGN KEY (`applieddiscount_id`) REFERENCES `module_applieddiscount` (`id`),
  CONSTRAINT `constraint_2` FOREIGN KEY (`seatreservation_id`) REFERENCES `module_seatreservation` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci; 
```

当从`TestModel`角度抛出一个选择，并为`SeatReservation`模型所具有的`AppliedDiscount` s 提供一个`prefetch_related`时，`prefetch_related`查询将看起来像这样:

```
SELECT
(`module_seatreservation_applied_discounts`.`seatreservation_id`) AS `_prefetch_related_val_seatreservation_id`, `module_applieddiscount`.`id`, `module_applieddiscount`.`name`, `module_applieddiscount`.`price`
FROM `module_applieddiscount` INNER JOIN `module_seatreservation_applied_discounts` ON (`module_applieddiscount`.`id` = `module_seatreservation_applied_discounts`.`applieddiscount_id`) WHERE `module_seatreservation_applied_discounts`.`seatreservation_id` IN (485696, 485697, 485693, 485694, 485695) 
```

根据为您的 MariaDB 设置了多少缓冲内存，以及您将多少`seatreservation_id`放入查询，MariaDB 将使用不同的查询方法。

让我们看看上面的`EXPLAIN EXTENDED`版本的精确查询:

```
+------+-------------+------------------------------------------+-------+-------------------------+------------+---------+------+------+----------+--------------------------------------------------------------+
| id   | select_type | table                                    | type  | possible_keys           | key        | key_len | ref  | rows | filtered | Extra                                                        |
+------+-------------+------------------------------------------+-------+-------------------------+------------+---------+------+------+----------+--------------------------------------------------------------+
|    1 | SIMPLE      | module_applieddiscount                   | ALL   | PRIMARY                 | NULL       | NULL    | NULL |    1 |   100.00 |                                                              |
|    1 | SIMPLE      | module_seatreservation_applied_discounts | range | unique_key,constraint_1 | unique_key | 4       | NULL |    5 |    80.00 | Using where; Using index; Using join buffer (flat, BNL join) |
+------+-------------+------------------------------------------+-------+-------------------------+------------+---------+------+------+----------+--------------------------------------------------------------+ 
```

到目前为止，它执行得非常快，因为它不需要使用临时表。但是用成千上万个`seatreservation_id`执行同样的 SQL 查询，你会得到一个非常不同的`EXPLAIN EXTENDED`结果:

```
+------+--------------+------------------------------------------+------+--------------------------+--------------+---------+----------------------------+-------+----------+----------------+
| id   | select_type  | table                                    | type | possible_keys            | key          | key_len | ref                        | rows  | filtered | Extra          |
+------+--------------+------------------------------------------+------+--------------------------+--------------+---------+----------------------------+-------+----------+----------------+
|    1 | PRIMARY      | module_applieddiscount                   | ALL  | PRIMARY                  | NULL         | NULL    | NULL                       |     1 |   100.00 |                |
|    1 | PRIMARY      | <subquery2>                              | ALL  | distinct_key             | NULL         | NULL    | NULL                       |  1000 |   100.00 |                |
|    1 | PRIMARY      | module_seatreservation_applied_discounts | ref  | unique_key,constraint_1  | constraint_1 | 4       | module_applieddiscount.id  | 25846 |   100.00 | Using where    |
|    2 | MATERIALIZED | <derived3>                               | ALL  | NULL                     | NULL         | NULL    | NULL                       |  1000 |   100.00 |                |
|    3 | DERIVED      | NULL                                     | NULL | NULL                     | NULL         | NULL    | NULL                       |  NULL |     NULL | No tables used |
+------+--------------+------------------------------------------+------+--------------------------+--------------+---------+----------------------------+-------+----------+----------------+ 
```

如您所见，有一个查询将针对每个结果遍历整个表**，该结果目前有 25846 条记录。这是查询开始大幅变慢的地方。因为这个**，我经历了执行 **5 分钟的查询。**

我不知道为什么 Django 不为`seatreservation_id`创建索引，尽管[我发现了与](https://code.djangoproject.com/ticket/22125)相反的错误报告(Django 不必为所有唯一索引的第一列创建索引)。据说，一旦 Github 上的 PR 被合并，索引创建将是可选的(取决于使用的 DB 后端)。

正如一条评论所说:

> unique 约束的第一列的索引是否多余的问题取决于数据库如何实现 unique 约束。

根据经验测试(见上文)，在我的例子中，`seatreservation_id`上的索引似乎不是多余的，因为 MariaDB 并不为查询使用惟一索引。

所以我手动为`seatreservation_id` :
添加了一个索引

```
ALTER TABLE module_seatreservation_applied_discounts ADD INDEX new_index (seatreservation_id); 
```

...并执行上面相同的`EXPLAIN EXTENDED`选择，请求 1000 个 id:

```
+------+--------------+------------------------------------------+------+-----------------------------------+-----------+---------+--------------+------+----------+----------------+
| id   | select_type  | table                                    | type | possible_keys                     | key       | key_len | ref          | rows | filtered | Extra          |
+------+--------------+------------------------------------------+------+-----------------------------------+-----------+---------+--------------+------+----------+----------------+
|    1 | PRIMARY      | module_applieddiscount                   | ALL  | PRIMARY                           | NULL      | NULL    | NULL         |    1 |   100.00 |                |
|    1 | PRIMARY      | <subquery2>                              | ALL  | distinct_key                      | NULL      | NULL    | NULL         | 1000 |   100.00 |                |
|    1 | PRIMARY      | module_seatreservation_applied_discounts | ref  | unique_key,constraint_1,new_index | new_index | 4       | tvc_0._col_1 |    1 |   100.00 | Using where    |
|    2 | MATERIALIZED | <derived3>                               | ALL  | NULL                              | NULL      | NULL    | NULL         | 1000 |   100.00 |                |
|    3 | DERIVED      | NULL                                     | NULL | NULL                              | NULL      | NULL    | NULL         | NULL |     NULL | No tables used |
+------+--------------+------------------------------------------+------+-----------------------------------+-----------+---------+--------------+------+----------+----------------+ 
```

看到发生了什么？MariaDB 立即开始使用我刚刚添加的索引，查询现在只对每个结果行使用一次表上的迭代！这是一个重大的更新，在生产层面上也可以看到。

将这个键手动放到 SQL 级别的方式因开发人员而异，我将把它留给您。我在包含`models.py`的模块上将定制逻辑放入我的`apps.py`中，这将在 Django 检查(和本地 runserver)上执行。

那么有哪些外卖呢？

1.  如果您像我一样对速度感兴趣，那么如果可以的话，在生产中有意将 DB 内存设置保持在较低水平，同时观察各种视图的响应时间。我在生产中获得了 2 个 gig 给 MariaDB，尽管机器中有 32GB，并且总是有大约 20GB 空闲。
2.  观察您的生产日志。我使用 multitail，并为它创建了一个 [UWSGI 语法](https://gist.github.com/karolyi/3032020b4d62c6fd0b9148db1a242548)。
3.  如果一个视图需要很长时间，首先评估它的 SQL 查询时间(django-debug-toolbar 是你的朋友)。
4.  努力从里到外，从下到上了解你的申请。这包括了解底层数据库、项目使用的模块以及所有外部服务。这是我一个人的方法:我从头到尾建造绿地项目，包括项目管理和培训。
5.  不要害怕钻研 Django 的源代码和你使用的模块。这是你能够找到 bug，或者实际上改进你的代码，而且对开源项目本身有所贡献的时候。

当然，这只适用于你有时间和意愿积极支持你正在进行的项目的情况。如果不是这样，就在 MariaDB 上扔一大堆内存，出现火就灭了。我的经验是，如果你不去努力，这些火会更大，更难扑灭。

缓存和优化我的项目是我的技能之一。例如，这个项目在数据库之上有 4 级缓存:

1.  MariaDB 查询缓存
2.  Redis 存储从 MariaDB 获取的处理过的数据结构
3.  基于每个请求的 Redis 上的 [`ttl_cache()`](https://pypi.org/project/cachetools/) 和`lru_cache()`(请求与 PK 一起在缓存中使用)
4.  用于存储 pk 对象字典的内部缓存类，在逻辑中传递。

这种大量缓存导致视图处理大量数据，执行速度很快。

如果你能读到这里，恭喜你。这是帖子的戛然而止。