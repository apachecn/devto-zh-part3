# # 100 code examples–使用 utPLSQL 游标测试可更新视图

> 原文：<https://dev.to/pesse/100codeexamples-test-updatable-views-with-utplsql-cursors-1g59>

我们已经看过了 [utPLSQL 与用户定义类型](https://dev.to/pesse/100codeexamples--utplsql-cursor-comparison-with-user-defined-types-25nm)的游标比较。如果我们必须处理可更新视图，我们可以使用更简单的方法，从%ROWTYPEs 创建嵌套表集合。

这个场景和上一个类似:我们有许多有或没有驻军的星球。现在我们希望能够用 garrison-ID 更新视图，然后自动创建一个新的 garrison-entry。

```
/* Setup base data objects */
create table planets (
  id integer primary key,
  name varchar(256)
);

create table garrisons (
  id integer primary key,
  fk_planet integer not null,
  constraint garrisons_fk_planet foreign key ( fk_planet )
    references planets( id )
);

/* This is the view we want to test, a list of all
   planets and their garrisons (if they have some) */
create or replace view v_planet_garrisons as
select
  p.id planet_id,
  p.name planet_name,
  count(g.id) over (partition by p.id) planet_num_of_garrisons,
  g.id garrison_id
from planets p
  left outer join garrisons g on p.id = g.fk_planet
;

/* Lets first create our test */
create or replace package ut_garrisons as
  -- %suite(Garrisons)

  -- %test(Update V_PLANET_GARRISONS: Add garrison to a planet without assigned garrisons)
  procedure update_view_set_garrison;

  /* We can define a nested table of a %ROWTYPE */
  type t_v_planet_garrisons is
    table of v_planet_garrisons%rowtype;
end;
/

create or replace package body ut_garrisons as

  /* Helper-Function to get a cursor of the view
     by Planet-ID */
  function cursor_v_garrisons( i_id simple_integer)
    return sys_refcursor
  as
    c_result sys_refcursor;
    begin
      open c_result for
        select *
          from v_planet_garrisons
          where planet_id = i_id;
      return c_result;
    end;

  /* Helper-Function to get a cursor from a given
     ROWTYPE-object
   */
  function cursor_from_row(
    i_row v_planet_garrisons%rowtype
  ) return sys_refcursor
  as
    /* We first need an initialized collection */
    l_table t_v_planet_garrisons := t_v_planet_garrisons();
    c_result sys_refcursor;
    begin
      /* We can then put our given row inside
         that collection */
      l_table.extend;
      l_table(1) := i_row;
      /* And then create a cursor on it */
      open c_result for
        select * from table(l_table);
      return c_result;
    end;

  procedure update_view_set_garrison
  as
    l_expected_row v_planet_garrisons%rowtype;
    begin
      -- Arrange
      insert into planets values (-1, 'Dromund Kaas');

      /* We store the current row-value in
         a variable we can manipulate */
      select * into l_expected_row
        from v_planet_garrisons
        where planet_id = -1;

      -- Act: Update the view, setting a garrison-ID
      update v_planet_garrisons
        /* In a realistic scenario garrison_id
           could be populated by a sequence */
        set garrison_id = -2
        where planet_id = -1;

      -- Assert
      /* We now define how our expected row should
         look like */
      l_expected_row.garrison_id := -2;
      l_expected_row.planet_num_of_garrisons := 1;

      ut.expect(cursor_v_garrisons(-1))
        .to_equal(cursor_from_row(l_expected_row));
    end;

end;
/

/* It fails because of a missing trigger */
call ut.run('ut_garrisons');

/* which we can easily add */
create or replace trigger trg_save_planet_garrisons
  instead of update or insert or delete on v_planet_garrisons
  for each row
  declare
    begin
      if ( updating ) then
        if ( :old.garrison_id is null
             and :new.garrison_id is not null )
        then
          insert into garrisons (id, fk_planet)
            values ( :new.garrison_id, :new.planet_id);
        end if;
      end if;
    end;
/

/* It doesnt fail anymore */
call ut.run('ut_garrisons'); 
```

不幸的是，utPLSQL 仍然没有在 LiveSQL 中运行，但是您可以像往常一样从我的 [GitHub 存储库](https://github.com/pesse/sith-demo-db/blob/master/100CodeExamples/utplsql/12_utplsql_test_update_with_cursor_comparison.sql)中获得整个示例。

## 我为什么学这个

因为可更新视图是我工作中公共 API 的主要部分，所以我必须处理很多。有一种简单的方法来比较视图的单行和预期的结果是一个很大的改进。