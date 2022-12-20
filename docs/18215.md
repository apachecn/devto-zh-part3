# # 100 code examples–utPLSQL 的套件层次结构

> 原文：<https://dev.to/pesse/100codeexamples--utplsqls-suite-hierarchy-in-action-lk3>

utPLSQL 为您提供了以分层方式组织测试套件的可能性。我们可以使用它将设置/清理方法收集到单独的父包中。

我们不仅可以将我们的设置方法外包到一个单独的地方(遵循 DRY 原则)，而且我们还可以明确几个测试套件以某种方式连接在一起。

```
create table planets (
  id integer primary key,
  name varchar(256)
);

create or replace package ut_planet_setup as
  -- %suite(planetSetup)
  -- %suitepath(galaxy)

  -- %beforeall
  procedure setup_test_planet;
end;
/

create or replace package body ut_planet_setup as
  procedure setup_test_planet
  as
    begin
      insert into planets ( id, name )
        values ( -1, 'Korriban');
    end;
end;
/

create or replace package ut_planets as
  -- %suite(Planets)
  /* Hierarchic Suitepath must contain any parent suitepaths
     and the name of the parent test-package */
  -- %suitepath(galaxy.ut_planet_setup)

  -- %test(Check if test-plantes exist: 1 planet)
  procedure test_planets_exist;
end;
/

create or replace package body ut_planets as
  procedure test_planets_exist
  as
    l_count int;
    begin
      select count(*) into l_count from planets where id < 0;
      ut.expect(l_count).to_equal(1);
    end;
end;
/

create or replace package ut_garrisons as
  -- %suite(Garrisons)
  -- %suitepath(galaxy.ut_planet_setup)

  -- %beforeall
  procedure setup_another_test_planet;

  -- %test(Check if test-plantes exist: 2 planets)
  procedure test_planets_exist;

  /* We could add some more tests for Planet-Garrisons here */
end;
/

create or replace package body ut_garrisons as
  procedure setup_another_test_planet
  as
    begin
      insert into planets ( id, name )
        values (-2, 'Dromund Kaas');
    end;

  procedure test_planets_exist
  as
    l_count int;
    begin
      select count(*) into l_count from planets where id < 0;
      ut.expect(l_count).to_equal(2);
    end;
end;
/ 
```

当我们通过 suitepath 运行 suite 时，父 suite-name 显示为其 displayname，但必须通过其 package-name 调用。开头“:”表示我们寻找合适的路径:

```
call ut.run(':galaxy.ut_planet_setup'); 
```

```
galaxy
   planetSetup
     Planets
       Check if test-plantes exist: 1 planet [,011 sec]
     Garrisons
       Check if test-plantes exist: 2 planets (,003 sec)

 Finished in ,020764 seconds
 2 tests, 0 failed, 0 errored, 0 disabled, 0 warning(s) 
```

如果我们只通过包名调用它，utPLSQL 不会运行子套件

```
call ut.run('ut_planet_setup'); 
```

```
galaxy
   planetSetup

 Finished in ,03545 seconds
 0 tests, 0 failed, 0 errored, 0 disabled, 0 warning(s) 
```

但是对于部分测试，我们可以用特定的测试套件名称来调用它:

```
call ut.run('ut_planets'); 
call ut.run('ut_garrisons'); 
```

```
galaxy
   planetSetup
     Planets
      Check if test-plantes exist: 1 planet (,001 sec)

 Finished in ,02547 seconds
 1 tests, 0 failed, 0 errored, 0 disabled, 0 warning(s)

galaxy
   planetSetup
     Garrisons
      Check if test-plantes exist: 2 planet (,001 sec)

 Finished in ,01228 seconds
 1 tests, 0 failed, 0 errored, 0 disabled, 0 warning(s) 
```

你可以在 [github](https://github.com/pesse/sith-demo-db/blob/master/100CodeExamples/4_utplsql_test_hierarchy.sql) 找到完整的例子。

### 我为什么学这个

我试图清理我的测试代码库

1.  尽可能避免重复
2.  给出一些关于主题/模块之间依赖关系的上下文
3.  减少我的测试需要运行的时间