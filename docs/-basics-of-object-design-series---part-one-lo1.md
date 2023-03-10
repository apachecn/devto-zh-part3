# 对象设计基础-第一部分

> 原文：<https://dev.to/olivermensahdev/-basics-of-object-design-series---part-one-lo1>

在我之前的文章[对象设计基础系列](https://dev.to/omensah/basics-of-object-design-series-28ol)中，我分享了我一直在读的关于对象设计的书，[的](https://twitter.com/matthiasnoback)[对象设计风格指南](https://leanpub.com/object-design)，也通过一系列的博客文章分享了我的心得。到目前为止，我已经阅读了第一章，创建服务对象。在本文中，我们将讨论在创建服务对象时可以指导软件专业人员的某些原则。

作为软件专业人员，我们的主要目标是解决问题。建模是解决问题的方法之一。通过面向对象的建模，包括在软件中将问题域表示为对象的实践。从对象的角度考虑问题并在代码中表示它们可以使一切保持有序、灵活和可重用，因为对象就在我们周围。在一个应用程序中，可能会设计和创建两种类型的对象。他们是；

I)用于执行任务或给出信息的对象
ii)作为数据存储及其操作的对象

执行任务的对象被称为服务。众所周知，他们在做一件事。例如，如果我们拿一个像`FileLogger`这样的对象——记录文件是我们想到的唯一功能。这在直觉上是正确的。通常，我们甚至可以从服务的名称中了解它的用途。

## 关于服务我们应该知道的常见事情。

**它们是以不变的方式创建的**:*因此，它们创建一次，可以多次使用，而不会改变其功能和属性。这意味着当创建服务对象时，它们不应该有不可预测的行为*

**他们是实干家** : *任何使用服务对象的对象都能知道该服务具体做什么。例如，有一个名为`Router`的服务，说明这个服务是为应用程序*中的路由功能准备的

## 创建服务对象的关键原则。

### 1)创建对象所需的一切都必须作为构造函数参数注入。

想象一下创建一个路由器对象。我们需要提供路由器类创建对象所需的内容。这些依赖关系应该通过构造函数注入。这使得它为使用做好了充分准备，并且在创建后没有重新配置。让我们用代码试试。

```
<?php 

class Router
{
    public function __construct(array $routes)
    {
        $this->routes = $routes;
    }

    public function route($route): string
    {
        if (array_key_exists($route, $this->routes))
        {
            return $this->routes[$route];
        }

    }
}

$routes = array(
    "/" => "https://omensah.github.io",
    "/resume" => "https://omensah.github.io/resume", 
    "/contact" => "https://omensah.github.io/contact"
);
$routers = new Router($routes);
echo $routers->route("/"); 
```

Enter fullscreen mode Exit fullscreen mode

从代码中，我们可以看出构造函数只需要`routes`依赖项就可以使用了。

### 2)每当一个对象需要配置设置时，它们都必须作为构造函数参数注入。

有时创建一个对象需要配置对象的一部分，以便它的客户端知道某些决定。例如，当您使用一个像`FileLogger`这样的服务对象时，它的主要目的是将消息记录到一个文件中。嗯，我们可以将消息记录到任何目录下的文件中，而对象的客户机不知道具体的位置。为了防止这种情况，我们需要做的就是将所有配置作为构造函数参数传递。因此，客户端清楚地知道日志文件的位置。让我们试一个例子。

```
 <?php 
final class FileLogger {

    public function __construct(string $logFilePath){

        $this->logFilePath = $logFilePath;
    }

    public function log(string $message): void {
        file_put_contents(
            $this->logFilePath,
            $message,
            FILE_APPEND
        );
    }

}

$logger = new FileLogger("/home/olivermensah/Desktop/practices/OD/logs.txt");
$logger->log('A message'); 
```

Enter fullscreen mode Exit fullscreen mode

### 3)与任务相关的数据必须作为方法参数传递，而不是构造函数参数。

你可能已经注意到，在前两个例子中，我们有构造函数和方法参数。基本上，构造函数参数用于设置整个对象需要什么，而方法参数被注入到特定的方法中以完成手头的任务。例如，在`FileLogger`类中，将`message`参数传递给`log`方法确实很有意义。因此，只有`log`方法需要这样的依赖来完成它的任务。

**注意:**如果在日志方法中使用`message`参数之前，有其他操作必须对其进行操作，那么方法参数就没有必要了。

### 4)所有的构造函数参数都应该是必需的

当一个对象可以在不通过构造函数设置依赖的情况下工作时，我们根本不应该传递这样的依赖。没有将依赖值作为 null 传递的可选依赖。这引入了不必要的代码复杂性，因为在使用之前总是检查该依赖的无效性。让我们用代码试试。

```
 <?php

// null object

class Car {
    private $model = '';
    public function __construct($model = null){
        if($model){
            $this -> model = $model;
        }
    }
    public function getCarModel(){
        return ' The car model is: ' . $this -> model;
    }
}

$car = new Car();
echo $car->getCarModel();
$car1 = new Car("BMW");
echo $car->getCarModel(); 
```

Enter fullscreen mode Exit fullscreen mode

这些代码复杂性或变通办法可以通过提供默认值、要求所有依赖项或空对象来解决。

```
 <?php

// default value solution

class Car {
    private $model = '';
    public function __construct($model = "Mercedes"){
        $this -> model = $model;
    }
    public function getCarModel(){
        return ' The car model is: ' . $this -> model;
    }
}

$car = new Car();
echo $car->getCarModel();

$car1 = new Car("BMW");
echo $car->getCarModel();

//required dependency solution 

class Car {
    private $model = '';
    public function __construct(string $model){
        $this -> model = $model;
    }
    public function getCarModel(){
        return ' The car model is: ' . $this -> model;
    }
}

$car = new Car();
echo $car->getCarModel();
$car1 = new Car("BMW");
echo $car->getCarModel();

// null object

interface CarModel{
    public function getCarModel(): string;
}

class NullCarModel implements CarModel{
    public function getCarModel(): string{
        return ' The car model is: ';
    }
}

class MercedesCarModel implements CarModel{
    public function getCarModel(): string{
        return ' The car model is: is Mercedes';
    }
}

class Car {
    private $model = '';
    public function __construct(CarModel $carModel){
        $this->carModel = $carModel;
    }
    public function getCarModel(){
        return  $this->carModel->getCarModel();
    }
}

$car = new Car(new NullCarModel);
echo $car->getCarModel();

$car1 = new Car(new MercedesCarModel);
echo $car1->getCarModel(); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码引入了默认值、必需的依赖项和空对象来解决可选的依赖项。这是因为我们不想选择性地传递依赖关系，我们希望在需要使用时传递依赖关系。我认为 null 对象提供了一个很好的解决方法，并使依赖关系更明确地被注入。对于 null 对象，您必须做的是使用`interface`为所有可能的依赖关系创建一个类型。这些依赖项将使用此类型创建，并在以后注入以供使用。
例如，汽车需要一个`model`作为它的依赖，我创建了一个名为`CarModel`的类型，所有的汽车模型都是用这个类型创建的，然后通过构造函数注入。所以我们在没有任何`CarModel`和`MercedesCarModel`的时候就通过了`NullCarModel`对于奔驰汽车的车型来说。您可以创建任意数量的模型。

### 5)。用于构造函数注入，而不是 setter 注入。

在创建服务对象时，必须对其进行不变的定义。这意味着改变服务对象属性的方法是不允许的。让我们用代码试试；

```
final class Printer {

    public function ignoreErrors(bool $ignoreErrors): void{
        $this->ignoreErrors = $ignoreErrors;
    }
    public function print(){
        if($this->ignoreErrors) {
            echo "Error ignored";
        }
        else{
            echo "Errors not ignored";
        }
    }
// ...
}
$printer = new Printer();
$printer->ignoreErrors(false);
$printer->print(); 
```

Enter fullscreen mode Exit fullscreen mode

查看上面的代码，我们有一个方法可以将服务对象的属性设置为布尔状态。这意味着物体是不可预测的。

因此，建议只使用一个构造函数来设置一次所需的值，并且这些值不应该像下面重构的那样被更改；

```
 final class Printer {

    public function __construct(bool $ignoreErrors){
        $this->ignoreErrors = $ignoreErrors;
    }
    public function print(){
        if($this->ignoreErrors) {
            echo "Error ignored";
        }
        else{
            echo "Errors not ignored";
        }
    }
// ...
}
$ignoreErrors = true;
$printer = new Printer($ignoreErrors);
$printer->print(); 
```

Enter fullscreen mode Exit fullscreen mode

### 6)注入你一个对象需要的东西，而不是它能从哪里得到。

当处理具有大量代码库的大型应用程序时，总会有一个特殊的服务来保存其他服务，以便于使用。这种特殊服务可能被称为`service locator`或`service registry`或`service manager`。

一个`service locator`的主要目标是通过处理它们的实例化使其他服务变得容易使用。让我们想象一下有`Router`和`FileLogger`服务。它们将被载入`service locator`。和需要任何注册服务的应用程序的任何部分，它们可以使用特定的标识符到达服务定位器，如果没有找到标识符，就会抛出异常。让我们用代码试试。

```
 <?php 
final class FileLogger {

    public function __construct(string $logFilePath){

        $this->logFilePath = $logFilePath;
    }

    public function log(string $message): void {
        file_put_contents(
            $this->logFilePath,
            $message,
            FILE_APPEND
        );
    }

}

class Router
{
    public function __construct(array $routes)
    {
        $this->routes = $routes;
    }

    public function route($route): string
    {
        if (array_key_exists($route, $this->routes))
        {
            return $this->routes[$route];
        }

    }
}

final class ServiceLocator {

    public function __construct(){
        $this->services = [
            'FileLogger' => new  FileLogger("/home/olivermensah/Desktop/daily-practices/OD/log.txt"),
            "Router" => new Router(
                array(
                    "/" => "https://omensah.github.io",
                    "/resume" => "https://omensah.github.io/resume", 
                    "/contact" => "https://omensah.github.io/contact"
                )
            )
            // We can have any mumber of services here...
        ];
    }
    public function get(string $identifier): object{
        if (!isset($this->services[$identifier])) {
            throw new LogicException( 'Unknown service: ' . $identifier);
        }
        return $this->services[$identifier];
    }
}

//Injecting what you need to get them from service. 
final class FileLoggerController {

    public function __construct(FileLogger $fileLogger){
        $this->fileLogger = $fileLogger;
    }

    public function saveLog(){
        return $this->fileLogger->log("Oliver");
    }
}

$locator = new ServiceLocator();
$logger = new FileLoggerController($locator->get(FileLogger::class));
echo $logger->saveLog(); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们只传递了`FileLoggerController`工作所需的`FileLogger`。我们可以传递服务，然后再检索必要的服务。那违背了风格指南。只传递需要的东西，而不是你能从哪里得到的。

### 确保所有的依赖关系都是显式的。

有时，我们将代码深深嵌入到一个类中，而不让对象的客户端知道该对象是否有一些依赖关系。让我们用代码来看看这个。

```
 <?php

class Cache{
    private static $data = array(
        'post' => "Data is the new Gold", 
        'recent_post' => "Together we are building the tech scene in Ghana."
    );

    public static function has($key){
        return isset(self::$data[$key]);
    }

    public static function get($key){
        return self::$data[$key];
    }
}

final class DashboardController {
    public function getPost(){

        if (Cache::has('recent_post')) {
            echo Cache::get('recent_post');
        }else{
            echo "No Recent posts";
        }
    }
}

$ctrl = new DashboardController();
$ctrl->getPost(); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到,`DashboardController`将`Cache`作为一个依赖项，但是我们没有明确地让这个对象的客户端知道它有一个依赖项。这必须避免。确保客户端明确知道所有的依赖关系。让我们重构前面的代码。

```
class Cache{
    private static $data = array(
        'post' => "Data is the new Gold", 
        'recent_post' => "Together we are building the tech scene in Ghana, one 
                        meetup at a time"
    );

    public static function has($key){
        return isset(self::$data[$key]);
    }

    public static function get($key){
        return self::$data[$key];
    }
}

final class DashboardController{

    public function __construct(Cache $cache){
        $this->cache = $cache;
    }
    public function getPost(){
        // $recentPosts = [];
        if ($this->cache->has('recent_post')) {
            // $recentPosts = Cache::get('recent_posts');
            echo $this->cache->get('recent_post');
        }else{
            echo "No Recent posts";
        }
    }

}

$ctrl = new DashboardController(new Cache());
$ctrl->getPost(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，任何使用这个`DashboardController`的客户端都知道它使用`Cache`作为依赖项。这意味着永远不要隐藏你的依赖，通过构造函数或方法注入让他们知道。

### 8)。在构造函数中不做任何事情，只分配属性。

每当您创建一个服务对象时，这意味着您正在注入构造函数参数以准备使用它。将参数赋给对象属性之外的任何操作都必须在对象的另一个方法中完成，而不能在构造函数方法中完成。让我们回顾一下这段代码。

```
final class FileLogger{
    private  $logFilePath;

    public function __construct(string $logFilePath){
        $logFileDirectory = dirname($logFilePath);
        if (!is_dir($logFileDirectory)) {
            // create the directory if it doesn't exist yet
            mkdir($logFileDirectory, 0777, true);
        }
        touch($logFilePath);
        $this->logFilePath = $logFilePath;
    }
}

$logger = new FileLogger("/home/olivermensah/Desktop/oliver/d.txt"); 
```

Enter fullscreen mode Exit fullscreen mode

在构造函数中，我们检索给定路径的目录，检查该目录是否不存在，然后如果可能的话创建该目录，最后在该目录中添加文件。这需要构造函数做大量的工作。通过将这些操作从构造函数中提取到一个单独的方法中，我们可以做得更好。

```
 final class FileLogger{
    private $logFilePath;
    public function __construct(string $logFilePath){
        $this->logFilePath = $logFilePath;
    }
    public function log(): void{
        $this->ensureLogFileExists();
    }
    private function ensureLogFileExists(): void{
        if (is_file($this->logFilePath)) {
            return;
        }
        $logFileDirectory = dirname($this->logFilePath);
        if (!is_dir($logFileDirectory)) {
            // create the directory if it doesn't exist yet
            mkdir($logFileDirectory, 0777, true);
        }
        touch($this->logFilePath);
    }
}

$logger = new FileLogger("~/Desktop/oliver/a.txt");
$logger->log(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经能够将构造函数完成的工作提取到不同的方法中。

避免构造函数方法做大量工作的另一种方法是在实例化类之前设置必要的配置，结果作为构造函数参数传递。这通常被称为自举。

```
 final class FileLogger{
    public function __construct(string $logFilePath){
        /*
        * We expect that the log file path has already been
        * properly set up for us, so all we do here is a "sanity
        * check":
        */
        if (!is_writable($logFilePath)) {
            throw new InvalidArgumentException(sprintf(
                'Log file path "%s" should be writable',
                $logFilePath
            ));
        }
        $this->logFilePath = $logFilePath;
    }   
    public function log(string $message): void{
        // No need for a call to `ensureLogFileExists()` or anything
        // ...
    }
}
/*
* The task of creating the log directory and file should be moved to
* the bootstrap phase of the application itself:
*/
$logFilePath = "/home/olivermensah/Desktop/oliver/ca.txt";

if(!is_file($logFilePath)) {
    $logFileDirectory = dirname($logFilePath);
    if (!is_dir($logFileDirectory)) {
        // create the directory if it doesn't exist yet
        mkdir($logFileDirectory, 0777, true);
    }
    touch($logFilePath);
}
$logger = new FileLogger($logFilePath); 
```

Enter fullscreen mode Exit fullscreen mode

### 9)。当参数无效时引发异常

当该类的客户端提供无效参数时，类型检查器将显示警告。然而，并不是在所有情况下客户端都会传递错误的类型。传递正确的类型，但传递对对象方法的操作没有意义的值。在这种情况下，您需要抛出一个异常来确保健全性检查。

```
 <?php 

class Score  {
    private $minimumLevel;
    public function __construct(int $minimumLevel) {
        if ($minimumLevel <= 0) {
            throw new InvalidArgumentException('Minimum alerting level should be greater than 0');
        }
        $this->minimumLevel = $minimumLevel;
    }
}

$score = new Score(-100); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在浏览了关于如何在软件应用程序中创建好的服务对象的各种风格指南之后，我认为下面的要点将巩固我们如何设计服务对象的基础；

1)创建对象所需的一切都必须作为构造函数参数注入。

2)每当对象需要配置设置时，它们必须作为构造函数参数注入。

3)与任务相关的数据必须作为方法参数传递，而不是构造函数参数。

4)所有构造函数参数都应该是必需的

5)使用构造函数注入，而不是 setter 注入。

6)注入你一个对象需要的东西，而不是它能从哪里得到它。

7)确保所有依赖关系都是显式的。

8)在构造函数中不做任何事情，只分配属性。

9)当参数无效时抛出异常

我希望你觉得它们非常有用。
**NB** :让我们在评论区讨论一下你对这些指南的看法。

## 进一步阅读

[Matthias Noback 的](https://twitter.com/matthiasnoback)描述是从实用设计原则的角度对[风格的对象设计指南](https://leanpub.com/object-design)的最好的深入讨论之一。

## 保持联系

当我们继续学习和探索更多关于软件工程的知识时，让我们保持联系。别忘了在推特或 T2【LinkedIn】和我联系