# 通过 FireSql 将 NoSQL 引入您的传统灯组项目

> 原文：<https://dev.to/ua1labs/introduce-nosql-into-your-legacy-lamp-stack-projects-with-firesql-13ge>

厌倦了每次需要修改 MySQL 数据结构时都要编写模式更新脚本吗？或者你在 Wordpress 上有一个客户端，你想引入新的数据驱动特性，但是不想花时间去创建和管理新的 MySQL 表和模式来实现你的新功能？

[FireSQL](https://github.com/ua1-labs/firesql) 可能是您正在寻找的解决方案！

在 10 分钟内，你就可以在你的项目中安装并运行 FireSQL。准备开始与数据对象交互。忘记模式吧！就像这样开始插入对象:

```
// connect to database
$dns = 'mysql:host=localhost;dbname=test;port=3306';
$username = 'username';
$password = 'password1';
$pdo = new PDO($dns, $username, $password);
$db = new Fire\Sql($pdo);

// get a collection
$cars = $db->collection('Cars');

// insert $car objects
$cars->insert($car);

// retrieve $car objects
$fordCars = $cars->find('{"make":"ford"}'); 
```

在 2.1.0 的最新版本中，我们已经检查并完整记录了所有源代码，并添加了关于如何使用 FireSQL 的详细自述文件。

在[Packagist.org](https://packagist.org/packages/ua1-labs/firesql)查看