# 领域驱动设计 PHP 中的工厂

> 原文：<https://dev.to/coajaxial/domain-driven-design---the-factory-in-php-4gea>

编辑:在修改后的 TimeSpanFactory 中添加了用法示例。

在领域驱动设计中，我们希望我们的领域模型是防弹的。有时，在引入一个新对象时，有必要确保一些业务规则。如果这种构造太复杂或者对象本身不能保证，那么你应该把这个对象的构造放到一个专用的类中:一个工厂。

让我们举个简单的例子。假设我们有一个 TimeSpan 值对象。我们最初的 TimeSpan 实现可能是这样的:

```
<?php

class TimeSpan
{
    /** @var \DateTimeImmutable **/
    private $from;

    /** @var \DateTimeImmutable **/
    private $until;

    public function __construct(\DateTimeImmutable $from, \DateTimeImmutable $until) 
    {
        if ( $from >= $until ) {
            throw new \InvalidArgumentException('Invalid time span.');
        }
        $this->from = $from;
        $this->until = $until;
    }

    // Some other useful stuff goes in here...
} 
```

假设我们不希望时间跨度是无限的，某个用户可以通过如下配置指定一个最大值:

```
<?php

class TimeSpanConfiguration 
{
    /** @var \DateInterval **/
    private $maxTimeSpan;

    public function __construct(\DateInterval $maxTimeSpan) 
    {
        $this->maxTimeSpan = $maxTimeSpan;
    }
} 
```

所以现在我们有了一个业务规则:时间跨度不能超过配置的最大值。我们如何实施呢？我们可以随心所欲地构造 TimeSpan 实例，没有任何东西会强制这样的配置。和往常一样，这个问题有多种解决方案。我想给你看的是工厂:

```
<?php

class TimeSpanFactory 
{
    /** @var TimeSpanConfiguration **/
    private $maxTimeSpan;

    public function __construct(TimeSpanConfiguration $timeSpanConfiguration) 
    {
        // For the sake of simplicity we just give the factory the configuration directly.
        $this->timeSpanConfiguration = $timeSpanConfiguration;
    }

    public function createTimeSpan(\DateTimeImmutable $from, \DateTimeImmutable $until): TimeSpan
    {
        // We just ask the configuration if the given from-until time span is valid.
        // That way we don't need any getters on the configuration. Neat.
        if ( !$this->timeSpanConfiguration->isValidTimeSpanFromUntil($form, $until) ) {
            throw new \DomainException('This time span is too long!');
        }

        return new TimeSpan($from, $until);
    }

} 
```

现在，在构造新的 TimeSpan 时，我们只是直接使用 TimeSpanFactory 而不是 TimeSpan 的构造函数。这样，我们总是得到一个有效的时间跨度，它不长于配置的最大值。

你现在可能会问:当直接使用构造函数时，我仍然可以构造一个无效的 TimeSpan。是的，你是对的！这可能是一个取决于你的团队的问题。如果你的团队足够小，你可以同意总是使用 TimeSpanFactory 而不是直接使用构造函数。但是有一个强制你使用工厂的解决方案:反射。这可能会引入一些你不想要的副作用，但我还是会给你看；)

首先，制作 TimeSpan 的构造函数`private` :

```
<?php

class TimeSpan
{
    /** @var \DateTimeImmutable **/
    private $from;

    /** @var \DateTimeImmutable **/
    private $until;

    private function __construct(\DateTimeImmutable $from, \DateTimeImmutable $until) 
    {
        if ( $from >= $until ) {
            throw new \InvalidArgumentException('Invalid time span.');
        }
        $this->from = $from;
        $this->until = $until;
    }

    // Some other useful stuff goes in here...
} 
```

现在，没有办法用一个简单的`new`来构造一个 TimeSpan。当你尝试这样做时，PHP 将抛出一个致命错误。好吧，但是如果你不能再建造它，我们的时代工厂怎么能建造它呢？反思拯救！让我们看看我们的新实现 TimeSpanFactory:

```
<?php

class TimeSpanFactory 
{
    /** @var TimeSpanConfiguration **/
    private $maxTimeSpan;

    public function __construct(TimeSpanConfiguration $timeSpanConfiguration) 
    {
        // For the sake of simplicity we just give the factory the configuration directly.
        $this->timeSpanConfiguration = $timeSpanConfiguration;
    }

    public function createTimeSpan(\DateTimeImmutable $from, \DateTimeImmutable $until): TimeSpan
    {
        // We just ask the configuration if the given from-until time span is valid.
        // That way we don't need any getters on the configuration. Neat.
        if ( !$this->timeSpanConfiguration->isValidTimeSpanFromUntil($form, $until) ) {
            throw new \DomainException('This time span is too long!');
        }

        return $this->constructTimeSpan($from, $until);
    }

    private function constructTimeSpan(\DateTimeImmutable $from, \DateTimeImmutable $until): TimeSpan
    {
        $class = new ReflectionClass(TimeSpan::class);
        $constructor = $class->getConstructor();
        $constructor->setAccessible(true);
        $timeSpan = $class->newInstanceWithoutConstructor();
        $constructor->invoke($timeSpan, $from, $until);

        return $timeSpan;
    }

}

// Usage:
$factory = new TimeSpanFactory(new TimeSpanConfiguration(new \DateInterval('PT2D')));

$timeSpan = $factory->constructTimeSpan(new \DateTimeImmutable('2019-02-17 17:00:00'), new \DateTimeImmutable('2019-02-17 18:00:00'));

// Fails due too to long time span
$timeSpan = $factory->constructTimeSpan(new \DateTimeImmutable('2019-02-17 17:00:00'), new \DateTimeImmutable('2019-02-17 23:00:00'));

// Also failes, but due to private constructor ;)
$timeSpan = new TimeSpan(new \DateTimeImmutable('2019-02-17 17:00:00'), new \DateTimeImmutable('2019-02-17 23:00:00')); 
```

佤人...

[![Wait, that's illegal!](img/6eb74e66e09de20ff02578fad284c1d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a7a8izkN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.kym-cdn.com/entries/icons/mobile/000/028/207/Screen_Shot_2019-01-17_at_4.22.43_PM.jpg)

我同意，但是我宁愿有一个防弹的域模型，也不愿有人构建一个违反业务规则的无效时间跨度。这可能是一个不知道应该使用 TimeSpanFactory 的初级开发人员，甚至是一个刚刚忘记工厂的高级开发人员。

但是，正如我所说，这取决于你的团队。

DDD 快乐！

PS:我在不到 10 分钟的时间里写了这篇文章，所有的样本都是在 dev.to 的 markdown 编辑器中从头开始写的，所以请:如果你发现任何 bug，错别字等。，让我知道:)