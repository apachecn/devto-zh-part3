# # 100 code examples–utPLSQL 游标与用户定义类型的比较

> 原文：<https://dev.to/pesse/100codeexamples--utplsql-cursor-comparison-with-user-defined-types-25nm>

上周四，我很荣幸地在卡尔斯鲁厄向当地的 DOAG 用户组展示了 utPLSQL，展示后的一个问题是我们如何控制游标比较的预期结果。

我们已经有了一个使用 *SELECT FROM DUAL* 和 *UNION ALL* 的[示例](https://dev.to/pesse/100codeexamples--unit-tests-for-primarysecondary-lookup-with-cursor-comparison-1fci)，但是有时我们的预期结果更复杂。

在这种情况下，我们可以使用用户定义的类型和嵌套表集合的强大功能:

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

create or replace package ut_garrisons as
  -- %suite(Garrisons)

  -- %beforeall
  procedure setup_planets_and_garrsions;
  -- %test(V_PLANET_GARRISONS returns results for planets with and without garrisons)
  procedure select_v_planet_garrisons;

  /* To select from user-defined types in SQL (so we can use
     cursor-comparison) we need to define them on spec-level
     so they are visible. */
  type t_info_record is record (
    planet_id integer,
    planet_name varchar2(256),
    planet_num_of_garrisons integer,
    garrison_id integer
  );

  /* Nested tables are the only collections that can be accessed
     from SQL */
  type t_info_table is table of t_info_record;
end;
/

create or replace package body ut_garrisons as

  procedure setup_planets_and_garrsions
  as
    begin
      /* Insert test-data with negative primary keys
         so we dont have collisions with existing data */
      insert into planets values (-1, 'Dromund Kaas');
      insert into planets values (-2, 'Korriban');

      /* We want only one planet to have garrisons.
         To better distinct the PKs, we give different to
         the garrisons than to the planets */
      insert into garrisons (id, fk_planet ) values ( -10, -1 );
      insert into garrisons (id, fk_planet ) values ( -11, -1 );
    end;

  /* A little helper-function to get a cursor for
     the current values of our view, limited by entries
     with planet_id < 0, so we only get test-data */
  function cursor_current_planet_garrisons
    return sys_refcursor
  as
    c_result sys_refcursor;
    begin
      open c_result for
        select * from v_planet_garrisons where planet_id < 0
          /* Ordering is very important to get the results
             exactly as we expect them */
          order by planet_id desc, garrison_id desc;
      return c_result;
    end;

  procedure select_v_planet_garrisons
  as
    /* Remember to initialize the table-collection */
    l_expected_table t_info_table := t_info_table();
    c_expected sys_refcursor;
    begin
      /* Populate our table-type with the expected results */
      l_expected_table.extend;
      l_expected_table(1).planet_id := -1;
      l_expected_table(1).planet_name := 'Dromund Kaas';
      l_expected_table(1).planet_num_of_garrisons := 2;
      l_expected_table(1).garrison_id := -10;

      l_expected_table.extend;
      l_expected_table(2).planet_id := -1;
      l_expected_table(2).planet_name := 'Dromund Kaas';
      l_expected_table(2).planet_num_of_garrisons := 2;
      l_expected_table(2).garrison_id := -11;

      l_expected_table.extend;
      l_expected_table(3).planet_id := -2;
      l_expected_table(3).planet_name := 'Korriban';
      l_expected_table(3).planet_num_of_garrisons := 0;
      l_expected_table(3).garrison_id := null;

      /* Open expected-cursor for our populated data */
      open c_expected for
        select * from table(l_expected_table);

      /* Now we can do an easy cursor-comparison */
      ut.expect(cursor_current_planet_garrisons())
        .to_equal(c_expected);
    end;

end;
/

call ut.run('ut_garrisons'); 
```

遗憾的是，它在 LiveSQL 中无法运行，但你可以像往常一样在 [Github](https://github.com/pesse/sith-demo-db/blob/master/100CodeExamples/utplsql/8_utplsql_cursor_comparison_with_table_types.sql) 上获得完整的源代码。

要了解 utPLSQL 游标比较的信息有多大意义，只需更改一个预期值。或者删除一行。