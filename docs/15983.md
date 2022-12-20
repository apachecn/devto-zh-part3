# 🐘PHP 中的单元测试

> 原文：<https://dev.to/biros/-unit-tests-in-php-3il7>

嗨伙计们！我将谈谈我用 PHP 设计单元测试的方法。

## 免责声明

先来个(我的？)单元测试的定义:

> 单元测试的目标是检查每个公共函数的正确行为，给定一组有代表性的数据。重要的是，这些函数内部的外部调用必须在专门的测试中进行测试，因此需要被模仿。
> 进行这些测试的目的是在每次修改代码库时运行它们，以验证没有引入回归。

作为先决条件，代码需要*测试就绪*，在这个意义上，我们需要允许模仿依赖。在给定的函数中，依赖可以是内部的(例如项目的另一个类)，也可以是外部的(例如对第三方库的调用)。

我不会谈论使代码可测试的方法。我知道有两种技术可以使用依赖注入原理来实现这一点，但是也许还有其他的选择:

*   首先，您可以修改函数的接口，将它的所有依赖项作为参数传递(或者使用构造函数或设置函数)，
*   或者你可以使用一个**依赖注入容器**(又名 DIC)，这是一个常见的(反？)模式(详见 [PHP 的 PSR11](https://www.php-fig.org/psr/psr-11/) )。

## 代码架构

当我开始一个新的 web 项目时，我通常将代码分成几个不同的层:

*   一个*路由器*(通常是 Slim 路由器)，负责处理 HTTP 请求，
*   一个*控制器*层，负责数据验证和输出渲染，
*   一个*业务*层，负责业务逻辑、
*   一个*映射器*层(又名 DTO)，
*   一个*模型*层(又名道)。

这样做使代码更易测试。

根据需要，可能需要一些额外的东西，例如:

*   *检查器*，用于验证输入，
*   *视图*，呈现输出、
*   *中间件*，增加 HTTP 请求的前后处理，
*   *routes* ，管理其余 API 的路由，
*   *使能器*，用于管理对第三方 API(短信、电子邮件、...).

最后，一个引导文件来创建所有的东西。

经典的工作流程是:

*   一个*控制器*在给定的函数上被调用，
*   它使用专用的*检验器*来验证输入，
*   它用验证过的输入调用*业务*层，
    *   *业务*层可选地使用*映射器*与数据库交互
    *   它还可以通过*使能器*与第三方 API 交互
    *   它可选地将*模型*返回到*控制器*层
*   *控制器*使用专用的*视图*渲染*业务*层返回的*模型*。

当我为一个给定的层编写单元测试时，我只测试该层函数的行为，并且模拟对子层函数的调用。例如，当我为一个*控制器*编写测试时，我模拟对*业务*层、*检查器*和*视图*的调用。当我为一个*业务*对象编写测试时，我模拟对*映射器*、对*使能器*和其他*业务*对象的调用。

## 举例

让我们用一点代码来看看*控制器*是什么样子的:

```
class UserController
{
    /** @var UserBusiness */
    public $business;
    /** @var UserChecker */
    public $checker;
    /** @var UserView */
    public $view;

    public function __construct(
        UserBusiness $business, 
        UserChecker $checker, 
        UserView $view
    ) {
        $this->business = $business;
        $this->checker = $checker;
        $this->view = $view;
    }

    public function getById(string $id): string
    {
        $this->checker->checkId($id);
        $user = $this->business->getById($id);

        return $this->view->render($user);
    }
} 
```

这里您可以看到`getById`函数中的 3 个依赖项。我选择通过我的`UserController`类的构造函数来传递这些依赖关系。或者，我可以使用传递给函数(或构造函数)的容器，或者通过函数的参数传递 dep。结果应该是一样的:我必须模拟这 3 个 dep 来测试功能。

## 使用仿制品

谢天谢地， [PHPUnit](https://phpunit.de/) 附带了一个很棒的 API 来处理模拟(参见[测试替身](https://phpunit.readthedocs.io/en/7.4/test-doubles.html))。我不会在这里涵盖所有的特性，但是文档值得一看。

首先，我需要模拟`UserChecker`的`checkId`函数。正如您可能猜到的，如果`id`格式错误，它会引发一个异常。
然后，我模仿`UserBusiness`的`getById`功能。
最后是`UserView`的`render`功能。

```
 class UserControllerTest extends PHPUnit\Framework\TestCase
{
    public function testGetById_Ok()
    {
        $business = $this->createMock(UserBusiness::class);
        $checker = $this->createMock(UserChecker::class);
        $view = $this->createMock(UserView::class);

        $expectedId = 'id';
        $expectedUser = new UserModel($expectedId, 'john', 'doe');
        $expectedResult = 'result';

        $checker->expects($this->once())
            ->method('checkId')
            ->with($expectedId);

        $business->expects($this->once())
            ->method('getById')
            ->with($expectedId)
            ->willReturn($expectedUser);

        $view->expects($this->once())
            ->method('render')
            ->with($expectedUser)
            ->willReturn($expectedResult);

        $controller = new UserController($business, $checker, $view);
        $actualResult = $controller->getById($expectedId);

        $this->assertEquals($expectedResult, $actualResult);
    }
} 
```

每一层都要做同样的事情。

## 结论

编写详尽的单元测试可能是痛苦的，因为大多数时候，你花在编写测试上的时间比编写“真正的”代码还要多。但是在我看来，在测试你的应用时，没有可接受的权衡。我知道存在其他测试技术(比如 TDT)，但是我将它们视为补充测试，因为它们更接近于集成测试而不是单元测试。但也许我错了？

* * *

请不吝赐教！

感谢阅读！