# ★实现事件源:改善开发者体验

> 原文：<https://dev.to/freekmurze/implementing-event-sourcing-improving-the-developer-experience-4nac>

最近我们发布了 laravel-event-projector 的 [v2。这个包可能是在 Laravel 中开始事件源的最简单的方法。在 v2 中，我们引入了两个改善开发人员体验的“不可见”特性:事件处理方法的自动检测和事件处理程序的自动发现。](https://freek.dev/laravel-event-projector-v2-has-been-released)

即使你对事件采购一无所知，你也应该能够理解这篇文章的大部分内容。为了对我们的软件包有一个基本的了解，请阅读 laravel-event-projector 文档的介绍部分。

## 什么是事件处理程序？

事件处理程序是当某些事件被触发时将被调用的类。这里有一个投影仪的例子(投影仪是一种特殊类型的事件处理器)。

```
class BalanceProjector implements Projector
{
    use ProjectsEvents;

    protected $handlesEvents = [
        MoneyAddedEvent::class => 'onMoneyAdded',
        MoneySubtractedEvent::class => 'onMoneySubtracted',
    ];

    public function onMoneyAdded(MoneyAddedEvent $event)
    {
        // do some work
    }

    public function onMoneySubtracted(MoneySubtractedEvent $event)
    {
        // do some work
    }
} 
```

## 自动检测事件的处理方法

这个`handlesEvent`数组决定了当特定事件到来时应该调用哪些函数。例如，当这个投影仪接收到一个`MoneyAddedEvent`时，就应该调用`onMoneyAdded`函数。如果你看一下这个函数，你会发现我们想要接收的事件是类型暗示的。

如果用户不必编写一个`handlesEvent`数组，并且软件包会自动调用提示事件的函数，这不是很酷吗？在最新版本的事件投影仪中，我们做到了这一点。

让我们看看来自[`HandlesEvents`-trait](https://github.com/spatie/laravel-event-projector/blob/47f9d7a6479a1860e91956229a4463a706559c68/src/EventHandlers/HandlesEvents.php)的相关代码，它应用于每个事件处理程序。`autoDetectHandlesEvents`将使用一些反射来获取事件处理程序上的每个方法。接下来，对于找到的每个函数，它将检查第一个参数是否是实现`ShouldBeStored`的类。

```
private function autoDetectHandlesEvents(): Collection
{
    return collect((new ReflectionClass($this))->getMethods())
        ->flatMap(function (ReflectionMethod $method) {
            $method = new ReflectionMethod($this, $method->name);
            $eventClass = collect($method->getParameters())
                ->map(function (ReflectionParameter $parameter) {
                    return optional($parameter->getType())->getName();
                })
                ->first(function ($typeHint) {
                    return is_subclass_of($typeHint, ShouldBeStored::class);
                });

            if (! $eventClass) {
                return;
            }

            return [$eventClass => $method->name];
        })
        ->filter(); 
```

`autoDetectHandlesEvents`将返回一个数组，用将事件路由到正确的方法名。

## 自动注册事件处理程序

在包的 v1 中，每个事件处理程序都必须在`Projectionist`注册。`Projectionist`是一个确保当某些事件被触发时事件处理程序被调用的类。

```
Projectionist::addProjector(MyProjector::class);
Projectionist::addReactor(MyReactor::class); 
```

在最新版本的包中，您不再需要手动这样做。该包将自动查找和使用事件处理程序。该功能的灵感来自 Laravel [auto 如何发现其事件和事件监听器](https://blog.laravel.com/automatic-event-listener-discovery)。让我们看看事件处理程序自动发现在我们的包中是如何工作的。

在[服务提供者](https://github.com/spatie/laravel-event-projector/blob/a7d6eb956ec7f225aa2fe0cd85ff81710dbe048c/src/EventProjectorServiceProvider.php)中，你会找到`discoverEventHandlers`函数，从`boot`方法中[调用](https://github.com/spatie/laravel-event-projector/blob/47f9d7a6479a1860e91956229a4463a706559c68/src/EventProjectorServiceProvider.php#L35)。

```
private function discoverEventHandlers()
{
    $projectionist = app(Projectionist::class);

    $cachedEventHandlers = $this->getCachedEventHandlers();

    if (! is_null($cachedEventHandlers)) {
        $projectionist->addEventHandlers($cachedEventHandlers);

        return;
    }

    (new DiscoverEventHandlers())
        ->within(config('event-projector.auto_discover_projectors_and_reactors'))
        ->useBasePath(base_path())
        ->ignoringFiles(Composer::getAutoloadedFiles(base_path('composer.json')))
        ->addToProjectionist($projectionist);
} 
```

如果事件处理程序被缓存，它将使用这些缓存的注册，而不执行自动发现。我们以后再谈这个。

真正的自动发现发生在`DiscoverEventHandlers`类内部。我们将查找事件处理程序类的目录传递给它，默认情况下这将是`app_path()`。我们还会把它交给`base_path()`。这是需要的`DiscoverEventHandlers`将使用该路径将路径名转换为类名。

我们来看看实现链中的最后一个函数:`addToProjectionist`。这是 [`DiscoverEventHandlers`](https://github.com/spatie/laravel-event-projector/blob/47f9d7a6479a1860e91956229a4463a706559c68/src/DiscoverEventHandlers.php) :
最重要的功能

```
public function addToProjectionist(Projectionist $projectionist)
{
    $files = (new Finder())->files()->in($this->directories);

    return collect($files)
        ->reject(function (SplFileInfo $file) {
            return in_array($file->getPathname(), $this->ignoredFiles);
        })
        ->map(function (SplFileInfo $file) {
            return $this->fullQualifiedClassNameFromFile($file);
        })
        ->filter(function (string $eventHandlerClass) {
            return is_subclass_of($eventHandlerClass, EventHandler::class);
        })
        ->pipe(function (Collection $eventHandlers) use ($projectionist) {
            $projectionist->addEventHandlers($eventHandlers->toArray());
        });
} 
```

首先，它将获取我们应该寻找事件处理程序的目录中的所有文件(如前所述，默认情况下这是`app_path()`)。

接下来，我们将拒绝一些我们现在不想处理的文件。默认情况下，这些是由 composer 加载的非类文件。

之后，我们将每个文件路径映射到类应该在那里。所以[`fullQualifiedClassNameFromFile`函数](https://github.com/spatie/laravel-event-projector/blob/a7d6eb956ec7f225aa2fe0cd85ff81710dbe048c/src/DiscoverEventHandlers.php#L73-L84)会把`/home/username/myproject.com/app/Projectors/MyProjector.php`转换成`App\Projectors\MyProjector`。

我们将继续过滤所有扩展我们的`EventHandler`类的类。最后，所有剩余的课程将在放映员处登记。

现在，您可能不希望在生产过程中扫描所有的类。这就是为什么这个包还包含一个命令来[缓存所有注册的事件处理程序](https://github.com/spatie/laravel-event-projector/blob/master/src/Console/CacheEventHandlersCommand.php)。简而言之，这个类将把所有注册的事件处理类写到一个文件中。就像我们已经看到的，服务提供商将[检查文件](https://github.com/spatie/laravel-event-projector/blob/a7d6eb956ec7f225aa2fe0cd85ff81710dbe048c/src/EventProjectorServiceProvider.php#L97-L103)的存在，并且当该文件存在时不执行自动发现。

## 在关闭

有了事件处理方法的自动检测和事件处理程序的自动发现，包的用户可以简单地在他们的项目中创建一个这样的类，它就可以工作了。不需要自己将事件路由到方法，也不需要注册任何东西。

```
class BalanceProjector implements Projector
{
    use ProjectsEvents;

    public function onMoneyAdded(MoneyAddedEvent $event)
    {
        // perform some work
    }

    public function onMoneySubtracted(MoneySubtractedEvent $event)
    {
        // perform some work
    }
} 
```

我相信这种润色对于我们软件包的用户来说有很大的不同。感谢[塞巴斯蒂安·德·迪恩](https://twitter.com/sebdedeyne)为[建议](https://github.com/spatie/laravel-event-projector/issues/143)这些功能。

如果你想了解更多关于 laravel-event-projector 本身的信息，可以去查看包的文档。