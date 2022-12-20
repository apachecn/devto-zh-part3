# 揭开 Sparrow6 的未来特性

> 原文：<https://dev.to/melezhik/unveiling-the-future-features-of-sparrow6-20b3>

大约一个月前，我宣布麻雀迁移到 Perl6。

[路的](https://github.com/melezhik/Sparrow6/blob/master/Roadmap.md)比我预期的要长，但这是值得的。

事实上，我现在更好地理解了 Perl6，因为我从头开始重写了相当大的代码库，这也给了我一个机会来重新思考整个设计，并带来一些新的特性。

这是一个。**任务状态**这是[陪练 6](https://github.com/melezhik/Sparrow6) 的一个*杀手*特色。我在类似的自动化系统(chef 和 ansible)中找不到任何类似的东西。

现在，任务的状态作为一个散列对象返回到主场景，这样状态数据*就可以在进一步的任务中使用*。让我举个例子:

```
 #!perl6

my %state = task-run "keyvault secret", "azure-kv-show", %(
  kv      => "{config<kv>}",
  secret  => [
    "connection-string-dev"
  ]
);

say "checking connection: {%state<connection-string-dev>}";

task-run "check login", "mssql-check-login", %(
  dsn => %state<connection-string-dev>
); 
```

在这个场景中，调用了两个后续任务。第一个从 key vault 中获取带有 azure 证书的数据库连接字符串，并将其转储到屏幕上，*最初的*目的只是打印 kay vault 中的机密，没有其他目的。

有一天，我想我也想测试一下返回的连接字符串是否有效，是否可以用来连接数据库。所以我希望任务像函数一样返回值。这个想法在我的脑海里已经有一段时间了，直到我有机会看到这个特性的一个合适的用例。

现在我可以运行第二个任务，检查连接字符串是否允许连接到数据库，使用连接字符串执行 sqlcmd 命令。

该功能允许*从一个任务返回数据*，并且*使用*该数据作为另一个任务的*输入参数*，从而实现“流水线”任务。

这在老麻雀身上是不可能的。在这种情况下，只有两个后续的任务不能将数据从一个传递到另一个。

* * *

敬请关注。你可以在 [Roadmap.md](//ttps://github.com/melezhik/Sparrow6/blob/master/Roadmap.md) 文件中找到 [Sparrow6](https://github.com/melezhik/Sparrow6) 项目的状态。