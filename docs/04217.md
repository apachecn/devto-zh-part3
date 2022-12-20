# 例如，您的第一个数据库索引

> 原文：<https://dev.to/jakswa/your-first-db-index-by-example-ace>

我想探索如何简单地说明仅索引扫描的好处，于是就进行了快速浏览。为了使它快速，我们将保持事情简单。我们将在几个不同的场景中使用一个 SQL 查询。它将以亚毫秒级的速度开始(空表)。我们将增加一百万行，使我们的查询速度慢 1000 倍。然后我们将添加一个 btree 索引来加速它。

如果你愿意，你可以在你自己的`psql`控制台上跟随。首先，我已经运行了`create database my_db;`并切换到它。

## 设置+基线

首先，空表没有索引，只有一列:
`create table users (email text);`

让我们来关注一小段 SQL:

```
my_db=# select count(1) from users where email = 'bob@bob.bob';
 count
-------
     0
(1 row) 
```

我们将重复运行的`EXPLAIN ANALYZE`:

```
my_db=# EXPLAIN ANALYZE select count(1) from users where email = 'bob@bob.bob';
                                              QUERY PLAN                                              
------------------------------------------------------------------------------------------------------
 Aggregate  (cost=27.02..27.03 rows=1 width=8) (actual time=0.009..0.009 rows=1 loops=1)
   ->  Seq Scan on users  (cost=0.00..27.00 rows=7 width=0) (actual time=0.005..0.005 rows=0 loops=1)
         Filter: (email = 'bob@bob.bob'::text)
 Planning time: 0.095 ms
 Execution time: 0.058 ms
(5 rows) 
```

(`EXPLAIN ANALYZE`中的`ANALYZE`只是指它实际执行查询，并提供“实际”计时)

这个表是空的，所以 postgres 用了 58 微秒才给我们零行。这是我们的起点。

## 放慢速度

让我们通过添加一百万行来降低查询速度:

```
INSERT INTO users (email)
SELECT 'bob' || x.id::varchar || '@bob.bob'
FROM generate_series(1,1000000) AS x(id); 
```

并获得我们新的解释输出:

```
my_db=# EXPLAIN ANALYZE select count(1) from users where email = 'bob@bob.bob';
                                                 QUERY PLAN
-------------------------------------------------------------------------------------------------------------
 Aggregate  (cost=19628.50..19628.51 rows=1 width=8) (actual time=114.858..114.859 rows=1 loops=1)
   ->  Seq Scan on users  (cost=0.00..19628.50 rows=1 width=0) (actual time=114.854..114.854 rows=0 loops=1)
         Filter: (email = 'bob@bob.bob'::text)
         Rows Removed by Filter: 1000000
 Planning time: 0.154 ms
 Execution time: 114.904 ms 
```

postgres 扫描表用了 114ms，拒绝了我们计数条件的所有行。它甚至告诉我们它删除了多少行。

好的，我们从 58 到 114904 微秒。我们让它慢了几个数量级。到目前为止，这些查询都是顺序扫描所有的行，当您有大量的行时，这种方法不能很好地伸缩。当您的表是一个真实的例子时，与我们使用的这个单列表相比，它的伸缩性更差。

## 添加指标

让我们添加我们的索引，然后`EXPLAIN`再一次:

```
my_db=# CREATE index my_cool_index ON users (email);
my_db=# EXPLAIN ANALYZE select count(1) from users where email = 'bob@bob.bob';
                                                           QUERY PLAN                                                           
--------------------------------------------------------------------------------------------------------------------------------
 Aggregate  (cost=4.45..4.46 rows=1 width=8) (actual time=0.091..0.091 rows=1 loops=1)
   ->  Index Only Scan using my_cool_index on users  (cost=0.42..4.44 rows=1 width=0) (actual time=0.087..0.087 rows=0 loops=1)
         Index Cond: (email = 'bob@bob.bob'::text)
         Heap Fetches: 0
 Planning time: 0.442 ms
 Execution time: 0.154 ms 
```

呜，回复到亚毫秒级响应时间。请注意，输出不再包括“对用户进行序列扫描”，而是“使用 my_cool_index 仅扫描索引”。Postgres 改变了执行查询的方式。它现在遍历我们的 btree(平衡的[树](https://en.wikipedia.org/wiki/Tree_(data_structure)))索引，扫描匹配我们条件的条目。

当`SELECT`部分不要求您访问行时，查询可以是仅索引的。如果我们在一个单独的列中输入一个数字，我们将把“仅索引扫描”留在后面:

```
my_db=# ALTER TABLE users ADD COLUMN age integer;
my_db=# EXPLAIN ANALYZE select SUM(age) from users where email = 'bob5@bob.bob';
                                                        QUERY PLAN                                                         
---------------------------------------------------------------------------------------------------------------------------
 Aggregate  (cost=8.45..8.46 rows=1 width=8) (actual time=0.144..0.144 rows=1 loops=1)
   ->  Index Scan using my_cool_index on users  (cost=0.43..8.45 rows=1 width=4) (actual time=0.134..0.136 rows=1 loops=1)
         Index Cond: (email = 'bob5@bob.bob'::text)
 Planning time: 0.138 ms
 Execution time: 0.202 ms 
```

你成功了！我们已经讨论了一个简单的场景，其中索引加速了 SQL。我们也无意中暴露了自己的索引扫描，据我所知，这是性能的顶峰。👏