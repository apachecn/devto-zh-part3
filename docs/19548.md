# 将字符数据更正为数字

> 原文：<https://dev.to/andytower_rus/correct-sort-character-data-as-numeric-3lkf>

今天我找到了解决问题的方法，以正确的顺序排列数据。我说的是 char 数据内容数值数据。
例如:

```
with rawData (sCol) as (
select '10.1.1'
union all
select '1.1.1'
union all
select '2.1.1'
union all
select '2.10.1'
union all
select '2.2.1'
)
select * from rawData 
```

| 乙状结肠（sigmoid colon） |
| --- |
| 10.1.1 |
| 1.1.1 |
| 2.1.1 |
| 2.10.1 |
| 2.2.1 |

如果我使用标准排序

```
with rawData (sCol) as (
select '10.1.1'
union all
select '1.1.1'
union all
select '2.1.1'
union all
select '2.10.1'
union all
select '2.2.1'
)
select * from rawData
order by scol 
```

输出:

| 乙状结肠（sigmoid colon） |
| --- |
| 10.1.1 |
| 1.1.1 |
| 2.10.1 |
| 2.1.1 |
| 2.2.1 |

这是不对的，但是如果我使用 split，输出会很酷

```
with rawData (sCol) as (
select '10.1.1'
union all
select '1.1.1'
union all
select '2.1.1'
union all
select '2.10.1'
union all
select '2.2.1'
)
select * from rawData
order by
split_part(sCol,'.',1)::numeric,
split_part(sCol,'.',2)::numeric,
split_part(sCol,'.',3)::numeric 
```

输出

| 乙状结肠（sigmoid colon） |
| --- |
| 1.1.1 |
| 2.1.1 |
| 2.2.1 |
| 2.10.1 |
| 10.1.1 |

这是 PostgreSQL 代码，但这种技术可以用于其他数据库。