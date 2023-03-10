# # 100 code examples–使用原力替换对象

> 原文：<https://dev.to/pesse/100codeexamples--use-the-force-to-replace-objects-5b0o>

在前面的一个[示例](https://dev.to/pesse/100codeexamples--plsql-objects-basics-of-force-sensitive-beings-1275)中，我们学习了 PL/SQL 中对象的基本用法，通过它我们可以在 oracle 数据库中开始面向对象的开发。

然而，对象更常见的用法是将它们用作嵌套表集合(对象表)的基本类型。这样我们就可以从 SQL 访问对象，这是从 PL/SQL 函数返回表结构的一种非常强大的可能性。

当我们想要改变一个已经在表类型中被引用的对象时，麻烦就开始了:我们得到 ORA-02303 依赖错误。和往常一样，这里的“力”可以有所帮助:

```
-- We can use attribute-only objects without
-- any methods. We dont even have to specify
-- a body in that case
create or replace type t_disturbance is object
(
  planet_name varchar2(1000),
  strength number(10,4)
);
/

-- And we can create a table of objects
-- so we can select objects from SQL
create or replace type t_disturbances
  is table of t_disturbance;
/

-- Little helper function to create
-- disturbances
create or replace function get_disturbances
  return t_disturbances
as
  l_result t_disturbances := t_disturbances();
  begin
    l_result.extend;
    l_result(1) := new t_disturbance('Alderaan', 10000);
    return l_result;
  end;
/

select *
from table(get_disturbances());

-- What if we want to change the underlying object now?
create or replace type t_disturbance is object
(
  planet_name varchar2(1000),
  strength number(10,4),
  alignment varchar2(10)
);
/

-- Error.
-- The problem is, that our object-type is a
-- dependency of the table-type.
-- But we can use the force (since 11.2)!
create or replace type t_disturbance force is object
(
  planet_name varchar2(1000),
  strength number(10,4),
  alignment varchar2(10)
);
/

-- Caution! This doesnt work if the type is used
-- in a table:
create table disturbance_history (
  id integer not null primary key,
  disturbance t_disturbance,
  occured timestamp with local time zone
    default current_timestamp
);

create or replace type t_disturbance force is object
(
  planet_name varchar2(1000),
  strength number(10,4),
  alignment varchar2(10),
  cause_name varchar2(1000)
);
/

-- So lets just not do this for the moment
drop table disturbance_history;
/

create or replace type t_disturbance force is object
(
  planet_name varchar2(1000),
  strength number(10,4),
  alignment varchar2(10),
  cause_name varchar2(1000)
);
/

-- Check for invalid objects
select * from user_objects where status  'VALID';

-- The table-type is invalid, but 
-- we can just compile it without any
-- additional change
alter type t_disturbances compile;

-- We now need to adapt our helper function
-- because the default constructor of 
-- objects requires each attribute to be
-- passed
create or replace function get_disturbances
  return t_disturbances
as
  l_result t_disturbances := t_disturbances();
  begin
    l_result.extend;
    l_result(1) := new t_disturbance('Alderaan', 10000, null, null);
    return l_result;
  end;
/

select *
from table(get_disturbances()); 
```

这个例子可以在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/hyqyfannrmnlh1pqhpo2jpdl1) 上运行。关于 [github](https://github.com/pesse/sith-demo-db/blob/master/100CodeExamples/objects/9_table_objects_and_the_force.sql) 的完整源代码。

我在 10g 上开始了我的 PL/SQL 编码，记得有几个脚本只是删除了一个表类型，改变了底层的对象类型并再次创建了表类型。

事情是这样的:我甚至在更新到 11 和 12 之后还在使用那个方法，因为我直到最近才知道这个强大的小助手。也许我不是唯一的一个——如果是这样，请让我知道🙂