# # 100 代码示例–学习和教学的挑战:初级/次级查找

> 原文：<https://dev.to/pesse/100codeexamples--a-challenge-to-learn-and-teach-primaryreplica-lookup-knk>

不久前， [Gregory Brown](https://practicingdeveloper.com/) 提出了做一个“100 个代码示例”挑战的想法:

> 除了 [#100DaysOfCode](https://twitter.com/hashtag/100DaysOfCode?src=hash&ref_src=twsrc%5Etfw) ，似乎 [#100CodeExamples](https://twitter.com/hashtag/100CodeExamples?src=hash&ref_src=twsrc%5Etfw) 也是一个有趣的挑战。
> 
> 目标是在一两年的时间里制作 100 个小的带注释的示例程序，说明你学到的所有东西。
> 
> —实践开发者([@ Practicing dev](https://dev.to/practicingdev))[2018 年 10 月 8 日](https://twitter.com/practicingdev/status/1049362543867822080?ref_src=twsrc%5Etfw)

我非常喜欢这个想法，原因有几个:

*   这个挑战可能会帮助我追踪/意识到我学到了什么
*   将我所学的分解成小样本会巩固知识
*   希望这也能锻炼我教授和分享知识的技能

我不会给自己施加压力，因为这不是挑战的重点，但如果我能做到，我会在我的博客上发布 1-2 个示例，这样 100 个代码示例应该可以在大约一年内完成。

示例的主题很可能与我当前的工作相关，所以主要是 SQL、PL/SQL，我还将尝试添加几个关于 utPLSQL 的示例。每个例子都附有对目标和关键片段的简要解释——希望以一种可以理解的方式进行注释(我非常感谢您对后一部分的反馈！).

如果可能的话，我还会分享一个到 LiveSQL 或一个不同环境的链接，在那里例子可以立即运行(不会那么容易地与 [utPLSQL](http://utplsql.org) 例子一起工作)。

# 示例:主要/次要查找

我们经常遇到主要/次要的情况，有时我们需要一种简单的方法来根据单个条目的 ID 获取所有相关的条目。

在我的星球大战的例子中，死亡之星运行在几个电源节点上，其中一些有备用节点以防主电源节点不再工作。

我们想要创建一个视图，可以用任何电源节点的 ID 进行查询，不管是主节点还是次节点，并获得相关电源节点的完整列表作为结果。

节点的基本表非常简单:

| 圆柱 | 限制 |
| --- | --- |
| 身份证明 | 主键，非空 |
| 标签 |  |
| 主节点 FK |  |

视图可能是这样的:

```
create or replace view v_deathstar_grouped_power_nodes as
  -- We first need to arrange the related nodes into groups
  with node_groups as (
    select group_entry.id group_id, -- Group-ID is the ID of the primary node
           members.id member_id
      from deathstar_power_nodes group_entry
      -- We join the base-table with itself to get all
      -- related entries for each row
      inner join deathstar_power_nodes members
            -- using either the primary-node reference,
            -- or if it's NULL (because it *is* the
            -- primary node) the ID
            on group_entry.id = nvl(members.primary_node_fk, members.id)
      -- We only do this for primary nodes
      where group_entry.primary_node_fk is null
  )
  select nodes.id power_node_id,
         groups.group_id,
         groups.member_id,
         member.label member_label,
         case
           when member.primary_node_fk is null then 1
           else 0
         end is_primary
    from deathstar_power_nodes nodes
    -- We join the groups via primary-node reference (if it exists) or the ID (of the primary node)
    inner join node_groups groups
           on nvl(nodes.primary_node_fk, nodes.id) = groups.group_id
    -- To get more information than only the ID of the group-members we need to join our base table again
    inner join deathstar_power_nodes member
           on groups.member_id = member.id; 
```

Enter fullscreen mode Exit fullscreen mode

您可以在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/hm1p0fchy2tjrl51nfdzf0o5r) 上找到完整的工作示例。

这在本周的生产场景中非常方便。

注意:对于较大的数据集，这可能会带来性能问题。

*更新 2018-12-05:将措辞从主/副本更改为主/辅助，因为这样可能更容易理解。也使 WITH-clause 中的措辞更具表现力。谢谢法布西！*