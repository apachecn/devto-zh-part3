# 依赖注入:PHP 版本

> 原文：<https://dev.to/aleksikauppila/dependency-injection-php-edition-20gn>

“依赖注入”在日常编程术语中有两种不同的含义。很多时候它指的是“容器技术”。其他时候它指的是设计模式，这当然是混淆的来源。区分“设计模式”和“容器”有助于我们更好地理解这个概念。

## 图样

如果我不得不选择一个非常有限的必须知道的设计模式列表，[依赖注入](https://designpatternsphp.readthedocs.io/en/latest/Structural/DependencyInjection/README.html)肯定会在那个列表上。该模式本身非常简单，使您能够编写更多可测试和可配置的应用程序。

维基百科列出了三种类型的依赖注入:构造函数注入、设置函数注入和接口注入。我们将关注构造函数注入，因为其他类型会产生劣质的设计。

真的就这么简单:

```
// Without dependency injection
class MyService
{
    private $myLogger;

    public function __construct()
    {
        $this->myLogger = new MyLogger();
    }
    //...
}

// With dependency injection
class MyService
{
    private $myLogger;

    public function __construct(MyLogger $myLogger)
    {
        $this->myLogger = $myLogger;
    }
    //...
} 
```

不是在`MyService`中创建`MyLogger`的实例，而是在客户端代码中创建并通过构造函数传递。这就是依赖注入设计模式的全部内容。

这允许我们对这个类进行单元测试。我们可以模仿、嘲笑或伪造`MyLogger`的行为。如果注入的对象使用文件系统、数据库或连接到外部服务，这一点尤其重要。

通过这个小小的修改，我们已经实现了`MyService`更好的可测试性。为了在运行时实现不同的行为和更好的可维护性，我们必须依赖一个接口，而不是一个具体的类。

## 容器

容器只是一个提供服务实例访问的对象。我实际上非常喜欢“服务容器”这个术语，因为这些容器在构建服务时通常工作得最好。我不建议在容器中定义 DTO、道、窗体、值对象或其他对象。

基本想法很简单:

*   您编写如何创建服务的说明。这些被称为服务定义。
*   你把这些指令给一个`Container`或`ContainerBuilder`。
*   您要求`Container`提供某个类的实例:`$container->get(MyService::class);`。

也有提供[自动布线](http://php-di.org/doc/php-definitions.html#autowired-objects)的技术，这将产生一些魔力，并允许你有一个更轻的配置。我并不反对自动连接，但是我更喜欢显式的服务定义。

我使用过两家供应商的依赖注入容器:[symfony/dependency-injection](https://symfony.com/doc/current/components/dependency_injection.html)和 [php-di/php-di](http://php-di.org/) 。它们可以被引入到你的项目中，即使你可能使用其他框架，甚至根本没有框架。我不会在这里详细介绍如何设置这些技术，因为它们有很好的文档。

当谈到使用容器时，最后有一个非常重要的问题:你不应该在你的应用程序逻辑层或任何更高层访问容器！应该将访问权限限制在应用程序的较低级别上，运行该应用程序的组件是在该应用程序的较低级别上构建的。在 Symfony 的旧版本中，曾经有一个从控制器内部的容器中调用服务的惯例，但幸运的是这已经成为过去。

PHP-DI 的[“最佳实践”指南](http://php-di.org/doc/best-practices.html)为我们提供了这些重要的规则。我强烈建议您阅读整个文档，因为它包含了一些有用的建议，可以让您的容器生活更加轻松。

> 1.  Never get an entry directly from the container (always use dependency injection)
> 2.  More generally, write code that is decoupled from containers.
> 3.  Type-hint confronts the interface, which implementation is configured in the configuration of the container.

对于最后一点，我们可以继续使用依赖注入模式的简单例子。

为了真正使代码可维护，我们必须遵守[依赖倒置原则](https://en.wikipedia.org/wiki/Dependency_inversion_principle)，它说:

> 高层模块不应该依赖低层模块。两者都应该依赖于抽象。
> 
> 抽象不应该依赖于细节。细节应该依赖于抽象。

首先，我们确保`MyLogger`实现了`Psr\Log\LoggerInterface`。然后我们使用`LoggerInterface`作为记录器的类型提示。如果`MyLogger`是第三方依赖，这里可以参考[介绍的技术。](https://dev.to/aleksikauppila/protect-your-system-from-changes-in-3rd-party-dependencies-8m3) 

```
class MyService
{
    private $myLogger;

    public function __construct(LoggerInterface $logger)
    {
        $this->myLogger = $logger;
    }
    //...
} 
```

在这一改变之后，我们的代码更加可测试、可配置，并且对改变更加健壮。