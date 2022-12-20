# ★实施事件来源:聚集

> 原文：<https://dev.to/freekmurze/implementing-event-sourcing-aggregates-3ohf>

最近我们[发布了 laravel-event-projector](https://freek.dev/laravel-event-projector-v2-has-been-released) 的 [v2。这个包可能是在 Laravel 中开始事件源的最简单的方法。](https://docs.spatie.be/laravel-event-projector/v2/introduction)

一个突出的新特性是对聚合的支持。在创建 v2 时，我惊讶地发现如此强大的概念可以在如此少的代码中实现。在这篇简短的博文中，我想解释一下聚合是如何编码的。

如果你对事件源一无所知，或者不知道什么是聚合，那就去看看这个包的文档。它包含了针对新来者的完整介绍。本文的其余部分假设您知道什么是聚合，并且知道如何使用它们。

## 从以前的事件中重建一个集合体

在使用聚合之前，必须根据给定 uuid 的所有先前事件对其进行重组。作为一个包用户，你可以用`MyAggreggate::retrieve($uuid)`来做这件事。下面是该函数的实现。

```
public static function retrieve(string $uuid): AggregateRoot
{
    $aggregateRoot = (new static());

    $aggregateRoot->aggregateUuid = $uuid;

    return $aggregateRoot->reconstituteFromEvents();
} 
```

`$uuid`将作为实例变量`aggregateUuid`保存。实际的重建发生在`reconstituteFromEvents`。让我们来看看这段代码。

```
private function reconstituteFromEvents(): AggregateRoot
{
    StoredEvent::uuid($this->aggregateUuid)->each(function (StoredEvent $storedEvent) {
        $this->apply($storedEvent->event);
    });

    return $this;
} 
```

那个`::uuid`就是[只是在`StoredEventModel`上定义的一个范围](https://github.com/spatie/laravel-event-projector/blob/47f9d7a6479a1860e91956229a4463a706559c68/src/Models/StoredEvent.php#L59-L62)。那个`each`调用正在一个`Builder`实例上被调用，而不是一个`Collection`。在引擎盖下，`each`调用将以分块的方式获取所有事件。

让我们来看看那个`apply`方法。它会将事件的类名转换为方法名。`App\Event\MoneyAdded`将转换为`applyMoneyAdded`。如果聚合中存在具有该名称的方法，将使用事件作为参数调用该方法。

```
private function apply(ShouldBeStored $event): void
{
    $classBaseName = class_basename($event);

    $camelCasedBaseName = ucfirst(Str::camel($classBaseName));

    $applyingMethodName = "apply{$camelCasedBaseName}";

    if (method_exists($this, $applyingMethodName)) {
        $this->$applyingMethodName($event);
    }
} 
```

这基本上就是从过去的事件中重建一个集合所需的全部代码。

## 记录新事件

当一个集合被持久化时，它记录的所有新事件都应该被存储。这很容易实现。聚合提供了一个`recordThat`函数来记录新事件。

```
public function recordThat(ShouldBeStored $event): AggregateRoot
{
    $this->recordedEvents[] = $event;

    $this->apply($domainEvent);

    return $this;
} 
```

该函数将事件保存在实例的数组中。它还会立即将其应用到聚合中(使用我们前面已经讨论过的`apply`方法)。

当在聚合上调用`persist`时，所有的事件都将被传递给`StoredEvent`模型的`storeMany`方法。这个`$recordedEvents`数组将被清空，这样我们就不会记录相同的事件，如果在这个聚合实例上再次调用`persist`的话。

```
public function persist(): AggregateRoot
{
    StoredEvent::storeMany(
       $this->getAndClearRecoredEvents(),
       $this->aggregateUuid,
    );

    return $this;
}

private function getAndClearRecoredEvents(): array
{
    $recordedEvents = $this->recordedEvents;

    $this->recordedEvents = [];

    return $recordedEvents;
} 
```

因为事件在`storeMany`中的实际存储不是聚合的责任，所以我们不打算详细讨论它。简而言之， [`storeMany`](https://github.com/spatie/laravel-event-projector/blob/47f9d7a6479a1860e91956229a4463a706559c68/src/Models/StoredEvent.php#L74-L97) 将存储事件，将它们传递给所有注册的投影仪，并将分派作业将它们传递给所有排队的投影仪和反应器。

据此，我们实现了聚合。