# 我如何解决这个不可能的 SQL 查询？

> 原文：<https://dev.to/andy/how-i-do-solve-this-impossible-sql-query-275k>

所以我试着从 dev.to 的数据库里收集一些数据。不幸的是，SQL 不是我的强项，我大部分时间都在开发应用程序的 Ruby + Rails 端。不可能可能不是一个恰当的词，但对我来说似乎不太可能，因为我已经为此工作了 4 个小时。😭

我想从几个表中获取数据:

*   `articles`表、
*   `collections`表、
*   `reactions`表，和
*   `page_views`表。

`articles`表中有我需要的几列:`id`、`title`、`published_at`、`positive_reactions_count`和`cached_tag_list`。

`collections`表只有一个我需要的列:`slug`。这与`articles`所在的`articles.collection_id = collections.id`有直接关系。

`reactions`表与`articles`表的多态关系，其中一个`reaction`有一个`reactable_id = articles.id`和`reactable_type = 'Article'`。我需要`SUM`统计每个帖子的赞、独角兽和阅读列表反应的总数。

`page_views`表有我需要的三列:`counts_for_number_of_views`、`time_tracked_in_seconds`、`referrer`。这与`articles`所在的`page_views.article_id = articles.id`有直接关系。我需要将`counts_for_number_of_views`和`AVG`的`time_tracked_in_seconds`向上`SUM`。

我尝试显示如下所示的表格结果:

| 文章 id | 标题 | 发布于 | 缓存标签列表 | 系列 _ 标题 | 总反应 | 总点赞数 | 合计 _ 独角兽 | 总计 _ 书签 | 总浏览量 | 平均花费时间(秒) | 推荐人 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| one | 这个帖子 | 2019-04-18 | sql，postgresql，帮助 | " SQL 帮助" | one | one | one | five | Thirty | [https://dev.to](https://dev.to) |  |

到目前为止，我有一个无效的查询——还有一些我不理解的可怕的变体——看起来像这样:

```
SELECT articles.id,
      articles.title,
      articles.published_at,
      articles.positive_reactions_count AS total_reactions,
      articles.cached_tag_list,
      collections.slug AS series_title,
      (SELECT COUNT(reactions.id) from reactions where reactions.category = 'like' AND reactions.reactable_id = articles.id AND reactions.reactable_type = 'Article') AS total_likes,
      (SELECT COUNT(reactions.id) from reactions where reactions.category = 'unicorn' AND reactions.reactable_id = articles.id AND reactions.reactable_type = 'Article') AS total_unicorns,
      (SELECT COUNT(reactions.id) from reactions where reactions.category = 'readinglist' AND reactions.reactable_id = articles.id AND reactions.reactable_type = 'Article') AS total_bookmarks,
      (SELECT SUM(page_views.counts_for_number_of_views) from page_views where page_views.article_id = articles.id) AS total_views,
      (SELECT AVG(page_views.time_tracked_in_seconds) from page_views where page_views.article_id = articles.id) AS total_views,
FROM articles
FULL JOIN collections ON collections.id = articles.collection_id
INNER JOIN reactions ON reactions.reactable_id = articles.id AND reactions.reactable_type = 'Article'
INNER JOIN page_views ON page_views.article_id = articles.id
WHERE articles.user_id = my_id
  AND articles.published = TRUE 
```

Enter fullscreen mode Exit fullscreen mode

我认为查询和示例结果表有助于我试图描述的内容。这肯定超出了我的能力范围，但也许比我想象的要容易得多？

## 更新:

我添加了一个额外的`AND articles.id = my_article_id`来加快查询速度。看起来它跑了！然而，由于某种原因，我得到了 952 行。我认为我的加入用法可能不正确...