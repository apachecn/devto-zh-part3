# ActiveRecord 的功能以及何时停止使用它

> 原文：<https://dev.to/sooyang/power-of-activerecord-and-when-to-stop-using-it-1ejo>

在 Rails 中，`ActiveRecord`使得从数据库中查询数据变得非常容易——但是它在您的用例中表现如何呢？

# 场景

让我们假设我的 rails 应用程序中有一个`Project`模型。我想在仪表板上显示处于活动和待定状态的项目总数与活动项目总数之比。

大概是这样的:

```
 Team Lead Action (25/100) | Manager Action (32/100) 
```

# 实现

多亏了`ActiveRecord`，我们可以轻松实现获得总计数:

```
 active_projects = Project.where(status: 'active')
  @total_number_of_active_projects = active_projects.size
  @projects_pending_team_lead_approval = active_projects.reject(&:team_lead_action_time).count
  @projects_pending_manager_approval = active_projects.reject(&:manager_action_time).count 
```

瞧啊。我们可以很容易地调用这些变量来在仪表板上显示它们。问题解决了！

然而，我有点担心项目的数量是否会扩大。例如，想象一下，如果我们现在需要显示 5 个不同的值，我们的查询将如下扩展:

```
active_projects = Project.where(status: 'active')
  @total_number_of_active_projects = active_projects.size
  @projects_pending_team_lead_approval = active_projects.reject(&:team_lead_action_time).count
  @projects_pending_manager_approval = active_projects.reject(&:manager_action_time).count
  @projects_pending_manager_2_approval = active_projects.reject(&:manager_2_action_time).count
  @projects_pending_manager_3_approval = active_projects.reject(&:manager_3_action_time).count
  @projects_pending_manager_4_approval = active_projects.reject(&:manager_4_action_time).count
  @projects_pending_manager_5_approval = active_projects.reject(&:manager_5_action_time).count 
```

看起来不太容易扩展。对于这样简单的数据值，应该高效地进行检索。

让我们在`Benchmark`和`N = 1000`的帮助下使用数据来验证事情:

```
 user     system      total        real
count_active_project  0.066184   0.108178   0.174362 (  0.182557) 
```

大约需要`0.18s`。这一次我们只需更改查询:

```
 active_projects = Project.where(status: 'active')
  @total_number_of_active_projects = active_projects.size
  @projects_pending_team_lead_approval = active_projects.where(team_lead_action_time: nil).size
  @projects_pending_manager_approval = active_projects.where(manager_action_time: nil).size 
```

并对其进行基准测试:

```
 user     system      total        real
count_active_projects_ar    0.001460   0.000166   0.001626 (  0.002965) 
```

做同样事情的这个查询只需要`0.002s`——明显更快。不同的是这次我们没有使用`.reject` -(数组方法)，而是依靠`ActiveRecord`。但是为什么呢？让我们看看日志。

使用`.reject`:

```
irb(main):036:0> Project.where(status: 'active').reject(&:team_lead_action_time).count
  Project Load (2.2ms)  SELECT "projects".* FROM "projects" WHERE "projects"."status" = $1  [["status", 0]] 
```

使用`ActiveRecord`:

```
irb(main):040:0> Project.where(status: 'active', team_lead_action_time: nil).size
   (0.9ms)  SELECT COUNT(*) FROM "projects" WHERE "projects"."status" = $1 AND "projects"."team_lead_action_time" IS NULL  [["status", 0]] 
```

从输出中我们可以看到，`.reject`在处理之前将`projects`加载到内存中，而`ActiveRecord`将让数据库处理查询。数据库是快速的，旨在处理这样的过程，因此速度提高。

# 还有原始的 SQL 语句

更进一步，我们可以用 rails 编写原始 SQL。

```
 query = <<~SQL SELECT
      SUM(CASE WHEN status = 0 THEN 1 ELSE 0 END),
      SUM(CASE WHEN status = 0 AND team_lead_action_time IS null THEN 1 ELSE 0 END),
      SUM(CASE WHEN status = 0 AND manager_action_time IS null THEN 1 ELSE 0 END)
    FROM
      PROJECTS SQL

  ActiveRecord::Base.connection.execute(query) 
```

结果是:

```
 user     system      total        real
count_active_projects_sql    0.000250   0.000047   0.000297 (  0.000966) 
```

原始 SQL 只需要大约快 20 倍的`0.0009s`。

查看日志:

```
irb(main):044:0> Project.count_active_projects_sql
   (0.8ms)  SELECT
  SUM(CASE WHEN status = 0 AND team_lead_action_time IS null THEN 1 ELSE 0 END)
FROM
  PROJECTS
=> #<PG::Result:0x00007fa3cdc1c460 status=PGRES_TUPLES_OK ntuples=1 nfields=1 cmd_tuples=1> 
```

原始 SQL 要快得多，因为它不需要实例化一个新的 ActiveRecord 对象(这很慢),而是返回一个`PG::Result`对象。

如果我们增加`N = 100000`，基准测试结果将是:

```
 user     system      total        real
count_active_projects      4.428081   7.626659  12.054740 ( 12.177786)
count_active_projects_ar   0.002107   0.000303   0.002410 (  0.062542)
count_active_projects_sql  0.000380   0.000057   0.000437 (  0.026585) 
```

我们可以清楚地看到，使用原始 SQL 是最快的🏆。

# 外卖

1)最后，我决定使用原始 SQL 方法。考虑到性能，原始 SQL 适合处理这种情况。
2)在原始 SQL 和`ActiveRecord`之间，在大多数情况下`ActiveRecord`对于开发人员的生产力来说更好。
小心使用方法。Ruby `.reject` -(数组方法)很棒，但是要小心使用。

根据不同的场景，解决方案可能会有所不同。我们可以创造性地编写代码，并测试它以满足我们的需求。我们需要保持好奇心和创造力来改进我们的解决方案。

**示例代码可在此处找到[https://github.com/sooyang/query_speed_example](https://github.com/sooyang/query_speed_example)