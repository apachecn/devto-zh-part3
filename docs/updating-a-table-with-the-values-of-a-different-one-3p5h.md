# 用不同表的值更新表

> 原文：<https://dev.to/pesse/updating-a-table-with-the-values-of-a-different-one-3p5h>

我从事数据库专业工作已经超过 15 年，非常关注 Oracle——但是我真的忘记了如何用不同的值更新一个表(顺便说一下，这在 SQL Server 中要容易得多)。

因此，让我们假设我们有一个包含行星的表和一个包含这些行星上的驻军的表。

| **驻军 ID** | **星球名称** | **星球派系** |
| --- | --- | --- |
| one | 科里班 | 绝对权 |
| Two | 科里班 | 绝对权 |
| three | Dromund Kaas | 绝对权 |
| four | 霍斯 | 共和国 |

我们现在希望 garrisons 表中有一个新列，其中可以包含一个名称。

```
alter table garrisons add name varchar2(300) 
```

Enter fullscreen mode Exit fullscreen mode

帝国方面现在有一个请求，要求根据这个模式更新他们所有的驻军名称: ()

获取所需信息的基本选择查询如下:

```
select
  garrisons.id,
  planets.name
  from
    garrisons
    inner join planets
      on garrisons.fk_planet = planets.id
  where
    planets.faction = 'imperium' 
```

Enter fullscreen mode Exit fullscreen mode

## 可能性#1: PL/SQL FOR 循环

是的，这是丑陋和缓慢的，但它显示了我们本质上想要做的:

```
begin
  for rec in (select
    garrisons.id,
    planets.name
    from
      garrisons
      inner join planets
        on garrisons.fk_planet = planets.id
    where
      planets.faction = 'imperium'
  )
  loop
    update garrisons
      set name = rec.name || ' (' || to_char(rec.id) || ')'
      where id = rec.id;
  end loop;
end; 
```

Enter fullscreen mode Exit fullscreen mode

## 可能性#2:相关更新

如果您搜索“Oracle update table from results of another table”，这是最值得推荐的方法，但是我发现它很难读懂，而且我从来不记得如何正确地做到这一点:

```
update garrisons
  set name = (
    select
        planets.name || ' (' || to_char(garrisons.id) || ')'
      from planets
      where planets.id = garrisons.fk_planet
    )
where exists ( -- Limitation to Imperium
  select 1 from planets
    where planets.id = garrisons.fk_planet
      and planets.faction = 'imperium'
  ); 
```

Enter fullscreen mode Exit fullscreen mode

## 可能性三:合并

是的，这看起来很不错，因为我们可以保留最初的查询，我发现它非常有表现力。然而，您可能会假设一个`MERGE`语句来处理`INSERT`，并且您不能更新在`ON`子句中使用的列(这在可能性#2 中是可能的):

```
merge into garrisons target
  using (
    select
      garrisons.id,
      planets.name
      from
        garrisons
        inner join planets
          on garrisons.fk_planet = planets.id
      where
        planets.faction = 'imperium'
  ) source
  on (target.id = source.id)
  when matched then
    update set
      target.name = source.name || ' (' || to_char(source.id) || ')'; 
```

Enter fullscreen mode Exit fullscreen mode

## 可能性#4:内嵌-视图更新

这看起来很简洁，但是仅限于[保留键的表](https://asktom.oracle.com/pls/asktom/f?p=100:11:::::P11_QUESTION_ID:548422757486) :

```
update (
  select
    garrisons.id,
    garrisons.name garrison_name,
    planets.name planet_name
    from
      garrisons
      inner join planets
        on garrisons.fk_planet = planets.id
    where
      planets.faction = 'imperium'
)
  set garrison_name = planet_name || ' (' || to_char(id) || ')'; 
```

Enter fullscreen mode Exit fullscreen mode

和往常一样，你可以在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/h9fxpvfssti0lql9cr85kw4p6) 和我的 [GitHub 库](https://github.com/pesse/sith-demo-db/blob/master/100CodeExamples/14_update_table_from_another_table.sql)上找到完整的例子。