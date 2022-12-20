# 为 MVC 框架创建路由器

> 原文：<https://dev.to/emanuelvintila/creating-a-router-for-an-mvc-framework-1bih>

这是一篇来自我自己博客的交叉文章:[为 MVC 框架创建路由器](https://reflection.to/posts/php/1-Creating_a_Router_for_an_MVC_framework)

如果你不熟悉 MVC，我建议你在阅读本文之前先阅读一篇关于这个主题的文章。

这篇文章中的大部分代码至少需要 PHP 7.1 版本。

让我们假设你的域是`example.com`，你想构建你的应用程序，这样对`example.com/gallery/cats`的请求向用户显示一个小猫图片的图库，对`example.com/gallery/dogs`的请求显然应该显示一个狗的图库。

通过分析 URL，我们注意到 *gallery* 单词没有改变，只有*猫*、*狗*，或者任何你在数据库中有照片的东西。然后我们将创建一个处理这个逻辑的类。

```
<?php
// GalleryController.php
class GalleryController
{
    /** @var string */
    private $animal_type;

    public function __construct(string $animal_type)
    {
        $this->animal_type = $animal_type;
    }

    public function display()
    {
        // do whatever you need here, fetch from database, etc.
        echo $this->animal_type;
    }
} 
```

因此，当用户将浏览器指向`example.com/gallery/cats`时，应用程序需要用 *cats* 参数实例化新定义的类`GalleryController`，然后调用`display`方法。我们将使用正则表达式实现路由器。让我们首先定义一个数据类，它将请求与控制器和方法相关联。

```
<?php
// Route.php
class Route
{
    /** @var string */
    public $path;
    /** @var string */
    public $controller;
    /** @var string */
    public $method;

    public function __construct(string $path, string $controller, string $method)
    {
        $this->path = $path;
        $this->controller = $controller;
        $this->method = $method;
    }
} 
```

我们将定义一个非常简单的`Router`类，然后我们将使用它来看看它是如何工作的。

```
<?php
// Router.php
class Router
{
    /** @var Route[] */
    private $routes;

    public function register(Route $route)
    {
        $this->routes[] = $route;
    }

    public function handleRequest(string $request)
    {
        $matches = [];
        foreach ($this->routes as $route) {
            if (preg_match($route->path, $request, $matches)) {
                // $matches[0] will always be equal to $request, so we just shift it off
                array_shift($matches);
                // here comes the magic
                $class = new ReflectionClass($route->controller);
                $method = $class->getMethod($route->method);
                // we instantiate a new class using the elements of the $matches array
                $instance = $class->newInstance(...$matches);
                // equivalent:
                // $instance = $class->newInstanceArgs($matches);
                // then we call the method on the newly instantiated object
                $method->invoke($instance);
                // finally, we return from the function, because we do not want the request to be handled more than once
                return;
            }
        }
        throw new RuntimeException("The request '$request' did not match any route.");
    }
} 
```

现在，为了实际运行应用程序并测试`Router`类，创建一个包含以下内容的`index.php`文件，并配置您的 web 服务器将所有请求重定向到它。

```
<?php
// index.php
spl_autoload_extensions('.php');
spl_autoload_register();

$router = new Router();
$router->register(new Route('/^\/gallery\/(\w+)$/', 'GalleryController', 'display'));
$router->handleRequest($_SERVER['REQUEST_URI']); 
```

最后，将你的浏览器指向`example.com/gallery/cats`，它会在你的屏幕上显示单词*猫*。它的工作方式相当简单:

1.  我们向路由器注册路由，并告诉它处理传入的请求
2.  路由器检查所有注册的路由，查看请求是否与其中任何一条匹配
3.  它找到匹配的路由，用指定的名称实例化一个新类，向其构造函数提供所需的参数，然后在实例上调用指定的方法

这很简单。让我们再往前走一点。如果你的控制器构造函数接受一个对象作为参数，而不是一个基本类型呢？我们将定义一个`User`类，公开他们的姓名和年龄:

```
<?php
// User.php
class User
{
    /** @var string */
    public $name;
    /** @var int */
    public $age;

    public function __construct(string $name, int $age)
    {
        $this->name = $name;
        $this->age = $age;
    }
} 
```

和一个接受一个`User`作为参数的`UserController`类:

```
<?php
// UserController.php
class UserController
{
    /** @var User */
    private $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }

    public function show()
    {
        echo "{$this->user->name} is {$this->user->age} years old.";
    }
} 
```

让我们在主脚本中注册一个新的路由，就在调用`handleRequest` :
`$router->register(new Route('/^\/users\/(\w+)\/(\d+)$/', 'UserController', 'show'));`
之前，现在，如果你去`example.com/users/mike/26`，你会得到一个异常，因为`Router`试图传递一个字符串给`UserController`的构造函数，而不是一个`User`。解决方法包括使用更多的反射。

```
<?php
// Router.php
class Router
{
    /** @var Route[] */
    private $routes;

    public function register(Route $route)
    {
        $this->routes[] = $route;
    }

    public function handleRequest(string $request)
    {
        $matches = [];
        foreach ($this->routes as $route) {
            if (preg_match($route->path, $request, $matches)) {
                // $matches[0] will always be equal to $request, so we just shift it off
                array_shift($matches);
                // here comes the magic
                $class = new ReflectionClass($route->controller);
                $method = $class->getMethod($route->method);
                // we construct the controller using the newly defined method
                $instance = $this->constructClassFromArray($class, $matches);
                // then we call the method on the newly instantiated object
                $method->invoke($instance);
                // finally, we return from the function because we do not want the request to be handled more than once
                return;
            }
        }
        throw new RuntimeException("The request '$request' did not match any route.");
    }

    private function constructClassFromArray(ReflectionClass $class, array &$array)
    {
        $parameters = $class->getConstructor()->getParameters();
        // construct the arguments needed for its constructor
        $args = [];
        foreach ($parameters as $parameter)
            $args[] = $this->constructArgumentFromArray($parameter, $array);

        // then return the new instance
        return $class->newInstanceArgs($args);
    }

    private function constructArgumentFromArray(ReflectionParameter $parameter, array &$array)
    {
        $type = $parameter->getType();
        // if the parameter was not declared with any type, just return the next element from the array
        if ($type === null)
            return array_shift($array);

        // if the parameter is a primitive type, just cast it
        switch ($type->getName()) {
            case 'string':
                return (string) array_shift($array);
            case 'int':
                return (int) array_shift($array);
            case 'bool':
                return (bool) array_shift($array);
        }

        $class = $parameter->getClass();
        // if the parameter is a class type
        if ($class !== null) {
            // make another call that will actually call this method
            return $this->constructClassFromArray($class, $array);
        }

        throw new RuntimeException("Cannot construct the '{$parameter->getName()}' parameter in {$parameter->getDeclaringClass()->getName()}::{$parameter->getDeclaringFunction()->getName()} because it is of an invalid type{$type->getName()}.");
    }
} 
```

现在`UserController`类被正确实例化，因为`Router`知道如何构造控制器期望的`User`参数。但是，如果`User`的构造函数有一个表示三态值的可空布尔参数，例如它们是否通过了某个测试，会发生什么呢？当然，PHP 的布尔转换规则仍然成立。让我们修改`User`类来包含这个三态布尔:

```
<?php
// User.php
class User
{
    /** @var string */
    public $name;
    /** @var int */
    public $age;
    /** @var bool|null */
    public $passed_test;

    public function __construct(string $name, int $age, ?bool $passed_test)
    {
        $this->name = $name;
        $this->age = $age;
        $this->passed_test = $passed_test;
    }
} 
```

而`UserController`的`show`法为:

```
public function show()
{
    $message = 'invalid';
    if ($this->user->passed_test === true)
        $message = 'They passed the test!';
    elseif ($this->user->passed_test === false)
        $message = 'They didn\'t pass the test!';
    elseif ($this->user->passed_test === null)
        $message = 'They didn\'t attempt the test yet.';
    echo "{$this->user->name} is {$this->user->age} years old.\n";
    echo $message;
} 
```

最后，让我们修改用户的路由，以反映这一变化:
`$router->register(new Route('/^\/users\/(\w+)\/(\d+)\/?(\w+)?$/', 'UserController', 'show'));`
再次将浏览器指向`example.com/users/mike/26`实际上会将它们的`passed_test`属性设置为 false 而不是 null。但是为什么呢？这是因为当我们构造`User`类时，它的构造函数需要 3 个参数，但是 URL 只包含两个。因此，在`constructArgumentFromArray`中对`array_shift`的最后一次调用实际上返回了`null`，它被转换为`bool`，这是假的。这是一个简单的修复方法。`constructArgumentFromArray`方法变为:

```
private function constructArgumentFromArray(ReflectionParameter $parameter, array &$array)
{
    $type = $parameter->getType();
    // if the parameter was not declared with any type, just return the next element from the array
    if ($type === null)
        return array_shift($array);

    $class = $parameter->getClass();
    // if the parameter is a class type
    if ($class !== null) {
        // make another call that will actually call this method
        return $this->constructClassFromArray($class, $array);
    }

    // we ran out of $array elements
    if (count($array) === 0)
        // but we can pass null if the parameter allows it
        if ($parameter->allowsNull())
            return null;
        else
            throw new RuntimeException("Cannot construct the '{$parameter->getName()}' in {$parameter->getDeclaringClass()->getName()}::{$parameter->getDeclaringFunction()->getName()} because the array ran out of elements.");

    // if the parameter is a primitive type, just cast it
    switch ($type->getName()) {
        case 'string':
            return (string) array_shift($array);
        case 'int':
            return (int) array_shift($array);
        case 'bool':
            return (bool) array_shift($array);
    }

    throw new RuntimeException("Cannot construct the '{$parameter->getName()}' parameter in {$parameter->getDeclaringClass()->getName()}::{$parameter->getDeclaringFunction()->getName()} because it is of an invalid type{$type->getName()}.");
} 
```

现在，当我们将浏览器指向`example.com/users/mike/26`和`example.com/users/mike/26/any_truthy_or_falsy_value`时，`User`类被正确实例化。