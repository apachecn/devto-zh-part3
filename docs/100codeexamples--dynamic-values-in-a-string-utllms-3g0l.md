# # 100 code examples–字符串中的动态值:UTL_LMS

> 原文：<https://dev.to/pesse/100codeexamples--dynamic-values-in-a-string-utllms-3g0l>

如果您想使用 PL/SQL 在字符串中添加动态值，可以像这样连接它:

```
l_alertMessage := 'A bunch of "' || l_attacker ||
  '" is attacking with an estimated fleet of ' ||
  to_char(l_numOfShips) || ' ships'; 
```

但这两者都很难阅读，而且编写起来也很乏味，因为我们必须用 TO_CHAR 显式地转换所有不是 varchar2/nvarchar2 的内容。

然而，有几种更好的方法来实现这个目标。一种方式是使用 [UTL_LMS](https://docs.oracle.com/cd/B19306_01/appdev.102/b14258/u_lms.htm#i999311) 。

```
declare
  l_alertMessage varchar2(200) :=
    'A bunch of "%s" is attacking with ' ||
    'an estimated fleet of %d ships';
begin
  -- Works with values
  dbms_output.put_line(utl_lms.format_message(
    l_alertMessage,
    'Values', 10));

  -- Doesnt replace anything if no values are provided
  dbms_output.put_line(utl_lms.format_message(
    l_alertMessage));

  -- Replaces missing values with empty string
  dbms_output.put_line(utl_lms.format_message(
     l_alertMessage,
     'Not_all_values_set'));

  -- Works with NVARCHAR and BINARY_INTEGER types
  declare
    l_inputString nvarchar2(40) := 'NVARCHAR2/BINARY_INTEGER';
    l_numOfShips binary_integer := 25;
  begin
    dbms_output.put_line(utl_lms.format_message(
      l_alertMessage, l_inputString, l_numOfShips));
  end;

  -- Works with VARCHAR and subtypes of BINARY_INTEGER like PLS_INTEGER
  declare
    l_inputString varchar2(40) := 'VARCHAR2/PLS_INTEGER';
    l_numOfShips pls_integer := 75;
  begin
    dbms_output.put_line(utl_lms.format_message(
      l_alertMessage, l_inputString, l_numOfShips));
  end;

  -- Order is important
  declare
    l_inputString varchar2(40) := 'Wrong Order';
    l_numOfShips pls_integer := 122;
  begin
    dbms_output.put_line(utl_lms.format_message(
       l_alertMessage, l_numOfShips, l_inputString));
  exception when others then
    dbms_output.put_line('Wrong Order: ' || sqlerrm);
  end;

  -- Fails silently with INTEGER types
  declare
    l_inputString varchar2(40) := 'INTEGER';
    l_numOfShips integer := 13;
  begin
    dbms_output.put_line(utl_lms.format_message(
      l_alertMessage, l_inputString, l_numOfShips));
  end;

  -- Fails silently with NUMBER types
  declare
    l_inputString varchar2(40) := 'NUMBER';
    l_numOfShips number(10,0) := 34;
  begin
    dbms_output.put_line(utl_lms.format_message(
      l_alertMessage, l_inputString, l_numOfShips));
  end;

  -- You can escape % with doubling it
  dbms_output.put_line(utl_lms.format_message(
    'Probability to survive: %s%%', to_char(12.5)));
end;
/ 
```

输出:

```
A bunch of "Values" is attacking with an estimated fleet of 10 ships
A bunch of "%s" is attacking with an estimated fleet of %d ships
A bunch of "Not all values set" is attacking with an estimated fleet of ships
A bunch of "NVARCHAR2/BINARY_INTEGER" is attacking with an estimated fleet of 25 ships
A bunch of "VARCHAR2/PLS_INTEGER" is attacking with an estimated fleet of 75 ships
Wrong Order: ORA-06502: PL/SQL: numeric or value error
A bunch of "INTEGER" is attacking with an estimated fleet of ships
A bunch of "NUMBER" is attacking with an estimated fleet of ships
Probability to survive: 12.5% 
```

您可以在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/hn6oxzoqm3aesk0wmgpipnqqp) 上找到完整的工作示例。

### 我为什么学这个

*(这是一个新问题，我将在接下来的每一个代码示例中尝试回答)*

我目前正在寻找方法来提高包含几个动态部分的消息的可读性。这可能是一种可能性，尽管我目前看到的限制:

*   限于 VARCHAR2 和 BINARY_INTEGER(无日期或时间戳)
*   不支持替换多次出现的值
*   值的位置提供更难读取/维护，并且更容易出错