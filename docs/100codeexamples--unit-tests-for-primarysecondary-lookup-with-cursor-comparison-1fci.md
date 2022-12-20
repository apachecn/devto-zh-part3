# # 100 code examples–通过光标比较进行主要/次要查找的单元测试

> 原文：<https://dev.to/pesse/100codeexamples--unit-tests-for-primarysecondary-lookup-with-cursor-comparison-1fci>

为了确保对我们的死亡之星的主要/次要电源节点的[查找正确工作，并在我们的应用程序的未来变化中保持工作，我们希望使用](https://dev.to/pesse/100codeexamples--a-challenge-to-learn-and-teach-primaryreplica-lookup-knk) [utPLSQL](http://utplsql.org) 的光标比较功能进行一些基本的单元测试。

测试包标题:

```
create or replace package ut_deathstar_power_nodes as

  -- %suite(Deathstar power nodes - Primary/Secondary lookup)
  -- %suitepath(deathstar.powerNodes)

  -- %test(Get a result even if there is no secondary power node)
  procedure get_result_for_only_one_entry;

  -- %test(Get whole group of related power nodes, no matter which node-ID queried)
  procedure get_whole_group_multiple_secondaries;

end;
/ 
```

Enter fullscreen mode Exit fullscreen mode

测试包体:

```
create or replace package body ut_deathstar_power_nodes as

  function cursor_actual( i_power_node_id integer )
    return sys_refcursor
  as
    c_result sys_refcursor;
    begin
      -- Just get all results from our View-under-test
      -- for a given ID
      open c_result for
        select *
          from v_deathstar_grouped_power_nodes
          where power_node_id = i_power_node_id
          -- Order of rows is important for comparison
          order by member_id desc;

      return c_result;
    end;

  procedure get_result_for_only_one_entry
  as
    c_expected sys_refcursor;
    begin
      -- Arrange: Make sure we have proper test-data
      -- available, using negative primary keys.
      -- Will be rolled back after test
      insert into deathstar_power_nodes(id, label, primary_node_fk)
        values ( -1, 'Primary 1', null );

      -- Assert via cursor comparison
      -- Create expectation-cursor
      open c_expected for
        select
          -1 power_node_id,
          -1 group_id,
          -1 member_id,
          'Primary 1' member_label,
          1 is_primary
        from dual;

      -- Compare the two cursors
      ut.expect(cursor_actual(-1))
        .to_equal(c_expected);
    end;

  procedure get_whole_group_multiple_secondaries
  as
    -- We need a fresh cursor for each expectation,
    -- so we put it in a sub-function we can easily call
    function cursor_expected return sys_refcursor
    as
      c_expected sys_refcursor;
      begin
        -- Define Expectation-Cursor for this test
        open c_expected for
          -- Column-aliases are only necessary on
          -- first select
          select
            null power_node_id,
            -1 group_id,
            -1 member_id,
            'Primary 1' member_label,
            1 is_primary
          from dual
          union all select null, -1, -2, 'Secondary 1', 0 from dual
          union all select null, -1, -3, 'Secondary 2', 0 from dual;

        return c_expected;
      end;

    begin
      -- Arrange
      insert into deathstar_power_nodes(id, label, primary_node_fk)
        values ( -1, 'Primary 1', null );
      insert into deathstar_power_nodes(id, label, primary_node_fk)
        values ( -2, 'Secondary 1', -1 );
      insert into deathstar_power_nodes(id, label, primary_node_fk)
        values ( -3, 'Secondary 2', -1 );

      -- Assert
      -- Check first entry of testdata: Primary 1
      ut.expect(cursor_actual(-1))
        .to_equal(cursor_expected())
        -- Ignore column POWER_NODE_ID, because it doesn't matter
        -- That way we can use the same expectation cursor
        -- for all checks
        .exclude('POWER_NODE_ID');

      -- Check Secondary 1
      ut.expect(cursor_actual(-2))
        .to_equal(cursor_expected())
        .exclude('POWER_NODE_ID');

      -- Check Secondary 2
      ut.expect(cursor_actual(-3))
        .to_equal(cursor_expected())
        .exclude('POWER_NODE_ID');

    end;

end;
/ 
```

Enter fullscreen mode Exit fullscreen mode

测试通话:

```
call ut.run('ut_deathstar_power_nodes'); 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，我不能提供一个 LiveSQL 的例子，但是你可以在这里找到一个完整的 SQL 脚本。

脚本包含创建必要的死亡之星 _ 电源 _ 节点-表和视图，安装测试包和清理所有对象。

在任何安装了 utPLSQL 的环境中运行它，作为一个工作示例。