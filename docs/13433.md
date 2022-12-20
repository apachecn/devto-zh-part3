# 如何在 Magento 2 上导入导出 admin 用户？

> 原文：<https://dev.to/rafaelcg/how-to-import-and-export-admin-users-on-magento-2-bng>

您的管理员用户数据在 Magento 2 数据库的表 **admin_user** 和 **admin_passwords** 中，您可以使用这些命令导出，然后在第二个商店中导入:

**出口**T2】

```
$ mysqldump -p --user=username dbname admin_user admin_passwords > myAdminUsers.sql 
```

Enter fullscreen mode Exit fullscreen mode

**进口**

```
$ mysql -u username -p -D dbname < myAdminUsers.sql 
```

Enter fullscreen mode Exit fullscreen mode

> **重要**
> 
> 如果您使用数据库前缀，不要忘记在上面的这些命令中使用。