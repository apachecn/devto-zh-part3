# 时隔 8 年多，PHP 应用程序复活

> 原文：<https://dev.to/lancew/resurrecting-a-php-app-after-8-years-388f>

最近，有人提醒我，作为一个为期两年的学术研究项目的一部分，我创建的一个网站已经死了([http://www.rwjl.net](http://www.rwjl.net))，所以我开始让它起死回生。

## 第一步……代码在哪里？

所以我最后一次在这个网站上工作是在 2012 年左右。所以我没有可用的原始源代码…所以我可能有某种形式的源代码控制…但它不在我的 github repos 中；或者在我的笔记本上。

所以我开始通过 sftp 直接从网站上下载文件。然后初始化一个 git repo，并将其作为第一次提交(在通读检查了所有我不希望出现在历史记录中的凭证等之后)。

## 第二步……什么叫破？

总而言之，有两个主要问题需要解决。

1.  PHP 5 -> 7
2.  SQLite 2 -> 3

所以最初的网站是 PHP5，主机提供商已经自动将网站升级到 PHP7。所以在最初的调查中，旧的 SQLite 调用失败了，因为接口已经改变了，所以我需要解决这个问题。这里有一个例子:

老:

```
$dbo = new SQLiteDatabase("$dbfile");
$query = "SELECT * FROM players";
$result = $dbo->query($query) or die("Error in query");
while ($result->valid()) {
    $row = $result->current();
    ...
    $result->next();
} 
```

Enter fullscreen mode Exit fullscreen mode

这不再有效，所以需要重写一点如下:

```
$dbo = new SQLite3("$db");
$query = "SELECT * FROM players";
while ($row = $result->fetchArray()) {
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这是问题解决的第一部分，第二个问题是，从上面的代码可以看出，PHP7 是 SQLite3，我所有的旧数据文件都是 2010-2012 年的，都是 SQLite3 所以是时候迁移数据了。

这个其实很简单。我所要做的就是在我的笔记本电脑上安装 sqlite2，这样我就安装了 sqlite3 和 sqlite2。那么到 so 的迁移就是简单地将 sqlite2 版本转储到 sqlite3。例如

```
sqlite sqlite2/01_ijfgrand_prix2010_tunis.db | 01_ijfgrand_prix2010_tunis.db 
```

Enter fullscreen mode Exit fullscreen mode

我有大约 100 个这样的任务要做，所以写了一个快速 bash 循环将所有内容转储到文件，然后另一个循环将它们导入到 sqlite3…我不会粘贴到这里，因为它很难看。[![😉](img/6ca7e2fdb02e679784ddd93209c425c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k4nhOwvc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11/72x72/1f609.png%3Futm_source%3Drss%26utm_medium%3Drss)

我必须做的另一个修正是数组初始化，这在 PHP5 中是可能的(尽管很糟糕),但在 PHP7 中显然是无效的，所以我修改了这个:

```
$scores = '';
...
$scores[] = $change; 
```

Enter fullscreen mode Exit fullscreen mode

哪成了:

```
$scores = array();
...
$scores[] = $change; 
```

Enter fullscreen mode Exit fullscreen mode

这种模式只有一个实例，所以我假设最初$scores 在某个阶段是一个字符串，然后我开始将它用作数组。我猜 PHP5 更宽松一些；但是 PHP7 加强了更好的初始化。

## 步骤 3…清理并修复测试。

坦白说，我没有让这个网站以“正确”的方式运行。我得到了这个工作，然后开始考虑测试。这是在过去，因为我最初写这个网站的方式，我没有使用依赖管理，所以我的 simpletest 版本是过时的，测试不会运行等。

我决定务实一点，首先让网站正常工作(有人给我发了电子邮件，所以当务之急是让数据重新上线……而不是为这个博客练习正确的做事方式)。

现在一切都正常了，我开始删除一些东西。所有版本的网站已被“备份”到子目录。我没有通过 composer 这样的工具引入的库被删除了。

当我需要它们时，我创建了一个 composer.json 并使用它来引入依赖项。这些不会出现在回购协议中。所以下次让我困惑的就少了。

我最初在代码上使用了 PHP-code sniffer；所以我也将它添加到 composer.json 中，并在代码中运行它，应用了它建议的许多改进。

## 总结

作为一名全职 Perl 开发人员，很高兴看到我从 2010 年到 2012 年用 PHP 编写和维护的一个 web 应用程序。代码在我 2019 的眼里是如此的潦草；这是我的事，不是 PHP 的事。

PHP 和 Perl 在语法和“外观”方面非常接近，所以我发现从 Perl 跳到 PHP 比我最近从 Perl 跳到语法完全不同的地方要容易得多，当然还要适应不同的类型和结构。

让网站起死回生，重新访问一个当时对我意义重大的项目，并在互联网上看到一个完整的(尽管很小)web 应用程序，这是一件令人愉快的事情。

我不打算对网站或代码做太多，但现在已经把它推到 Github 上，所有的数据和代码都可供研究人员或其他开发人员使用……或者是我，当我再次忽视它时，再过 8 年，我必须让这个网站再次起死回生。