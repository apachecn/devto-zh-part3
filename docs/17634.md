# SQLSTATE[HY000] [2002]连接被拒绝

> 原文：<https://dev.to/aleeshapjohn/sqlstatehy000-2002-connection-refused-4lm2>

嗨，我正在使用 xampp 服务器运行我的 php laravel 应用程序。我使用命令“php artisan migrate”进行迁移，我得到了这个错误:

:SQLSTATE[HY000] [2002]连接被拒绝(SQL:select * from information _ schema . tables，其中 table_schema = testing，table_name = migrations)

我的 env 文件被修改成这样:
DB _ CONNECTION = MySQL
DB _ HOST = 127 . 0 . 0 . 1
DB _ PORT = 3306
DB _ DATABASE = testing
DB _ USERNAME = root
DB _ PASSWORD =

其中“测试”是我的数据库名称。我尝试了很多方法，也交叉验证了我的数据库证书。
我需要这方面的帮助