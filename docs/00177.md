# 从 postgres 学到的新东西

> 原文：<https://dev.to/andi/new-stuff-from-postgres-learned-4ml6>

# 手表

如果您已经打开了`psql` shell，并且想要在 n 秒内刷新某个命令，在执行该命令一次后，只需键入，例如 10 代表每 10 秒刷新一次

```
\watch 10 
```

# 安全定义者-作为所有者运行程序/功能

默认行为:fun 由执行用户运行，您可以在定义您的过程后添加`SECURITY DEFINER`来轻松更改它，例如:

```
CREATE OR REPLACE FUNCTION public.foo() 
RETURNS VOID AS $$

-- do whatever you need here and remember

$$ LANGUAGE plpgsql SECURITY DEFINER; 
```

*   作用域内的`user`是函数的所有者——定义它的人(`effective user`
*   为了让用户真正调用它，你可能需要调用包装函数，而不需要`SECURITY DEFINER`抓住用户并把它传递给作为`SECURITY DEFINER`运行的函数。

仅供参考，默认修饰符称为`SECURITY INVOKER`。

[https://www . PostgreSQL . org/docs/current/SQL-create function . html](https://www.postgresql.org/docs/current/sql-createfunction.html)

# pg_stat_activity

每个服务器进程占一行，显示与该进程的当前活动相关的信息，如状态和当前查询。使用`ddl_command_start`查找`event trigger`中缺少的更多上下文信息时，会发现它非常有用。

[https://www . PostgreSQL . org/docs/9.2/monitoring-stats . html # PG-STAT-ACTIVITY-VIEW](https://www.postgresql.org/docs/9.2/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW)

# 调试级别

```
SET client_min_messages TO DEBUG5; 
```

这里描述的从`DEBUG5`到`PANIC`全部可用[https://www . PostgreSQL . org/docs/9.1/RUNTIME-CONFIG-logging . html # RUNTIME-CONFIG-SEVERITY-LEVELS](https://www.postgresql.org/docs/9.1/runtime-config-logging.html#RUNTIME-CONFIG-SEVERITY-LEVELS)

在`PL/PgSQL` :
中使用

```
 RAISE NOTICE `running as user: %', session_user; 
```

在`plpythonu` :
中使用

```
plpy.debug('query: {}'.format(query)) 
```