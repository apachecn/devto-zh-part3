# 为什么 Rails ActiveRelation.update_all 更新的是一组不同的记录？

> 原文：<https://dev.to/andy/why-is-rails-activerelationupdateall-updating-a-different-set-of-records-404g>

我有一个奇怪的案例，魔法做了一些意想不到的事情。给定如下:

```
comment = Comment.find(params[:id])
# select only the original commenter's comments that match two scenarios
related_comments = comment.where(something: params[:something)
  .union(comment.where(something_else: params[:something_else]))
  .where(user_id: comment.user_id)

related_comments.count
#=> 4
# This is to demonstrate how many records I'm intending to update.
# Using .length would work too, for example's sake.

related_comments.update_all(receive_notifications: true)
#=> SQL (3.9ms)  UPDATE "comments" SET "receive_notifications" = 't'
#=> WHERE "comments"."user_id" = $1  [["user_id", 11]] [sql_query]
#=> 12 
```

Enter fullscreen mode Exit fullscreen mode

从表面上看，`.update_all`正在运行一个 SQL 查询，用一个为 11 的`user_id`来更新所有的注释。从技术上讲，`.where(user_id: 11)`是最后一个查询，但是它不应该只为`related_comments`的关系运行 SQL 查询吗？

我有一个解决方法可以做到这一点，但是这需要两个 SQL 查询，而且看起来过于麻烦:

```
comment = Comment.find(params[:id])
# same as above
related_comment_ids = comment.where(something: params[:something)
  .union(comment.where(something_else: params[:something_else]))
  .where(user_id: comment.user_id).pluck(:id)
#=> [12, 14, 17, 18]

Comment.where(id: related_comment_ids).update_all(receive_notifications: true)
#=> SQL (5.7ms)  UPDATE "comments" SET "receive_notifications" = 't'
#=> WHERE "comments"."id" IN (18, 14, 17, 12) [sql_query]
#=> 4 
```

Enter fullscreen mode Exit fullscreen mode