# Elasticsearch 嵌套数据类型和查询操作符的语义

> 原文：<https://dev.to/byronruth/the-semantics-of-elasticsearchs-nested-datatype-and-query-operator-gbf>

基本问题是 [`nested`查询操作符](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-nested-query.html)中的谓词是否一次考虑一个文档(根据文档应该是这样的)，更重要的是，多个合取(and)或析取(or)操作是否在独立的记录上进行计算和组合。

这是我们将要测试的映射。我们假设这个索引叫做`shapes`。我们只关心组成形状的点，所以本例中不包括其他属性。

```
{  "mappings":  {  "_doc":  {  "properties":  {  "points":  {  "type":  "nested",  "properties":  {  "x":  {  "type":  "integer"  },  "y":  {  "type":  "integer"  }  }  }  }  }  }  } 
```

这里有一些我们将用来测试的形状记录。

```
{"points":  [  {"x":  1,  "y":  1},  {"x":  1,  "y":  2},  {"x":  2,  "y":  1},  {"x":  2,  "y":  2}  ]}  {"points":  [  {"x":  1,  "y":  1},  {"x":  1,  "y":  2}  ]}  {"points":  [  {"x":  1,  "y":  1},  {"x":  1,  "y":  2},  {"x":  2,  "y":  1}  ]} 
```

重复一下，问题是`nested`查询(这里的`points`是一个[嵌套数据类型](https://www.elastic.co/guide/en/elasticsearch/reference/current/nested.html))是否考虑了一个数组中的*所有*子记录。

首先，考虑下面的查询:

```
{  "query":  {  "nested":  {  "path":  "points",  "query":  {  "bool":  {  "must":  [  {"term":  {"points.x":  2}}  ]  }  }  }  }  } 
```

这意味着“给我找出至少有一个点在这里的所有形状”。在上面的记录中，第二个形状记录没有一个点，`x = 2`和 Elasticsearch (ES)正确地将其从结果中排除。

第二个测试增加了一个要求，点必须*也*有`y = 2`而不是任何值。

```
{  "query":  {  "nested":  {  "path":  "points",  "query":  {  "bool":  {  "must":  [  {"term":  {"points.x":  2}},  {"term":  {"points.y":  2}}  ]  }  }  }  }  } 
```

第一个形状满足这个要求，es 只正确地返回这个记录。

现在，我们将尝试一个看似略有不同的查询。`must`项将放在单独的`nested`表达式中。

```
{  "query":  {  "bool":  {  "must":  [  {  "nested":  {  "path":  "points",  "query":  {  "term":  {"points.x":  2}  }  }  },  {  "nested":  {  "path":  "points",  "query":  {  "term":  {"points.y":  2}  }  }  }  ]  }  }  } 
```

这里的区别在于，每个记录都是针对每个`nested`操作数*独立地*进行评估的，而上面的查询需要一个点来满足两个条件。

我们可以通过下面的查询进一步确认这种*独立的*行为，该查询要求形状“有一个点`x = 1, y = 1`和至少一个点`x = 2`，但没有点`x = 2, y = 2`”。

```
{  "query":  {  "bool":  {  "must":  [  {  "nested":  {  "path":  "points",  "query":  {  "bool":  {  "must":  [  {"term":  {"points.x":  1}},  {"term":  {"points.y":  1}}  ]  }  }  }  },  {  "nested":  {  "path":  "points",  "query":  {  "bool":  {  "must":  [  {"term":  {"points.x":  2}}  ]  }  }  }  }  ],  "must_not":  {  "nested":  {  "path":  "points",  "query":  {  "bool":  {  "must":  [  {"term":  {"points.x":  2}},  {"term":  {"points.y":  2}}  ]  }  }  }  }  }  }  } 
```

注意最后两个`nested`操作数重叠，因为两者都将在`x = 2`处匹配。所有三个形状都满足第一个`must`条件，但是只有形状 1 和 3 满足第二个`must`条件。`must_not`条件排除了形状 1，因为它包含点`x = 2, y = 2`。因此，ES 结果仅包含形状 3。

## 与 SQL 的比较

对于那些熟悉关系数据库以及这些类型的查询如何在 SQL 中表达的人来说，对一对多关系的多个记录进行条件化起初并不直观，但是很容易学习并且变得显而易见。

如果我们以关系形式对这些数据进行建模，我们将得到下面的表:

```
create table "shapes" (
  id integer primary key
);

create table "points" (
  shape_id integer references "shapes" ("id"),
  x integer,
  y integer
); 
```

由于一个形状可以有许多点，因此需要将它建模为引用该点所属的形状记录的关系(这可以进一步规范化，以不复制任何唯一的点，但这种优化对本文并不重要)。

insert 语句如下，它利用了 Postgres 的多行`VALUES`语法。

```
insert into "shapes" (id) values
  (1), (2), (3);

insert into "points" (shape_id, x, y) values
  (1, 1, 1), (1, 1, 2), (1, 2, 1), (1, 2, 2),
  (2, 1, 1), (2, 1, 2),
  (3, 1, 1), (3, 1, 2), (3, 2, 1); 
```

如果我们要用 SQL 表达上面的第一个 ES 查询，我们应该写:

```
select
  shapes.id
from
  shapes
  inner join points
    on shapes.id = points.shape_id
where
  points.x = 2 
```

这将像 es 一样返回形状 1 和 3

第二个查询同样简单，只需在`y`上为同一点添加条件。

```
select
  shapes.id
from
  shapes
  inner join points
    on shapes.id = points.shape_id
where
  points.x = 2 and
  points.y = 2 
```

第三个查询呢？提醒一下，我们将这两个条件分成单独的 ES `nested`查询，根据记录的语义，这些查询应该在记录间独立操作。在 SQL 中对此建模需要表达形状和点之间的独立关系。

```
select
  shapes.id
from
  shapes
  inner join points p1
    on shapes.id = p1.shape_id
  inner join points p2
    on shapes.id = p2.shape_id
where
  p1.x = 2 and
  p2.y = 2 
```

这里的区别在于每个条件(在`where`子句中)适用于不同的`points`关系，因此不需要任一关系中的单个记录来满足两个条件，而是需要两个关系中的至少一个记录来满足其中一个条件。

第三个查询呢？

```
select
  shapes.id
from
  shapes
  inner join points p1
    on shapes.id = p1.shape_id
  inner join points p2
    on shapes.id = p2.shape_id
  inner join points p3
    on shapes.id = p3.shape_id
where
  p1.x = 1 and p1.y = 1 and
  p2.y = 2 and
  not (p3.x = 2 and p3.y = 2) 
```

对`points`表进行了另一个连接，产生了一个新的关系，但这也意味着这个关系上的任何条件都独立于其他连接的关系而应用。在这种情况下，第三个关系断言一个形状的*不*包含一个点`x = 2, y = 2`。

通过这种比较，我们可以断言,`nested`操作数中的条件独立于其他`nested`操作数在记录集合上操作，因此语义上等同于上面 SQL 语句中表示的独立连接。