# DB2:重启身份

> 原文：<https://dev.to/adzubla/db2-restart-identity-4cgd>

如何重新启动标识列的值？

```
ALTER TABLE <table_name> ALTER COLUMN <identity_column_name> RESTART WITH <counter_value> 
```