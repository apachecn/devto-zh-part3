# 框架印象:卢蒙简介

> 原文：<https://dev.to/buphmin/framework-impressions-a-brief-look-into-lumen-1l3j>

因此，我花了一些时间来思考本系列的下一部分，以及我真正想深入到每个框架中的深度。对于这篇文章和我在这个系列中完成的其他文章，我将更多地做第一印象，而不是深入探究。这应该更像是一次原型开发会议，而不是全面的特性评审。最终使用(或不使用)框架是个人喜好的问题，我在这些文章中的角色应该是注意以下几点:

*   文件是否清晰易懂
*   这个框架有多通用
*   对性能的快速浏览

这样一来，让我们来看看[流明](https://lumen.laravel.com/)。Lumen 是一个基于 [Laravel](https://laravel.com/) 的微框架，该框架部分基于 [Symfony](https://symfony.com/) 组件。在我对 Lumen 的快速印象中，我会说用法相对简单，尤其是在使用了模仿 Laravel 的 [AdonisJS](https://adonisjs.com/) 之后。

## 文档

关于 Lumen，文档是第一个让我觉得有点奇怪和难以理解的部分。医生有一个坏习惯，基本上是说“这里没有记录，去看看 Laravel 的医生。”这可能没问题，但是 Lumen 中的一些上下文和用法与 Laravel 中的不同，所以有时您必须自己拼凑。当我只是想做一个原始的 sql 查询时，这一点就很明显了。医生说

```
$results = app('db')->select("SELECT * FROM users"); 
```

Enter fullscreen mode Exit fullscreen mode

但我不清楚‘app’是不是一个全球功能。

## 多才多艺

由于 Lumen 的构造方式，你可以自由支配你想做的事情。除了一些你可以使用的东西，大多数都是基于我见过的所有东西的原始 PHP。创建服务相当简单，您可以依靠依赖注入引擎来管理依赖关系。我没有摆弄 ORM，因为这是一个微框架，但是如果你愿意，可以选择使用它们。如果你想的话，Laravel ORM 看起来很有说服力。总的来说，我不认为 Lumen 有什么不好，只要它受 PHP 平台支持。

建立一条路线是相当琐碎的。您可以在 web.php 使用回调或对类函数的字符串引用来定义路由。

```
 $router->get('league_players/raw/{id}', function($id) {
    $sql = "
            select * from 
            league_player lp
            join league l on lp.league_id = l.id
            join player p on lp.player_id = p.id
            where lp.id = :id
        ";

   return app('db')->select($sql, ['id' => $id]);
});

$router->get('league_players/service/{id}', 'LeaguePlayerController@getLeaguePlayer'); 
```

Enter fullscreen mode Exit fullscreen mode

### 依赖注入

Lumen 确实提供了依赖注入，我能够使用类控制器创建一个路由，并向类路由注入一个服务。我没有注意到在使用类方法和 DI 时有很大的开销。

## 表现

所以从现在开始，我要改变我的表现测试。由于我没有单独的机器来运行测试，我将只给出我的两美分和一个回购，您可以使用它来做自己的测试。

运行了几个场景后，我发现 Lumen 接近 Symfony 4，但比 Symfony 4 快。在单一请求下没有区别。当并发请求超过 100 个时，你会注意到 Lumen 领先了。

## 最后的想法

总的来说，我对卢蒙印象不是很好。在当今世界，expressjs 司空见惯，我觉得对 Lumen 的需求并不存在。文档是足够的，但不是一流的，性能(至少是股票)也没有 Symfony 好到可以真正考虑使用它而不是 Symfony。

我会说，如果你想要一个简单的高性能微框架，你最好使用一个全功能的 PHP 框架，或者选择 express(和 learning node.js)。如果你真的想坚持使用 php，另一个选择是用一些简单的设置创建你自己的。

我对卢蒙的印象就到此为止了。请在评论中告诉我你的想法。

接下来应该看什么框架/微框架？我在想 js/go 里的东西，让我知道！

编辑:

我忘了连接回购，所以你可以玩我做了什么！[https://github.com/buphmin/LumenComparison](https://github.com/buphmin/LumenComparison)