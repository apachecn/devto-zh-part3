# 递归很难:PostgreSQL 版

> 原文：<https://dev.to/ypkuby/recursion-is-difficult-postgresql-edition-2o6m>

这是最初贴在我的博客上的，在那里你会发现我讨论了从午餐到工程、系统管理和经营我自己的 ISP 的一切事情！

不管开发人员的经验水平如何，递归无疑是最难掌握的事情之一。当正确实现时，递归是美丽的、简单的，并且可以完全无状态。

现在想象一下在 PostgreSQL 中成功实现递归函数，这些函数能够通过各种触发器来维护自己。这个前提很好，一个部署函数和触发器，可以检测表中的更新，并触发一个函数。这个函数接受旧状态和新状态的参数——看起来很简单，是吗？

# 模仿起递归函数

我总是发现在纸上模仿预期的递归函数是获得完整图像的最好方法，在大多数情况下，你不能在头脑中可靠地看到递归。当在纸上写函数时，我通常默认使用我最常用的语言 PHP，例如，如果我想获取旧状态和新状态，并检查 id_link 号是否改变，它看起来像这样:

```
function onUpdate( \psqltable $previous, \psqltable $next ) {
    if( $previous->get('id_link') !== $next->get('id_link') ) {
         // this is now linked to a new parent element, let's rebuild
         rebuild( $previous, $next );
    }
} 
```

我们的最终目标是，如果我们链接到一个新的“id_link”标记，我们希望重建一个完整的树结构，随着情况的变化删除和重新插入新的节点，所有这些都是通过触发器自动完成的。让我们深入了解一下这可能是什么样子...

```
function rebuild( &$previous, &$next ) {
    $children_prev = $previous->children();
    if( count( $children_prev ) > 0 ) rebuild( $children_prev, $next ); // recursion one
    $previous->getCurrent()->reinsert()->change('id_link', $next->get('id_link'))->change('last_updated', time())->apply();
} 
```

如果我们开始分离这个函数，我们可以看到我们得到了当前节点的所有子节点，如果我们有任何子节点，我们再次调用 n 个子节点的 rebuild，直到我们到达堆栈的底部。然后，我们获取对象中的当前索引，将 id_link、last_updated 字段分别更改为父 id_link 和 time。

从表面上看，这似乎很简单，所以这次让我们在 PSQL 中尝试一下吧！

# PSQL 模型

*揭秘:绝不是 PSQL 专家！*

从我们最基本的触发器开始:

```
 DROP TRIGGER IF EXISTS trigger_for_changes ON reference_detail;
  CREATE TRIGGER trigger_for_changes BEFORE INSERT OR UPDATE OR DELETE ON reference_detail FOR EACH ROW EXECUTE PROCEDURE reference_detail_changed();

  CREATE OR REPLACE FUNCTION reference_detail_changed() RETURNS trigger language plpgsql AS $$
    BEGIN
        IF TG_OP = 'UPDATE' OR TG_OP = 'INSERT' THEN
            PERFORM adapt_change( OLD.id_link, NEW.id_link );
            RETURN NEW;
        ELSE
            PERFORM adapt_change( OLD.id_link, OLD.id_link );
            RETURN OLD;
        END IF;
    RETURN NEW;
    END;
  $$; 
```

我决定在实现中改变它，而不是只传递我们的 ID_LINK 来搜索标题表，它包含所有当前对象的元数据。让我们尝试一下我们的函数:

```
 CREATE OR REPLACE FUNCTION adapt_change( old_id_link VARCHAR, new_id_link VARCHAR ) returns integer
  language plpgsql
  as $$
    DECLARE
      meta_row RECORD;
      ref_detail RECORD;
    BEGIN
      IF old_id_link == new_id_link THEN
        DELETE FROM reference_detail WHERE id_link = old_id_link;
        FOR meta_row IN SELECT id_curr_link FROM reference_meta WHERE id_meta_link = old_id_link LOOP
          DELETE FROM reference_meta WHERE id_meta_link = old_id_link; -- clear out meta record
          PERFORM adapt_change( ref_detail.rm.id_link, ref_detail.rm.id_link ); -- parent was destroyed, time to delete ourselves!
        END LOOP;
      ELSE
        UPDATE reference_detail SET id_link = new_id_link WHERE id_link = old_id_link;
        UPDATE reference_meta SET id_curr_link = new_id_link, id_meta_link = new_id_link WHERE id_link = old_id_link;
        FOR meta_row IN SELECT rowid, id_curr_link FROM reference_meta WHERE id_meta_link = old_id_link LOOP
          UPDATE reference_detail SET id_link = new_id_link WHERE id_link = id_curr_link;
          UPDATE reference_meta SET id_curr_link = new_id_link, id_meta_link = new_id_link WHERE id_curr_link = id_curr_link AND rowid = meta_row.rowid;
        END LOOP;
      END IF;
      RETURN 1;
    END;
  $$; 
```

[![Wowsers!](img/996db2c6cd690a350b09654dc41f3863.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ToEZIoYr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kuby.ca/conteimg/2019/02/image-7.png)

这很混乱，但它是完全递归的，让我来分解一下，从我们的第一个 IF 条件开始，我们检查我们的旧 id 是否与我们的新 id 匹配，如果是这样，我们就是一个删除操作，所以我们将删除我们自己，以及我们不再需要的子树。

我们的 ELSE 条件变得有趣多了，让我们在这里单独看一下:

```
UPDATE reference_detail SET id_link = new_id_link WHERE id_link = old_id_link;
UPDATE reference_meta SET id_curr_link = new_id_link, id_meta_link = new_id_link WHERE id_link = old_id_link;
FOR meta_row IN SELECT rowid, id_curr_link FROM reference_meta WHERE id_meta_link = old_id_link LOOP
  UPDATE reference_detail SET id_link = new_id_link WHERE id_link = id_curr_link;
  UPDATE reference_meta SET id_curr_link = new_id_link, id_meta_link = new_id_link WHERE id_curr_link = id_curr_link AND rowid = meta_row.rowid;
END LOOP; 
```

*注意:对于那些不知道的人，使用 PERFORM 是因为我们不关心这种情况下的返回值。*

首先，我们更新细节和元表中的记录，以匹配新的父项。然后，我们开始迭代我们的 meta_row 对象，从元表中获取当前的(id_curr_link ),当我们的 id_meta_link 匹配 id 时，我们只是一个子节点——然后，我们再次开始递归，并开始改变该记录及其所有子节点。

这绝不是一个完美的功能，但对于我目前的用例，即想要删除/更新数据树，它似乎正好符合要求。性能方面，它非常快，从开始到结束平均不到一秒钟。

这是自我管理数据库表的基本实现，由插入/更新/删除触发器和递归函数运行。如果你问我，我会说这很好——让数据库在一定程度上自我维护是很好的，这比使用另一种语言(如 PHP)来维护数据库要好得多。如果做对了，PostgreSQL 会快得令人难以置信——只是做对了需要努力，但这是值得的！