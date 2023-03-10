# Symfony 介绍|现代 PHP 应用的基础

> 原文：<https://dev.to/brpaz/an-introduction-to-symfony--the-foundation-of-modern-php-applications-ehj>

在这篇文章中，我将对 Symfony 做一个简单的介绍，并演示如何使用它来构建现代的高性能 PHP 应用程序。

本文基于写作时的最新 Symfony 版本(Symfony 4.2)

## 什么是 Symfony

根据官方网站:

> Symfony 是一组 PHP 组件、一个 Web 应用程序框架、一种理念和一个社区——它们和谐地一起工作。

在我看来，这个定义真实地展示了 Symfony 背后的主要思想。Symfony 不应该被视为像 Laravel 或 Rails 一样的全栈 web 框架，默认情况下，它包括了构建传统网站所需的一切，如数据库访问或模板引擎，如果你正在构建 API，这些东西可能并不需要。
相反，它是一组模块化组件，您可以通过多种方式组合它们来构建强大的应用程序。
它遵循从小处着手，按需添加更多功能的方法。

这些组件可以一起使用以获得完整的 Symfony 体验，也可以在各种 PHP 项目中单独使用。Symfony 组件是许多流行 PHP 项目的基础，包括 Laravel、Drupal、Prestashop 等等。

您可以在此找到所有可用组件的列表[。](https://symfony.com/components)

## 为什么是 Symfony

选择 Symfony 作为 PHP 框架有很多原因:

*   当创建一个新的 Symfony 项目时，只需要安装引导框架和处理 HTTP 生命周期所需的核心组件，这使得它非常轻量级，非常适合微服务或 API。然后，您可以根据需要添加其他组件来构建更复杂的应用程序。
*   令人难以置信的模块化得益于它的组件系统。
*   遵循 OOP 设计模式和最佳实践(如依赖注入)的干净代码。
*   实现了许多 PHP-FIG 标准，如 PSR-2(代码样式)、PSR-3(日志接口)、PSR-4(自动加载器)、PSR-6(缓存接口)、PSR-11(服务容器)，允许与其他库轻松交互。
*   不依赖于特定的数据库系统/ ORM、模板引擎等。
*   很棒的文档。
*   持续关注改进开发体验和性能。
*   稳定和成熟，但总是使用最新的 PHP 特性。
*   良好定义的发布过程和长期支持，以及向后兼容性的巨大妥协，使其成为企业或更复杂项目的完美选择。
*   根据 PHP 基准测试，它是目前最快的 PHP 框架。
*   许多基于 Symfony 的优秀平台支持快速应用开发，如 [API 平台](https://api-platform.com/)。 [EasyAdminBundle](https://symfony.com/doc/master/bundles/EasyAdminBundle/index.html) 或 [Sylius](https://sylius.com/) 。

## 谁用 Symfony

Symfoyn 被全球很多热门公司使用，包括
[Spotify](https://spotify.com) 、 [Blablacar](https://www.blablacar.com) 、 [Trivago](https://trivago.com) 、[球场英雄](https://www.coursehero.com/)甚至 [YouPorn](https://youporn.com) 。

因此，如果你怀疑 Symfony 和 PHP 的性能或可伸缩性，请记住 YouPorn 是用 Symfony 构建的；)

## 入门

* * *

### 系统要求

Symfony 4 需要 PHP 7.1.3 或更高版本才能运行，此外还有其他次要要求。为了简单起见，Symfony 提供了一个[工具](https://symfony.com/doc/current/reference/requirements.html)来快速检查你的系统是否满足所有这些要求。

### 创建新的 Symfony 项目

创建一个新的 Symfony 项目最简单的方法是使用[作曲](https://getcomposer.org/)

```
composer create-project symfony/skeleton devto-symfony 
```

Enter fullscreen mode Exit fullscreen mode

这个命令将下载所有需要的依赖项并创建基本的目录结构。

接下来，我们将安装[symfony/web-server-bundle](https://packagist.org/packages/symfony/web-server-bundle)，它在为 Symfony 应用程序优化的内置 PHP webserver 之上提供了一个层。

```
cd devto-symfony
composer require server --dev 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以用下面的命令启动您的应用程序:

```
 php bin/console server:run 
```

Enter fullscreen mode Exit fullscreen mode

如果您在网络浏览器中打开 [http://127.0.0.1:8000/](http://127.0.0.1:8000/) ，您应该会看到 Symfony 欢迎页面。

注: Symfony 最近宣布了一个新的 [symfony cli](https://symfony.com/doc/master/cloud/getting-started) 和本地 web 服务器，这可能会成为创建新 Symfony 项目的新标准方式。

## 捆绑系统

在上一步中，我们安装了所谓的 Symfony 捆绑包(symfony/web-server-bundle)。束是 Symfony 世界中的一个重要概念。

捆绑包类似于其他软件中的插件/模块，允许跨 Symfony 应用程序共享代码或将第三方代码与框架粘合。

Symfony 框架的核心特性也是用捆绑包实现的。在 Symfony 以前的版本中，建议将你自己的应用程序代码组织成捆绑包，但在 Symfony 4 中却不是这样，因为捆绑包现在是由 Symfony 团队想象出来的，被专门使用。在应用程序之间共享代码，并将第三方代码与 Symfony 集成。你可以在这里阅读更多关于捆绑包的信息。

### 目录结构

让我们看看 Symfony 4 项目的目录结构。

```
.
├── bin
├── composer.json
├── composer.lock
├── config
├── phpunit.xml.dist
├── public
├── src
├── symfony.lock
├── templates
├── tests
├── translations
├── var
└── vendor
└── .env 
```

Enter fullscreen mode Exit fullscreen mode

我认为目录名很能说明问题:

*   bin ->这里是任何可执行文件或脚本。您应该已经有了至少一个“console.php”文件，这是 Symfony 控制台可执行文件。您将经常使用控制台来运行有用的命令。
*   config ->存储应用程序配置文件。路由、服务定义和捆绑包配置都在这个目录中。
*   src ->这是您将放置应用程序代码的文件夹。你可以把 src/文件夹里面的内容组织成你想要的样子。
*   public ->应用程序的主入口点(index.php)和任何公共可访问的文件，如 js 或 css 文件，都将转到这个目录。
*   模板->树枝视图。如果不需要，您可以删除该目录。
*   测试->您的 PHPUnit 测试
*   翻译->您的应用程序可能需要的任何翻译文件。
*   var ->缓存、日志和其他临时文件。
*   供应商->随 composer 安装的任何第三方软件包
*   。env ->在开发过程中存储应用程序环境变量的文件。

### 基本请求流程

Symfony 使用前端控制器模式来处理对应用程序的任何请求。

[![request flow](img/0e84ac6ad0e55280dca557efed7b7571.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X95EM_d1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://symfony.com/doc/current/_images/request-flow.png)

1.  客户端(例如浏览器)发送 HTTP 请求
2.  每个请求执行同一个文件(public/index.php)。这个文件充当应用程序的前端控制器。
3.  前端控制器引导 Symfony 内核并传递请求信息
4.  在内部，Symfony 使用路由和控制器来创建页面响应。
5.  Symfony 将您的响应对象转换成文本头和内容(即 HTTP 响应)，然后发送回客户端。

### 路由

Symfony 允许你定义多种格式的路线，如注释、YAML、XML 和 PHP。更多详情请参见官方文档中的[路由](https://symfony.com/doc/current/routing.html)章节。

这里有一个使用 YAML 定义的路线的例子:

```
# config/routes.yaml
blog_list:
    path:     /blog
    controller: App\Controller\BlogController::list

blog_show:
    path:     /blog/{slug}
    controller: App\Controller\BlogController::show 
```

Enter fullscreen mode Exit fullscreen mode

推荐的方法是使用注释，我一开始不太喜欢，但现在我认为它非常有用，因为路由定义接近控制器动作。尽管如此，使用注释还是有一些限制，比如对路径加载顺序的控制较少。

## 配置

Symfony 遵循 [12factor app](https://12factor.net/) 的建议，使用环境变量来定义应用级配置。在开发中，这些可以在一个。env”文件，就像许多其他流行的框架一样。

对于不随环境、包配置、服务和路由配置而改变的静态配置，它使用位于 config/目录中的 YAML 文件。

### 服务容器

依赖注入是 Symfony 的一等公民。Symfony 实现了遵循 [PSR-11](https://www.php-fig.org/psr/psr-11/meta/) 接口的阿迪容器。

Symfony 容器的一些特征:

*   容器被编译和缓存，所以没有性能损失。
*   自动连接允许创建服务，而无需编写任何配置，只需使用方法的类型提示。
*   懒惰的服务
*   服务标签

### 持久层

如前所述，Symfony 不强迫你使用任何持久层或 ORM。尽管如此， [Doctrine](https://www.doctrine-project.org/) 是推荐的一个，并且与 symfony 有很深的整合，但是如果你喜欢，你甚至可以使用 Laravel 的口才。

### 模板引擎。

与持久层一样，Symfony 不会强迫你使用任何模板引擎，而是与 [Twig](https://twig.symfony.com/) 深度集成。Twig 语法非常好，它的灵感来自 Python 中的 Jinja2。

* * *

## 构建示例应用程序

之前我们创建了一个新的 Symfony 项目，现在让我们添加一些代码。在本文中，我们将创建一个简单的 API 端点，从 sqllite 数据库中返回图书列表。这将允许演示一些基本概念，如创建控制器和使用数据库，以及一些有用的控制台命令。

首先让我们安装 [Maker bundle](https://symfony.com/doc/current/bundles/SymfonyMakerBundle/index.html) ，它提供了一些非常代码生成的命令。

```
composer require symfony/maker-bundle --dev 
```

Enter fullscreen mode Exit fullscreen mode

请注意“- dev”标志，因为我们只想在开发环境中访问这个包。

我们将使用教条作为我们的持久层，所以让我们安装所需的包:

```
composer require doctrine/doctrine-bundle doctrine/doctrine-migrations-bundle 
```

Enter fullscreen mode Exit fullscreen mode

现在打开你的。env 文件，并查找

" ### >条款/条款包### "部分。

多亏了 Symfony Flex ，这一部分是在我们安装教义包时自动添加的。

Symfony Flex 是一个 composer 插件，允许基于[配方](https://flex.symfony.com/)进行自动捆绑配置。你可以在这里了解更多关于[的信息。](http://fabien.potencier.org/symfony4-demo.html)

除了将这行代码添加到。env 文件，学说配方还将必要的配置文件添加到 config 文件夹中，并在 config/bundles.php 文件中注册包。很漂亮，你不觉得吗？

由于我们将使用 sqllite，我们需要安装 php-sqllite 扩展。

在 ubuntu 18.04 上可以使用 apt:

`sudo apt -y install php-sqlite3`
安装

然后我们需要更改 DATABASE_URL 变量来使用我们的数据库:

```
DATABASE_URL=sqlite:///%kernel.project_dir%/var/data.db 
```

Enter fullscreen mode Exit fullscreen mode

### 创建你的数据库和实体

现在让我们创建应用程序数据库。为此，您可以运行 DoctrineBundle 提供的以下命令:

```
php bin/console doctrine:database:create 
```

Enter fullscreen mode Exit fullscreen mode

好的。现在让我们创建我们的“图书”实体。多亏了 maker bundle，我们可以交互地做这件事。

只需运行

`php bin/console make:entity`

并回答如下问题:

*   书；
*   姓名；字符串；50;没有；
*   isbn:字符串；50;没有；
*   作者；255;没有；
*   年份；整数；是
*   类别；字符串；30;不

键入 enter 以完成实体的创建。

你的“src/Entity/Book.php”应该是这样的:

```
<?php

namespace App\Entity;

use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Entity(repositoryClass="App\Repository\BookRepository")
 */
class Book
{
    /**
     * @ORM\Id()
     * @ORM\GeneratedValue()
     * @ORM\Column(type="integer")
     */
    private $id;

    /**
     * @ORM\Column(type="string", length=50)
     */
    private $name;

    /**
     * @ORM\Column(type="string", length=50)
     */
    private $isbn;

    /**
     * @ORM\Column(type="string", length=255)
     */
    private $author;

    /**
     * @ORM\Column(type="integer", nullable=true)
     */
    private $year;

    /**
     * @ORM\Column(type="string", length=20)
     */
    private $category;

    public function getId(): ?int
    {
        return $this->id;
    }

    public function getName(): ?string
    {
        return $this->name;
    }

    public function setName(string $name): self
    {
        $this->name = $name;

        return $this;
    }

    public function getIsbn(): ?string
    {
        return $this->isbn;
    }

    public function setIsbn(string $isbn): self
    {
        $this->isbn = $isbn;

        return $this;
    }

    public function getAuthor(): ?string
    {
        return $this->author;
    }

    public function setAuthor(string $author): self
    {
        $this->author = $author;

        return $this;
    }

    public function getYear(): ?int
    {
        return $this->year;
    }

    public function setYear(?int $year): self
    {
        $this->year = $year;

        return $this;
    }

    public function getCategory(): ?string
    {
        return $this->category;
    }

    public function setCategory(string $category): self
    {
        $this->category = $category;

        return $this;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

该命令提供了一种快速生成实体基本框架的方法。你可以选择使用它或者手动操作。

下一步是在数据库中创建相应的表。有许多方法可以实现这一点，但我推荐使用迁移。

我们将使用命令

`php bin/console doctrine:migrations:diff`

通过比较我们的实体和数据库模式来生成一个新的迁移类。默认情况下，迁移将放在“src/Migrations”文件夹中。

现在，要应用迁移，执行:

```
php bin/console doctrine:migrations:migrate 
```

Enter fullscreen mode Exit fullscreen mode

现在，您的数据库中应该有一个 book 表。

现在让我们创建一些虚拟数据。为此，我们还将使用迁移。另一种选择是使用 [DoctrineFixturesBundle](https://symfony.com/doc/master/bundles/DoctrineFixturesBundle/index.html) 。

运行:

```
php bin/console doctrine:migrations:generate 
```

Enter fullscreen mode Exit fullscreen mode

创建空迁移类。
打开生成的文件，用以下内容替换“up”功能:

```
public function up(Schema $schema) : void
    {
        // this up() migration is auto-generated, please modify it to your needs
        $this->addSql("INSERT INTO book ('name', 'isbn', 'author', 'year', 'category') 
          VALUES 
            ('Programming PHP', '0596006810', 'Rasmus Lerdorf,  Kevin Tatroe, Peter MacIntyre', 2006, 'Programming'),
            ('PHP Cookbook', '0596101015', 'Adam Trachtenberg,  David Sklar', 2006, 'Programming'),
            ('PHP Design Patterns', '0596101015', 'Aaron Saray', 2009, 'Programming')            
            ");

    } 
```

Enter fullscreen mode Exit fullscreen mode

现在运行，

`php bin/console doctrine:migrations:migrate`

运行迁移。
您的 books 表现在应该已经填充了我们创建的数据。

### TheBookListService

现在我们已经创建了我们的实体，让我们创建负责从数据库获取书籍的服务。

在“src/Service/booklistservice . PHP”中新建一个文件，内容:

```
 <?php

namespace App\Service;

use App\Entity\Book;
use App\Repository\BookRepository;

class BookListService
{
    /**
     * @var BookRepository
     */
    protected $booksRepository;

    public function __construct(BookRepository $repo)
    {
        $this->booksRepository = $repo;
    }

    /**
     * @return Book[]
     */
    public function get(): array
    {
        return $this->booksRepository->findAll();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，您可以看到自动布线的运行。Symfony 会自动注入 BookRepository 类，不需要任何额外的配置。

当我们使用“make:entity”命令创建实体时，会自动生成 BookRepository 类。如果您手工创建您的实体，您应该在“src/Repository/book Repository . PHP”中手工创建这个类。

```
<?php

namespace App\Repository;

use App\Entity\Book;
use Doctrine\Bundle\DoctrineBundle\Repository\ServiceEntityRepository;
use Symfony\Bridge\Doctrine\RegistryInterface;

/**
 * @method Book|null find($id, $lockMode = null, $lockVersion = null)
 * @method Book|null findOneBy(array $criteria, array $orderBy = null)
 * @method Book[]    findAll()
 * @method Book[]    findBy(array $criteria, array $orderBy = null, $limit = null, $offset = null)
 */
class BookRepository extends ServiceEntityRepository
{
    public function __construct(RegistryInterface $registry)
    {
        parent::__construct($registry, Book::class);
    } 
```

Enter fullscreen mode Exit fullscreen mode

然后，您需要通过向实体添加注释来关联存储库和实体类:

```
 /**
 * @ORM\Entity(repositoryClass="App\Repository\BookRepository")
 */
class Book 
```

Enter fullscreen mode Exit fullscreen mode

### 控制器

现在让我们创建我们的 BookController 类。Maker bundle 还提供了一种生成新控制器类的简单方法。

```
php bin/console make:controller BookController 
```

Enter fullscreen mode Exit fullscreen mode

将在您的“src/Controller”文件夹中生成一个新文件。请注意，maker bundle 希望您遵循“src”文件夹中的特定结构，如控制器、实体、存储库等。这对于较小的项目来说是很好的，并且在所有默认的 Symfony 配置下都能开箱即用。对于更大的项目，我建议根据业务特征对你的课程进行分组。这将需要一些额外的配置来确保 Symfony 在正确的地方找到类。但是这超出了本文范围。

现在修改生成的控制器动作，如下所示:

```
 /**
     * @Route("/books", name="books")
     * @param BookListService $listService
     * @return JsonResponse
     */
    public function index(BookListService $listService, SerializerInterface $serializer)
    {
        $books = $listService->get();

        return new JsonResponse(
            $serializer->serialize($books, 'json'),
            200,
            [],
            true
        );

    } 
```

Enter fullscreen mode Exit fullscreen mode

这里，路由是通过注释定义的，但是如果您愿意，也可以使用 YAML 或 PHP。“index”方法参数是自动绑定的。您也可以将它们注入控制器构造函数中。如果您有仅由控制器上的特定动作使用的服务，这将为您提供很大的灵活性。

我们使用 Symfony serializer 组件将 Book 对象序列化为 json。

如果打开[http://127 . 0 . 0 . 1:8080/Books](http://127.0.0.1:8080/books)，应该会看到一个 json 响应，里面有我们创建的图书列表。

这就是这个演示应用程序的全部内容。如果你继续下去，你会发现这非常简单。我可以在一些事情上输入更多的细节，但是这篇文章太大了。

希望这足以让你看到 Symfony 的基础和潜力。

* * *

## 为什么 Symfony 超过 Laravel

我不想在这里引发一场关于框架的战争。我们已经有足够的这些了。我承认 Laravel 为振兴 PHP 社区所做的贡献，我认为总体来说这是一个不错的框架。最大的不同是一些核心设计决策，这让我更喜欢 Symfony，而 IMO 则让它更适合不仅仅是原型或小型应用程序的项目。

#### Symfony 更小更灵活

默认情况下，Symfony 只提供引导框架和处理 http 请求/响应所需的核心组件，然后您可以根据需要添加更多组件。这非常适合微服务或较小的应用。

另一方面，默认情况下，Laravel 附带了所有的附加功能，这使得它变得更加笨重，并且有许多您可能不需要的依赖项。

是的，你可以将 [Lumen](https://lumen.laravel.com/) 用于你的 Laravel 微服务，但仍然是一个不同的框架。Symfony 曾经有 Silex，但他们决定放弃它，改为 Symfony 微服务友好。

Symfony 也有[网站框架](https://github.com/symfony/website-skeleton)如果你想用模板、表格等建立一个传统网站，这很棒。

#### Laravel 滥用单件、立面和静态方法

众所周知，Laravel 有许多助手类，大部分可以使用 Facades 或静态方法访问。

虽然在开始时使用这些“捷径”可能看起来开发起来更快，但随着应用程序的增长，它将使应用程序更难维护和测试。

另一方面，对任何事情都使用依赖注入，这使得编写干净、模块化和可测试的代码变得容易得多。

#### 活动记录与数据映射器

可能我对 Laravel 最大的抱怨是他们的 ORM，口才。不是 ORM 本身，而是底层设计模式“活动记录”。

在“活动记录”中，实体类通常是数据库表的直接映射。这使得您的业务领域与您的数据库结构过于耦合。

另一个问题是可测试性。因为在活动记录中，你的实体通常会扩展一些基类，这些基类会创建一个到数据库的连接，与没有活动数据库连接的实体相比，实现正确的单元测试真的很难。没有简单的方法可以模仿“save”方法，例如，当您在创建的对象的新实例中调用它时。

活动记录也会有更差的性能，因为它更难控制和优化 ORM 完成的查询。

我更喜欢更好的关注点分离和更“DDD”的方法，其中实体只是 POPOs，具有关于相关持久性的最少细节，然后拥有持久层并使用存储库模式来集中我的所有查询。

教条允许所有这一切，因为它实现了数据映射模式。存储库是将持久性代码从您的域实体中分离出来的一种很好的方式。

你可以在这里读到更多关于这些模式的信息:

*   ORM 模式:活动记录和数据映射器对于对象-关系映射的权衡-深思熟虑的代码

## 结论

这篇文章很长！。还有很多内容我可以更详细地讨论，但可能会在其他文章或系列中讨论。

我希望您至少了解 Symfony 是如何工作的，以及它为什么是构建现代 PHP 应用程序的最佳选择。

如果您有任何问题或意见，请随时使用下面的评论框。

同时，如果您想深入了解 Symfony，请随意查看以下资源:

*   Symfony 文档-Symfony 官方文档。
*   [SymfonyCasts](https://symfonycasts.com/tracks/symfony) -最好的 PHP & Symfony 教程
*   [Tobias Nyholm“深入了解 Symfony 4 内部”](https://www.youtube.com/watch?v=pz6VdH4_BSg)[Symfony 博客](https://symfony.com/blog/)——Symfony 官方博客

谢谢你。