# 了解查询对象

> 原文：<https://dev.to/matthewbdaly/understanding-query-objects-4g42>

我去年维护的项目继承了一个相当可疑的数据库结构，该结构目前很难重构，这也使得许多查询变得更加复杂。目前，我正忙于建立一个全新的主页，这就需要添加一些新的查询。因为其中一些涉及到在几个相似的表(应该是一个表，grr…)之间执行联合，所以对于每个查询，它们可能涉及到一些相当大的块。

因此，进一步分解这些查询是有意义的。由于 Zend 1 没有任何类似于雄辩中的作用域的东西，我真的没有一种简单的方法来在模型中分解这些查询(无论如何，我目前正试图将查询逻辑从模型中分离出来)，所以我选择将它们转换成查询对象，这是一种我以前没有使用过的模式(但可能应该有)。

查询对象就像它在 tin 上显示的一样——它是一个 PHP 对象，执行一个非常具体的查询。这可能看起来有点过了，但是它只对最复杂和令人费解的查询有用。如您所料，它可以接受参数，基于这一点，查询的某些部分可能是可选的，但基本上它应该只构建和运行一个查询。

在这篇文章中，我将介绍如何创建一个，它与存储库模式的关系，以及何时创建一个。

# 创建查询对象类

我非常喜欢 PHP 中的`__invoke()`魔法方法。对于外行来说，它允许你实例化类，然后像使用函数一样使用它，这使得它对回调非常有用。这也带来了其他一些好处:

*   与函数不同，您可以创建私有方法来完成其他部分的工作，从而更容易理解 main 方法。
*   它可以有一个构造函数，因此既可以通过构造函数接受依赖项，也可以通过依赖项注入进行实例化，与使用回调相比，简化了设置和测试。
*   由于`__invoke()`是 PHP 语言固有的一部分，对于只负责使用那个方法名的类来说，比选择像`handle()`或`run()`这样的名字更有意义。

因此，我的查询对象通常使用`__invoke()`方法来触发查询。

因为不再支持 Zend 1，所以我就不展示如何在特定的上下文中编写查询了。我还没有在 Laravel 中使用这个模式，但是如果我使用了，它看起来会像这样:

```
<?php

namespace App\Queries;

use Illuminate\Database\DatabaseManager;

final class DashboardItems
{
    protected $db;

    public function __construct(DatabaseManager $db)
    {
        $this->db = $db;
    }

    public function __invoke(int $days = 7)
    {
        return $this->fooTable()
            ->union($this->barTable())
            ->whereRaw('start_date >= (NOW() - INTERVAL ? DAY)', [$days]);
            ->get();
    }

    private function fooTable()
    {
        return $this->db->table('foo')
            ->where('type', '=', 'fooType');
    }

    private function barTable(int $days)
    {
        return $this->db->table('bar')
            ->where('type', '=', 'barType');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们将想要执行`UNION`的每个表分解成一个私有方法。这可能是查询对象的最大优势——它让您将特别笨拙的查询分解成逻辑步骤，使它们更具可读性。您也可以通过在存储库类上添加私有方法来实现这一点，但是我不愿意将私有方法添加到只在一个查询中使用的存储库中——在我看来，查询对象更适合这样做。

# 那么存储库呢？

我经常在我的代码库中使用存储库模式，无论是对于 Laravel 项目还是当前基于 Zend 1 的遗留项目。重构它是一项持续的工作，这样所有的查询都可以从存储库类中调用，让模型充当数据的容器。那么查询对象如何适应这里呢？

需要注意的是，虽然存储库代表与表相关的所有查询，但是查询对象只代表一个查询，因此存储库仍然应该是调用查询的地方。然而，存储库应该将实际的查询委托给查询对象。我当前应用程序的应用程序结构的相关部分看起来有点像这样:

```
└── app
    ├── Queries
    │ └── DashboardItems.php
    └── Repositories
        └── DashboardRepository.php 
```

Enter fullscreen mode Exit fullscreen mode

并且存储库可以如下调用查询对象:

```
<?php

namespace App\Repositories;

use App\Queries\DashboardItems;

final class DashboardRepository
{
    public static function dashboardItems(int $days = 7)
    {
        $query = new DashboardItems;
        return $query($days);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

目前，我的存储库都使用静态方法，因为我仍在将查询迁移到存储库类的过程中。这也意味着我不能轻易使用依赖注入。对于 Laravel 应用程序，类似的调用可能如下所示:

```
<?php

namespace App\Repositories;

use App\Queries\DashboardItems;

final class DashboardRepository
{
    protected $dashboardQuery;

    public function __construct(DashboardItems $dashboardQuery)
    {
        $this->dashboardQuery = $dashboardQuery;
    }

    public function dashboardItems(int $days = 7)
    {
        return $this->dashboardQuery($days);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

唯一真正的区别是，我们可以在容器之外实例化查询对象，从而简化设置。

# 何时使用查询对象

我认为这可能是不言而喻的，但它应该是一个实际上需要作为查询对象实现的罕见查询，特别是如果您使用的是像 concertive 这样的 ORM，它提供了像 scopes 这样的功能，到目前为止我只有两个使用这种模式，还有两个实现为“reporter”类，但也可能是查询对象。到目前为止，我的经验是，值得考虑的大型查询包括:

*   生成报告的查询，特别是当它们有各种选项时
*   使用联合的查询，如上例所示，因为使用私有方法获取每个表是有意义的
*   具有多个复杂连接的查询

较小的查询通常适合放在存储库类的单个方法中。如果是这样的话，那么他们可以在那里生活而不会有麻烦。但是，如果您的查询变得太大，无法包含在单个方法中，那么与其将私有方法添加到存储库类中，不如将其重构为一个查询对象。您仍然可以在您的存储库类上通过相同的方法调用它，但是存储库可以遵从查询对象。因为我通常使用 decorators 来缓存来自我的存储库类的响应，所以坚持使用这种方法来保持缓存的一致性也是有意义的。

查询对象实际上只为特别大的查询提供任何值。然而，在这种情况下，它们是无价的。通过将这些大的查询分解成一系列的步骤，它们有助于使它们更容易理解。