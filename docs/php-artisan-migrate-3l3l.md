# php 工匠迁移

> 原文：<https://dev.to/highcenburg/php-artisan-migrate-3l3l>

我被一个潜在雇主要求用 Laravel 和 Mysql 创建一个 CRUD 应用程序，但当我运行时出现错误:

```
php artisan migrate
```

I've read all links on the first page of **Google** and **Duckduckgo** already but I couldn't find the answer to my problem.

我运行 **php artisan migrate** 时的错误是:

```
Illuminate\Database\QueryException  : SQLSTATE[HY000] [2054] The server requested authentication method unknown to the client (SQL: select * from information_schema.tables where table_schema = Laravel-tasks and table_name = migrations)

在/Users/my _ username/PHP/todo/vendor/laravel/framework/src/Illuminate/Database/connection . PHP:664
660 |//如果在尝试运行查询时发生异常，我们将格式化错误
 661| //消息以包括与 SQL 的绑定，这将使该异常成为对开发人员更有帮助的
 662| //而不仅仅是数据库的错误。
 663| catch(异常$e) {

664 | throw new query exception(
665 | $ query，$ this->prepare bindings($ bindings)，$ e
666 |)；【667 | }【668 | T3】

异常跟踪:

1 PDO exception::(" PDO::_ _ construct():服务器请求了客户端未知的认证方法[caching _ sha2 _ password]")
/Users/my _ username/PHP/todo/vendor/laravel/framework/src/Illuminate/Database/Connectors/connector . PHP:70

2 PDO::_ _ construct(" MySQL:host = 127 . 0 . 0 . 1；端口= 3306；dbname=Laravel-tasks "，" root "，" YGM4QV6kWnjz3uN！"，[])
/Users/my _ username/PHP/todo/vendor/laravel/framework/src/Illuminate/Database/Connectors/connector . PHP:70
```

这是我在项目文件夹中执行的命令行

```
php artisan make:migration create_tasks_table
```

这是 create_tasks_table.php 上的代码:

```

bigIncrements('id');
            $table->string('title');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('tasks');
    }
}

```

上的数据库。环境是:

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=Laravel-tasks
DB_USERNAME=root
DB_PASSWORD=*****
```

希望你有经验的回应！

干杯！