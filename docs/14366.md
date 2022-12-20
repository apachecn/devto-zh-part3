# 在 PHP 中创建自己的依赖注入容器

> 原文：<https://dev.to/matthewbdaly/creating-your-own-dependency-injection-container-in-php-342k>

在早期阶段，依赖注入可能是一个难以理解的概念。即使你一直在使用它，它也常常像魔术一样。然而，一旦你真正了解了它的具体细节，它就没那么复杂了，构建你自己的容器是了解它如何工作以及如何使用它的一个好方法。

在本教程中，我将带您创建一个简单的最小依赖注入容器，使用 PHPSpec 作为 TDD 工作流的一部分。虽然最终结果不一定是我乐于在生产环境中使用的东西，但它足以理解基本概念，并使它不像一个黑盒。我们的容器将被称为厄尼(如果你想知道为什么，这是一个参考 90 年代的视频游戏，其中有一个基于埃利克·坎通纳的角色称为厄尼容器)。

我们需要做的第一件事是建立我们的依赖关系。我们的容器将实现 PSR-11，所以我们需要包含定义它的接口。我们还将使用 PHP 代码嗅探器来确保代码质量，并使用 PHPSpec 进行测试。您的`composer.json`应该是这样的:

```
{
    "name": "matthewbdaly/ernie",
    "description": "Simple DI container",
    "type": "library",
    "require-dev": {
        "squizlabs/php_codesniffer": "^3.3",
        "phpspec/phpspec": "^5.0",
        "psr/container": "^1.0"
    },
    "license": "MIT",
    "authors": [
        {
            "name": "Matthew Daly",
            "email": "450801+matthewbdaly@users.noreply.github.com"
        }
    ],
    "require": {},
    "autoload": {
        "psr-4": {
            "Matthewbdaly\\Ernie\\": "src/"
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要将它放入我们的`phpspec.yml`文件:

```
suites:
    test_suite:
        namespace: Matthewbdaly\Ernie
        psr4_prefix: Matthewbdaly\Ernie 
```

Enter fullscreen mode Exit fullscreen mode

完成之后，我们就可以开始我们的实现了。

# 创建异常

PSR-11 规范为异常定义了两个接口，我们将在实际转移到容器本身之前实现这两个接口。其中第一个是`Psr\Container\ContainerExceptionInterface`。运行以下命令为异常创建一个基本规范:

```
$ vendor/bin/phpspec desc Matthewbdaly/Ernie/Exceptions/ContainerException 
```

Enter fullscreen mode Exit fullscreen mode

在`spec/Exceptions/ContainerExceptionSpec.php`为它生成的规范将看起来像这样:

```
<?php

namespace spec\Matthewbdaly\Ernie;

use Matthewbdaly\Ernie\ContainerException;
use PhpSpec\ObjectBehavior;
use Prophecy\Argument;

class ContainerExceptionSpec extends ObjectBehavior
{
    function it_is_initializable()
    {
        $this->shouldHaveType(ContainerException::class);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这不足以满足我们的需要。我们的异常还必须实现两个接口:

*   `Throwable`
*   `Psr\Container\ContainerExceptionInterface`

前者可以通过从`Exception`继承来解决，而后者不需要任何额外的方法。让我们扩展我们的规范来检查这些:

```
<?php

namespace spec\Matthewbdaly\Ernie\Exceptions;

use Matthewbdaly\Ernie\Exceptions\ContainerException;
use PhpSpec\ObjectBehavior;
use Prophecy\Argument;

class ContainerExceptionSpec extends ObjectBehavior
{
    function it_is_initializable()
    {
        $this->shouldHaveType(ContainerException::class);
    }

    function it_implements_interface()
    {
        $this->shouldImplement('Psr\Container\ContainerExceptionInterface');
    }

    function it_implements_throwable()
    {
        $this->shouldImplement('Throwable');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在运行规范，PHPSpec 将为您生成样板异常:

```
$ vendor/bin/phpspec run
Matthewbdaly/Ernie/Exceptions/ContainerException                                
  11 - it is initializable
      class Matthewbdaly\Ernie\Exceptions\ContainerException does not exist.

Matthewbdaly/Ernie/Exceptions/ContainerException                                  
  16 - it implements interface
      class Matthewbdaly\Ernie\Exceptions\ContainerException does not exist.

Matthewbdaly/Ernie/Exceptions/ContainerException                                
  21 - it implements throwable
      class Matthewbdaly\Ernie\Exceptions\ContainerException does not exist.

                                      100% 3
1 specs
3 examples (3 broken)
23ms

  Do you want me to create `Matthewbdaly\Ernie\Exceptions\ContainerException`   
  for you?                                                                      
                                                                         [Y/n]
y
Class Matthewbdaly\Ernie\Exceptions\ContainerException created in /home/matthew/Projects/ernie-clone/src/Exceptions/ContainerException.php.

Matthewbdaly/Ernie/Exceptions/ContainerException                                
  16 - it implements interface
      expected an instance of Psr\Container\ContainerExceptionInterface, but got
      [obj:Matthewbdaly\Ernie\Exceptions\ContainerException].

Matthewbdaly/Ernie/Exceptions/ContainerException                                
  21 - it implements throwable
      expected an instance of Throwable, but got
      [obj:Matthewbdaly\Ernie\Exceptions\ContainerException].

            33% 66% 3
1 specs
3 examples (1 passed, 2 failed)
36ms 
```

Enter fullscreen mode Exit fullscreen mode

它失败了，但我们预料到了。我们需要更新我们的异常来扩展基本 PHP 异常，并实现`Psr\Container\ContainerExceptionInterface`。让我们现在就做:

```
<?php

namespace Matthewbdaly\Ernie\Exceptions;

use Psr\Container\ContainerExceptionInterface;
use Exception;

class ContainerException extends Exception implements ContainerExceptionInterface
{
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们重新运行规范:

```
$ vendor/bin/phpspec run
                                      100% 3
1 specs
3 examples (3 passed)
24ms 
```

Enter fullscreen mode Exit fullscreen mode

我们需要实现的第二个异常是`Psr\Container\NotFoundExceptionInterface`，这是一个类似的故事。运行以下命令来创建规范:

```
$ vendor/bin/phpspec desc Matthewbdaly/Ernie/Exceptions/NotFoundException 
```

Enter fullscreen mode Exit fullscreen mode

同样，需要修改规范来验证它是可抛出的，并且实现了所需的接口:

```
<?php

namespace spec\Matthewbdaly\Ernie\Exceptions;

use Matthewbdaly\Ernie\Exceptions\NotFoundException;
use PhpSpec\ObjectBehavior;
use Prophecy\Argument;

class NotFoundExceptionSpec extends ObjectBehavior
{
    function it_is_initializable()
    {
        $this->shouldHaveType(NotFoundException::class);
    }

    function it_implements_interface()
    {
        $this->shouldImplement('Psr\Container\NotFoundExceptionInterface');
    }

    function it_implements_throwable()
    {
        $this->shouldImplement('Throwable');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了简洁起见，我省略了输出，但是如果您运行`vendor/bin/phpspec run`，您会看到它失败，因为生成的类没有实现所需的接口。将`src/Exceptions/NotFoundException`修改如下:

```
<?php

namespace Matthewbdaly\Ernie\Exceptions;

use Psr\Container\NotFoundExceptionInterface;
use Exception;

class NotFoundException extends Exception implements NotFoundExceptionInterface
{
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`vendor/bin/phpspec run`现在应该可以看到它通过了。现在让我们转到容器类…

# 建筑容器

运行以下命令创建容器规范:

```
$ vendor/bin/phpspec desc Matthewbdaly/Ernie/Container 
```

Enter fullscreen mode Exit fullscreen mode

然而，默认生成的规范是不够的。我们需要检查它实现了所需的接口:

```
<?php

namespace spec\Matthewbdaly\Ernie;

use Matthewbdaly\Ernie\Container;
use PhpSpec\ObjectBehavior;
use Prophecy\Argument;

class ContainerSpec extends ObjectBehavior
{
    function it_is_initializable()
    {
        $this->shouldHaveType(Container::class);
    }

    function it_implements_interface()
    {
        $this->shouldImplement('Psr\Container\ContainerInterface');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们运行 PHPSpec，我们将生成我们的类:

```
$ vendor/bin/phpspec run
Matthewbdaly/Ernie/Container                                                    
  11 - it is initializable
      class Matthewbdaly\Ernie\Container does not exist.

Matthewbdaly/Ernie/Container                                                      
  16 - it implements interface
      class Matthewbdaly\Ernie\Container does not exist.

                            75% 25% 8
3 specs
8 examples (6 passed, 2 broken)
404ms

  Do you want me to create `Matthewbdaly\Ernie\Container` for you?              
                                                                         [Y/n] 
y
Class Matthewbdaly\Ernie\Container created in /home/matthew/Projects/ernie-clone/src/Container.php.

Matthewbdaly/Ernie/Container                                                      
  16 - it implements interface
      expected an instance of Psr\Container\ContainerInterface, but got
      [obj:Matthewbdaly\Ernie\Container].

                                 87% 12% 8
3 specs
8 examples (7 passed, 1 failed)
40ms 
```

Enter fullscreen mode Exit fullscreen mode

现在，正如我们所看到的，这个类没有实现接口。让我们补救一下:

```
<?php

namespace Matthewbdaly\Ernie;

use Psr\Container\ContainerInterface;

class Container implements ContainerInterface
{
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们运行测试，它们应该会失败，因为该类需要添加所需的方法:

```
$ vendor/bin/phpspec run
✘ Fatal error happened while executing the following 
    it is initializable 
    Class Matthewbdaly\Ernie\Container contains 2 abstract methods and must therefore be declared abstract or implement the remaining methods (Psr\Container\ContainerInterface::get, Psr\Container\ContainerInterface::has) in /home/matthew/Projects/ernie-clone/src/Container.php on line 7 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用允许您自动实现接口的编辑器或 IDE，您可以运行它来添加所需的方法。我使用 PHPActor 和 Neovim，并使用 Transform 菜单中的选项来实现契约:

```
<?php

namespace Matthewbdaly\Ernie;

use Psr\Container\ContainerInterface;

class Container implements ContainerInterface
{
    /**
     * {@inheritDoc}
     */
    public function get($id)
    {
    }

    /**
     * {@inheritDoc}
     */
    public function has($id)
    {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`vendor/bin/phpspec run`现在应该让规范通过，但是这些方法实际上还没有做任何事情。如果你阅读 PSR-11 的规范，你会看到`has()`返回一个布尔值来表明一个类是否可以被实例化，而`get()`要么返回一个指定类的实例，要么抛出一个异常。我们将添加一些规范，检查内置类是否可以被两者返回，未知类是否显示预期的行为。我们将同时做这两项，因为在这两种情况下，实际解析所需类的功能将被推迟到一个解析器方法，结果这些方法不会做那么多:

```
 function it_has_simple_classes()
    {
        $this->has('DateTime')->shouldReturn(true);
    }

    function it_does_not_have_unknown_classes()
    {
        $this->has('UnknownClass')->shouldReturn(false);
    }

    function it_can_get_simple_classes()
    {
        $this->get('DateTime')->shouldReturnAnInstanceOf('DateTime');
    }

    function it_returns_not_found_exception_if_class_cannot_be_found()
    {
        $this->shouldThrow('Matthewbdaly\Ernie\Exceptions\NotFoundException')
            ->duringGet('UnknownClass');
    } 
```

Enter fullscreen mode Exit fullscreen mode

这些测试验证了:

*   当被始终存在的`DateTime`类调用时，`has()`返回`true`
*   `has()`为未定义的`UnknownClass`返回`false`
*   `get()`成功实例化`DateTime`的一个实例
*   如果你试图实例化未定义的`UnknownClass`，抛出一个异常

运行规范将会产生错误:

```
$ vendor/bin/phpspec run
Matthewbdaly/Ernie/Container                                                      
  21 - it has simple classes
      expected true, but got null.

Matthewbdaly/Ernie/Container                                                    
  26 - it does not have unknown classes
      expected false, but got null.

Matthewbdaly/Ernie/Container                                                    
  31 - it can get simple classes
      expected an instance of DateTime, but got null.

Matthewbdaly/Ernie/Container                                                    
  36 - it returns not found exception if class cannot be found
      expected to get exception / throwable, none got.

                         66% 33% 12
3 specs
12 examples (8 passed, 4 failed)
98ms 
```

Enter fullscreen mode Exit fullscreen mode

让我们填充这些空方法:

```
<?php

namespace Matthewbdaly\Ernie;

use Psr\Container\ContainerInterface;
use Matthewbdaly\Ernie\Exceptions\NotFoundException;
use ReflectionClass;
use ReflectionException;

class Container implements ContainerInterface
{
    /**
     * {@inheritDoc}
     */
    public function get($id)
    {
        $item = $this->resolve($id);
        return $this->getInstance($item);
    }

    /**
     * {@inheritDoc}
     */
    public function has($id)
    {
        try {
            $item = $this->resolve($id);
        } catch (NotFoundException $e) {
            return false;
        }
        return $item->isInstantiable();
    }

    private function resolve($id)
    {
        try {
            return (new ReflectionClass($id));
        } catch (ReflectionException $e) {
            throw new NotFoundException($e->getMessage(), $e->getCode(), $e);
        }
    }

    private function getInstance(ReflectionClass $item)
    {
        return $item->newInstance();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，`has()`和`get()`方法都需要将一个字符串 ID 解析为一个实际的类，因此公共功能存储在一个名为`resolve()`的私有方法中。这使用了 [PHP 反射 API](http://php.net/manual/en/book.reflection.php) 将类名解析为实际的类。我们将字符串 ID 传递给一个构造函数`ReflectionClass`，`resolve()`方法要么返回创建的`ReflectionClass`实例，要么抛出一个异常。

对于门外汉来说，`ReflectionClass`允许您反映其完全限定类名被传递给构造函数的对象，以便以编程方式与该类进行交互。我们将使用的方法包括:

*   `isInstantiable` -确认类是否可以被实例化(例如，traits 和抽象类不能)
*   `newInstance` -创建所讨论项目的新实例，只要它在构造函数中没有依赖项
*   `newInstanceArgs` -使用传入的参数创建一个新实例
*   `getConstructor` -允许你获得关于构造器的信息

反射 API 相当全面，如果你想了解更多，我推荐阅读上面链接的文档。

对于`has()`方法，我们检查被解析的类是可实例化的，并返回结果。对于`get()`方法，我们使用`getInstance()`实例化项目并返回，如果失败则抛出异常。

# 登记对象

在当前状态下，容器不允许你设置一个项目。为了有用，我们需要能够指定一个接口或字符串应该被解析为一个给定的类，或者在我们需要传入标量参数(如数据库对象)的情况下，指定应该如何实例化该类的一个具体实例。为此，我们将创建一个新的`set()`公共方法，允许设置依赖关系。以下是修改后的规格，包括这个:

```
<?php

namespace spec\Matthewbdaly\Ernie;

use Matthewbdaly\Ernie\Container;
use PhpSpec\ObjectBehavior;
use Prophecy\Argument;
use DateTime;

class ContainerSpec extends ObjectBehavior
{
    function it_is_initializable()
    {
        $this->shouldHaveType(Container::class);
    }

    function it_implements_interface()
    {
        $this->shouldImplement('Psr\Container\ContainerInterface');
    }

    function it_has_simple_classes()
    {
        $this->has('DateTime')->shouldReturn(true);
    }

    function it_does_not_have_unknown_classes()
    {
        $this->has('UnknownClass')->shouldReturn(false);
    }

    function it_can_get_simple_classes()
    {
        $this->get('DateTime')->shouldReturnAnInstanceOf('DateTime');
    }

    function it_returns_not_found_exception_if_class_cannot_be_found()
    {
        $this->shouldThrow('Matthewbdaly\Ernie\Exceptions\NotFoundException')
            ->duringGet('UnknownClass');
    }

    function it_can_register_dependencies()
    {
        $toResolve = new class {
        };
        $this->set('Foo\Bar', $toResolve)->shouldReturn($this);
    }

    function it_can_resolve_registered_dependencies()
    {
        $toResolve = new class {
        };
        $this->set('Foo\Bar', $toResolve);
        $this->get('Foo\Bar')->shouldReturnAnInstanceOf($toResolve);
    }

    function it_can_resolve_registered_invokable()
    {
        $toResolve = new class {
            public function __invoke() {
                return new DateTime;
            }
        };
        $this->set('Foo\Bar', $toResolve);
        $this->get('Foo\Bar')->shouldReturnAnInstanceOf('DateTime');
    }

    function it_can_resolve_registered_callable()
    {
        $toResolve = function () {
            return new DateTime;
        };
        $this->set('Foo\Bar', $toResolve);
        $this->get('Foo\Bar')->shouldReturnAnInstanceOf('DateTime');
    }

    function it_can_resolve_if_registered_dependencies_instantiable()
    {
        $toResolve = new class {
        };
        $this->set('Foo\Bar', $toResolve);
        $this->has('Foo\Bar')->shouldReturn(true);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这需要处理相当多的场景，所以我们有几个测试。这些验证了:

*   `set()`方法返回容器类的一个实例，以允许方法链接
*   当设置了依赖项时，调用`get()`会返回该类的一个实例
*   当一个设置了`__invoke()`魔法方法的具体类被传递给`set()`时，它将被调用并返回响应。
*   当传递的值是回调时，解析回调并返回响应
*   当设置了依赖关系时，调用`has()`返回正确的值

请注意，我们使用匿名类进行测试——我以前写过这些，它们在这种情况下非常有用，因为它们允许我们创建一个简单的内联类来进行测试。

运行规范应该导致我们被提示生成`set()`方法，然后失败:

```
$ vendor/bin/phpspec run
Matthewbdaly/Ernie/Container                                                    
  42 - it can register dependencies
      method Matthewbdaly\Ernie\Container::set not found.

Matthewbdaly/Ernie/Container                                                    
  49 - it can resolve registered dependencies
      method Matthewbdaly\Ernie\Container::set not found.

Matthewbdaly/Ernie/Container                                                    
  57 - it can resolve registered invokable
      method Matthewbdaly\Ernie\Container::set not found.

Matthewbdaly/Ernie/Container                                                    
  68 - it can resolve registered callable
      method Matthewbdaly\Ernie\Container::set not found.

Matthewbdaly/Ernie/Container                                                    
  77 - it can resolve if registered dependencies instantiable
      method Matthewbdaly\Ernie\Container::set not found.

                          70% 29% 17
3 specs
17 examples (12 passed, 5 broken)
316ms

  Do you want me to create `Matthewbdaly\Ernie\Container::set()` for you?       
                                                                         [Y/n]
y
  Method Matthewbdaly\Ernie\Container::set() has been created.

Matthewbdaly/Ernie/Container                                                    
  42 - it can register dependencies
      expected [obj:Matthewbdaly\Ernie\Container], but got null.

Matthewbdaly/Ernie/Container                                                    
  49 - it can resolve registered dependencies
      exception [exc:Matthewbdaly\Ernie\Exceptions\NotFoundException("Class Foo\Bar does not exist")] has been thrown.

Matthewbdaly/Ernie/Container                                                    
  57 - it can resolve registered invokable
      exception [exc:Matthewbdaly\Ernie\Exceptions\NotFoundException("Class Foo\Bar does not exist")] has been thrown.

Matthewbdaly/Ernie/Container                                                    
  68 - it can resolve registered callable
      exception [exc:Matthewbdaly\Ernie\Exceptions\NotFoundException("Class Foo\Bar does not exist")] has been thrown.

Matthewbdaly/Ernie/Container                                                    
  77 - it can resolve if registered dependencies instantiable
      expected true, but got false.

                          70% 11% 17% 17
3 specs
17 examples (12 passed, 2 failed, 3 broken)
90ms 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们需要正确设置`set()`方法，并定义一个属性来包含存储的服务:

```
 private $services = [];

    public function set(string $key, $value)
    {
        $this->services[$key] = $value;
        return $this;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这修复了第一个规范，但是需要修改解析器来处理手工设置 ID 的情况:

```
 private function resolve($id)
    {
        try {
            $name = $id;
            if (isset($this->services[$id])) {
                $name = $this->services[$id];
                if (is_callable($name)) {
                    return $name();
                }
            }
            return (new ReflectionClass($name));
        } catch (ReflectionException $e) {
            throw new NotFoundException($e->getMessage(), $e->getCode(), $e);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将允许我们解析用`set()`设置的类。然而，我们也想解决任何可调用的问题，比如回调或者实现了`__invoke()`魔法方法的类，这意味着有时`resolve()`会返回可调用的结果，而不是`ReflectionClass`的实例。在这种情况下，我们应该直接退货:

```
 public function get($id)
    {
        $item = $this->resolve($id);
        if (!($item instanceof ReflectionClass)) {
            return $item;
        }
        return $this->getInstance($item);
    } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，因为在`set()`的第二个参数中指定的任何具体类中都自动调用了`__invoke()`方法，所以只有当定义了`__invoke()`方法的类作为字符串表示传入时，才有可能解析它们。下面的心理战应该会清楚这意味着什么:

```
>>> use Matthewbdaly\Ernie\Container;
>>> $c = new Container;
=> Matthewbdaly\Ernie\Container {#2307}
>>> class TestClass { public function __invoke() { return "Called"; }}
>>> $c->get('TestClass');
=> TestClass {#2319}
>>> $c->set('Foo\Bar', 'TestClass');
=> Matthewbdaly\Ernie\Container {#2307}
>>> $c->get('Foo\Bar');
=> TestClass {#2309}
>>> $c->set('Foo\Bar', new TestClass);
=> Matthewbdaly\Ernie\Container {#2307}
>>> $c->get('Foo\Bar');
=> "Called" 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，如果我们传入一个定义了`__invoke()`方法的类的完全限定类名，它可以像预期的那样被解析。然而，如果我们将它的一个具体实例传递给`set()`，它将被调用并返回响应。这可能不是您希望自己的容器具有的行为。

根据 PHP 联盟的容器实现的这个问题，这也是他们的一个问题，所以这只是一个玩具例子，我不会为此失眠。如果你使用这篇文章作为编写你自己的容器的基础，你需要注意一些事情。

# 解析依赖关系

我们的集装箱里少了一样东西。现在，它应该能够实例化几乎所有没有依赖关系的类，但这只是少数。为了有用，容器应该能够自动解析一个类的所有依赖关系。

让我们为此添加一个规范:

```
 function it_can_resolve_dependencies()
    {
        $toResolve = get_class(new class(new DateTime) {
            public $datetime;
            public function __construct(DateTime $datetime)
            {
                $this->datetime = $datetime;
            }
        });
        $this->set('Foo\Bar', $toResolve);
        $this->get('Foo\Bar')->shouldReturnAnInstanceOf($toResolve);
    } 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们必须有点狡猾。匿名类是同时定义和实例化的，所以我们不能在测试中将其作为匿名类传入。相反，我们调用匿名类并获取其名称，然后将其作为第二个参数设置为`set()`。然后我们可以验证返回的对象是同一个类的实例。

运行这个会抛出一个错误:

```
$ vendor/bin/phpspec run
Matthewbdaly/Ernie/Container                                                    
  86 - it can resolve dependencies
      exception [err:ArgumentCountError("Too few arguments to function class@anonymous::__construct(), 0 passed and exactly 1 expected")] has been thrown.

                                    94% 18
3 specs
18 examples (17 passed, 1 broken)
60ms 
```

Enter fullscreen mode Exit fullscreen mode

这是意料之中的。我们的测试类接受构造函数中的一个实例`DateTime`作为强制依赖，因此实例化它会失败。我们需要更新`getInstance()`方法，以便它可以处理任何依赖关系:

```
 private function getInstance(ReflectionClass $item)
    {
        $constructor = $item->getConstructor();
        if (is_null($constructor) || $constructor->getNumberOfRequiredParameters() == 0) {
            return $item->newInstance();
        }
        $params = [];
        foreach ($constructor->getParameters() as $param) {
            if ($type = $param->getType()) {
                $params[] = $this->get($type->getName());
            }
        }
        return $item->newInstanceArgs($params);
    } 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用反射 API 来获取构造函数。如果没有构造函数，或者没有必需的参数，我们就像以前一样返回反射类的一个新实例。

否则，我们将遍历所需的参数。对于每个参数，我们获取为该参数指定的类型的字符串表示，并从容器中检索它的一个实例。之后，我们使用这些参数来实例化对象。

让我们再次运行规格:

```
$ vendor/bin/phpspec run
                                      100% 18
3 specs
18 examples (18 passed)
51ms 
```

Enter fullscreen mode Exit fullscreen mode

我们的容器现在完成了。我们可以:

*   解决现成的简单类
*   设置任意键来解析特定的类或可调用的结果，以便能够将接口映射到具体的实现，或者解析需要特定的非对象参数的类，例如 PDO
*   解析具有多个依赖项的复杂类

对于刚刚超过 100 行的 PHP 来说还不错…

# 最后的想法

正如我所说的，这是一个非常小的依赖注入容器的例子，当有这么多现有的、成熟的解决方案可用时，我不建议在生产中使用它。我不知道现有解决方案的性能如何，也不知道它是否有任何问题，坦率地说，这不是重点——这是为了了解依赖注入容器一般如何工作的学习练习，而不是作为实际有用的生产代码。如果你想要一个现成的容器，我会给你指出`league/container`的方向，它对我很有用。

你可以在 [GitHub](https://github.com/matthewbdaly/ernie) 上找到这个教程的代码，所以如果你有什么问题，你应该去那里看看问题出在哪里。当然，如果你继续在此基础上创建你自己的超级容器，一定要让我知道！