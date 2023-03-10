# 负比较的陷阱

> 原文：<https://dev.to/pesse/pitfalls-of-the-minus-comparison-173i>

我的工作包括许多数据库视图，因为在我们的例子中它们是一种公共 API。自从我开始谈论自测和 [utPLSQL](http://utplsql.org) 以来，当确保一个新的、重建的视图具有与旧视图完全相同的内容时，负比较总是有它的位置。

然而，我做错了，直到最近我发现了一个主要的陷阱。

因为我喜欢讲故事，所以让我们再从一个星球大战的场景开始:我们有一个包含所有星球大战角色的表，另一个包含他们出现在哪一集电影中的信息。

我们有以下样本数据:

| **ID** | **字符** | **集** |
| --- | --- | --- |
| one | 达斯·维达 | 3, 4, 5, 6 |
| Two | 卢克·天行者 | 4, 5, 6, 7, 8 |
| three | 雷伊 | 7, 8 |

我们目前的看法是这样的:

```
create or replace view all_movie_characters
  as
  select
    sw_char.id,
    sw_char.name
  from
    star_wars_characters sw_char
    inner join appearance_in_episode ep
      on sw_char.id = ep.character_fk
  group by sw_char.id, sw_char.name; 
```

Enter fullscreen mode Exit fullscreen mode

然而，我们刚刚发现了一个叫做`appears_in_movie(i_character_id)`的简洁的小函数——看起来我们可以通过使用它使我们的视图更具可读性(不要开始关于性能的争论，*这只是一个虚构的例子*！):

```
create or replace view all_movie_character_2
  as
  select
    sw_char.id,
    sw_char.name
  from
    star_wars_characters sw_char
  where appears_in_movie(sw_char.id) = 1; 
```

Enter fullscreen mode Exit fullscreen mode

因为我们非常小心，我们不仅替换了我们最初的视图，而且给它起了一个新的(非常有创意的)名字，这样我们就可以比较两个视图，确保它们有相同的内容:

```
select * from all_movie_characters
minus
select * from all_movie_character_2
union all
select * from all_movie_character_2
minus
select * from all_movie_characters; 
```

Enter fullscreen mode Exit fullscreen mode

返回了 0 行。太好了！我们可以满怀信心地将这一小小的改进投入生产！

永远不要相信自己。我们来看一下`appears_in_movie`-函数:

```
create or replace function appears_in_movie(
    i_character_id integer
  ) return integer result_cache
  as
    l_count integer;
  begin
    select count(*)
      into l_count
      from appearance_in_episode
      where character_fk = i_character_id
        and episode_no between 4 and 6;
    if l_count > 0 then
      return 1;
    else
      return 2;
    end if;
  end; 
```

Enter fullscreen mode Exit fullscreen mode

不对不对。似乎创建这个功能的人有不同的背景，并且只关心出现在**原版 3 集第 4 到 6 集**中的角色！*(我想在此声明我完全认可目前为止的第 7 集以上)*

选择新视图的结果:

| 1 |达斯·维达|
| 2 |卢克·天行者|

但是发生了什么？为什么我们没有注意到雷伊在新视图中完全消失了？

问题是`UNION ALL`和`MINUS`有[相同的优先级](https://docs.oracle.com/cd/B19306_01/server.102/b14200/queries004.htm)，这意味着它们是按照出现的顺序应用的。事情是这样的:

```
 Darth Vader
  Luke Skywalker
  Rey
MINUS
  Darth Vader
  Luke Skywalker
----------------------
  Rey
UNION ALL
  Darth Vader
  Luke Skywalker
---------------------
  Rey
  Darth Vader
  Luke Skywalker
MINUS
  Darth Vader
  Luke Skywalker
  Rey
--------------------
0 Rows. 
```

Enter fullscreen mode Exit fullscreen mode

为了避免这种情况，我们需要将两个`MINUS`比较放在括号中:

```
(
  select * from all_movie_characters
  minus
  select * from all_movie_character_2
)
union all
(
  select * from all_movie_character_2
  minus
  select * from all_movie_characters
); 
```

Enter fullscreen mode Exit fullscreen mode

返回 1 行:3 Rey

你可以在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/h8lfxm9oc373da6v12j4k3z10) 上找到完整的例子，在我的 [github 库](https://github.com/pesse/sith-demo-db/blob/master/100CodeExamples/13_minus_comparison.sql)上找到注释版本。

### 这是#100CodeExample 吗？

是的，github 上的版本是一个完全注释过的、可运行的例子，和前面的一样。

但是我想尝试一下，通过添加一些故事，只突出关键部分，让博客上的例子更加有趣和平易近人。