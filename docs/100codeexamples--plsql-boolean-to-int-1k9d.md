# # 100 code examples–PL/SQL 布尔值到 INT

> 原文：<https://dev.to/pesse/100codeexamples--plsql-boolean-to-int-1k9d>

对我来说，Oracle 数据库的一个主要缺点是缺少布尔 SQL 类型。

是的，boolean 存在于 Oracle 中，但只存在于 PL/SQL 中，而不存在于 SQL 上下文中，这意味着您不能选择 BOOLEAN 类型，也不能通过 JDBC 检索它。

克服这一点的最简单方法是用一种简单的方法将 BOOLEAN 转换成 1/0 INTEGER——有几种方法可以做到这一点:

```
declare
  -- Use a straight if-else approach
  function bool_int_if ( i_bool boolean ) return int
  as
    begin
      if ( i_bool ) then
        return 1;
      else
        return 0;
      end if;
    end;

  -- Use case-when-else
  function bool_int_case( i_bool boolean ) return int
  as
    begin
      return case i_bool when true then 1 else 0 end;
    end;

  -- Use case-when-else with possible NULL return
  function bool_int_case_null( i_bool boolean ) return int
  as
    begin
      return case i_bool when true then 1 when false then 0
             else null end;
    end;

  -- Use the bool_to_int-function of sys.diutil-package
  function bool_int_diutil( i_bool boolean ) return int
  as
    begin
      return sys.diutil.bool_to_int(i_bool);
    end;

  -- Use sys.diutil and nvl to deal with NULL-values
  function bool_int_diutil_nvl( i_bool boolean ) return int
  as
    begin
      return nvl(sys.diutil.bool_to_int(i_bool),0);
    end;
begin

  dbms_output.put_line(
    'IF-approach: ' ||
    bool_int_if(true) || ', ' ||
    bool_int_if(false) || ', ' ||
    bool_int_if(null)
  );

  dbms_output.put_line(
    'CASE-approach: ' ||
    bool_int_case(true) || ', ' ||
    bool_int_case(false) || ', ' ||
    bool_int_case(null)
  );

  dbms_output.put_line(
    'CASE-approach with NULL: ' ||
    bool_int_case_null(true) || ', ' ||
    bool_int_case_null(false) || ', ' ||
    bool_int_case_null(null)
  );

  dbms_output.put_line(
    'DIUTIL-approach: ' ||
    bool_int_diutil(true) || ', ' ||
    bool_int_diutil(false) || ', ' ||
    bool_int_diutil(null)
  );

  dbms_output.put_line(
    'DIUTIL with NVL-approach: ' ||
    bool_int_diutil_nvl(true) || ', ' ||
    bool_int_diutil_nvl(false) || ', ' ||
    bool_int_diutil_nvl(null)
  );

end; 
```

输出:

```
IF-approach: 1, 0, 0
CASE-approach: 1, 0, 0
CASE-approach with NULL: 1, 0,
DIUTIL-approach: 1, 0,
DIUTIL with NVL-approach: 1, 0, 0 
```

您可以在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/htbibfu7s3t7wyqbkzt1lxrun) 上运行这个，但是只能在没有 DIUTIL-Approach 的情况下运行(这个包在 LiveSQL 上不可用)。

对我来说，赢家取决于用例。

如果我想要一个不可空的布尔转换(与 PL/SQL BOOLEAN 不同，PL/SQL BOOLEAN *允许空)，那么 **CASE-approach** 是最容易理解的，它使用最常见的语法和关键字，并且最不冗长。

如果我想要一个可空的转换。DIUTIL 可能正是我所需要的。

更新:感谢亚采克提醒我，一个可空的布尔值可能是一个有效的用例。我更新了帖子和*来说明这一点。

亚采克还指出，CASE 方法并不完全是单责任的，因为它不仅将 PL/SQL 布尔值转换为 INT，还将 NULL 解释为 0。我认为，责任取决于用例。如果我的用例需要不可为空的布尔值，那么转换的责任就是提供不可为空的布尔值。

欢迎你伸出手来和我讨论！*