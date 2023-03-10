# ★laravel-query-builder 的重要安全版本

> 原文：<https://dev.to/freekmurze/an-important-security-release-for-laravel-query-builder-12n8>

我们的 [laravel-query-builder](https://github.com/spatie/laravel-query-builder) 包暴露了一个严重的安全问题:它允许 SQL 注入攻击。Laravel Query Builder v1.17.1 现已上市，修复了该漏洞。如果您正在使用该软件包，请立即停止阅读，首先升级到最新版本。对于 Laravel 5.6、5.7 和 5.8，如果您使用 1.17.1 版是安全的。对于 Laravel 5.5，您应该使用 1.16.1 版。任何其他版本都被认为是不安全的。

## 问题

laravel-query-builder 允许您根据请求过滤、排序和包含有说服力的关系。

假设您想让所有用户按电子邮件排序:

```
https://mysite.com/users?sort=email 
```

在端点上，您可以使用我们的包来构建基于 URL 参数的查询。

```
use Spatie\QueryBuilder\QueryBuilder;

$users = QueryBuilder::for(User::class)->get();
// all `User`s sorted by email 
```

在引擎盖下，我们将其转换为:

```
User::orderBy('email')->get(); 
```

Laravels 内部查询构建器将把它转换成下面的 SQL 查询:

```
select * from `users` order by `email` asc 
```

这一切都在按计划进行。然而，当有人输入以下排序查询时，问题就出现了:

```
https://mysite.com/users?sort=email->"%27))%23injectedSQL 
```

在引擎盖下，我们的软件包将把它转换成这个:

```
User::orderBy('email->"%27))%23injectedSQL')->get(); 
```

您可能认为您会得到一个 MySQL 错误，因为您在一个不存在的列上进行排序，但事实并非如此。Laravel 会将上面的代码转换成这个 SQL 查询。乍一看，生成的查询似乎很奇怪，但是它 100%有效，MySQL 将执行它。

```
select * from `users` order by json_unquote(json_extract(`email`, '$.""'))#injectedSQL"')) asc 
```

Laravel 将解析 URL 中的`->`,并替换为那些 JSON MySQL 函数。查询的`"%27))`部分将允许我们打破开放字符串，并导致函数提前关闭，之后我们可以添加任何我们想要的 SQL 语句。在这种情况下，我们只是添加了一个友好的注释。但是心怀不轨的人可以在那里添加任何他们想要的恶意代码。

在这个资源库中，您会发现一个演示该问题的测试。

## 修复此问题

我们通过在包处理列名的任何地方只允许字母数字字符、`-`和`_`来修复这个问题。这是[我的同事亚历克斯做的相关承诺](https://github.com/spatie/laravel-query-builder/commit/3aa483b63c79d9fabcb8653fe837a7736eb93bea)。

## 在关闭

您可能认为 Laravel 应该受到责备，因为它无法生成安全的 MySQL 查询。但在我看来，这不是拉弗尔的错。文档[声明](https://laravel.com/docs/master/queries#introduction)你不应该清理作为绑定传递的字符串。

> Laravel 查询生成器使用 PDO 参数绑定来保护您的应用程序免受 SQL 注入攻击。不需要清理作为绑定传递的字符串。

我可以想象有一些应用程序在请求中把`field_name`像`https://mysite.com/some-resource?orderby=field_name`一样传递给`orderBy`函数。每当在查询中指定列名时，传递安全字符串是您的责任。在我看来，Laravel 文档可以更加强调这一事实。

编辑:同时[laravel 文档中增加了警告](https://laravel.com/docs/master/queries#introduction)。

非常感谢我的同事亚历克斯和 T2 调查此事，感谢乔纳斯·斯陶登梅尔报道最初的问题。也感谢[努诺·马杜罗](https://twitter.com/enunomaduro)和[德赖斯·温特斯](https://twitter.com/driesvints)校对这篇博文。