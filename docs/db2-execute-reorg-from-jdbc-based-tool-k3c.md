# DB2:从基于 JDBC 的工具执行 reorg

> 原文：<https://dev.to/adzubla/db2-execute-reorg-from-jdbc-based-tool-k3c>

使用 DataGrip、DbVisualizer、SQuirreL 或任何其他基于 JDBC 的工具时:

```
CALL SYSPROC.ADMIN_CMD ('REORG TABLE TABLE_NAME') 
```

它应该也可以与其他 db2 客户机本地命令一起工作。