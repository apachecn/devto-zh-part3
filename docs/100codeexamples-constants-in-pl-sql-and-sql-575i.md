# # 100 code examples–PL/SQL 和 SQL 中的常量

> 原文：<https://dev.to/pesse/100codeexamples-constants-in-pl-sql-and-sql-575i>

我们经常在代码库的几个地方处理相同的东西，比如名字、id、代码或者类似的东西。当我们把它们写得稍有不同时，问题就出现了，我们通常不会注意到这一点，因为这不会产生编译错误。

此外，这些东西一旦设置，通常就不可更改，因此大多数编程语言都提供了常量。PL/SQL 也是这样做的，但是将这些常量放入 SQL 可能有点棘手。

```
create table characters_of_force (
  name varchar2(128) not null primary key,
  alignment varchar2(10) not null,
  constraint characters_of_force_chk
    check (alignment in ('dark','light','neutral'))
);

insert into characters_of_force
  values ('Darth Vader', 'dark');
insert into characters_of_force
  values ('Luke Skywalker', 'light');
insert into characters_of_force
  values ('Aurra Sing', 'neutral');

select * from characters_of_force;

/* We want to have constants for the possible alignment-
   values so typos are less likely and our IDE can help us
*/
create or replace package alignment_types as
  /* the constant keyword prevents the package variable
     from being overridden at a later time */
  dark constant varchar2(10) := 'dark';
  light constant varchar2(10) := 'light';
  neutral constant varchar2(10) := 'neutral';
end;
/

/* We can now use the constants from PL/SQL.
   Note how we dont need a package body if we just
   want to provide constants */
begin
  dbms_output.put_line(
    'Anakin was first aligned to '
    || alignment_types.light
    || ' but turned to '
    || alignment_types.dark
    || ' later');
end;
/

/* But we can not access package variables from SQL */
select alignment_types.light from dual;

/* To overcome this we can change our package */
create or replace package alignment_types as
  /* No public constants anymore,
     but functions without parameters */
  function light return varchar2;
  function dark return varchar2;
  function neutral return varchar2;
end;
/

create or replace package body alignment_types as
  /* Here we have our constants again,
     hiding them from public access */
  g_dark constant varchar2(10) := 'dark';
  g_light constant varchar2(10) := 'light';
  g_neutral constant varchar2(10) := 'neutral';

  /* The functions are our only publicly available item */
  function light return varchar2
  as
    begin
      return g_light;
    end;

  function dark return varchar2
  as
    begin
      return g_dark;
    end;

  function neutral return varchar2
  as
    begin
      return g_neutral;
    end;
end;
/

/* Now we can access the constant from SQL, too */
select alignment_types.light from dual;

/* We can, however, not use it in the check constraint */
alter table characters_of_force
  drop constraint characters_of_force_chk;

alter table characters_of_force
  add constraint characters_of_force_chk
  check ( alignment in
    (alignment_types.light,
     alignment_types.dark,
     alignment_types.neutral)
  );

/* But we can work around this with a virtual column */
create or replace package alignment_types as
  function light return varchar2;
  function dark return varchar2;
  function neutral return varchar2;
  /* We need a deterministic check-function */
  function valid_type(i_value in varchar2)
    return int deterministic;
end;
/

create or replace package body alignment_types as
  g_dark constant varchar2(10) := 'dark';
  g_light constant varchar2(10) := 'light';
  g_neutral constant varchar2(10) := 'neutral';

  function light return varchar2
  as
    begin
      return g_light;
    end;

  function dark return varchar2
  as
    begin
      return g_dark;
    end;

  function neutral return varchar2
  as
    begin
      return g_neutral;
    end;

  /* This function can just check against our constants */
  function valid_type(i_value in varchar2)
    return int deterministic
  as
    begin
      if ( i_value in (light, dark, neutral)) then
        return 1;
      else
        return 0;
      end if;
    end;
end;
/

/* We create a virtual column with this function */
alter table characters_of_force
  add (alignment_check number generated always as (
    alignment_types.valid_type(alignment)
  ));

/* And can then check the content of this virtual column */
alter table characters_of_force
  add constraint characters_of_force_chk
  check ( alignment_check = 1 );

insert into characters_of_force ( name, alignment )
  values ('Anakin Skywalker', 'confused'); 
```

这个例子可以在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/h18u4127zffd00qpeyr5bxrx6) 上找到。

### 我为什么学这个

常量很棒有很多原因，我试图在我编写的任何语言中使用它们。

也许对我来说最重要的事情是，它们允许我的 IDE 完成大部分的打字工作，并且常量的使用将那些不被注意的打字错误变成了产生编译错误的错误。