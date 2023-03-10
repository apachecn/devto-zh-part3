# 装饰服务类别

> 原文：<https://dev.to/matthewbdaly/decorating-service-classes-54il>

我以前写过关于使用 decorators 来扩展现有类的功能的文章，在 repository 模式的上下文中使用 concertive。然而，同样的做法也适用于许多其他情况。

最近，我被要求在遗产项目的主页上添加 RSS 提要，这是我这些天的主要关注点。最终的服务类看起来像这样:

```
<?php

namespace App\Services;

use Rss\Feed\Reader;
use App\Contracts\Services\FeedFetcher;

class RssFetcher implements FeedFetcher
{
    public function fetch($url)
    {
        return Reader::import($url);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

按照松耦合的原则，我还为它创建了一个接口:

```
<?php

namespace App\Contracts\Services;

interface FeedFetcher
{
    public function fetch($url);
} 
```

Enter fullscreen mode Exit fullscreen mode

我最近能够使用 PHP-DI 将依赖注入添加到项目中，所以现在我可以通过类型提示接口并将它解析到`RssFetcher`类来将 feed fetcher 的实例注入到控制器中。

然而，有一个问题。我不希望应用程序在每次页面加载时发出多个 HTTP 请求来获取这些提要。同时，运行一个调度任务来获取这些提要并将它们存储在数据库中也有点多，因为很多时候这是不必要的。显而易见的解决方案是在指定的时间长度内缓存提要内容，在本例中是五分钟。

我*可以*将缓存集成到服务类本身中，但这不是最佳实践，因为它会被绑定到实现中。如果将来我们需要切换到不同的提要处理程序，我们必须重新实现缓存功能。所以我认为装饰服务类是有意义的。

decorator 类实现了与 feed fetcher 相同的接口，并在构造函数中接受了该接口的另一个实例，以及一个 PSR6 兼容的缓存库。大概是这样的:

```
<?php

namespace App\Services;

use App\Contracts\Services\FeedFetcher;
use Psr\Cache\CacheItemPoolInterface;

class FetcherCachingDecorator implements FeedFetcher
{
    protected $fetcher;

    protected $cache;

    public function __construct(FeedFetcher $fetcher, CacheItemPoolInterface $cache)
    {
        $this->fetcher = $fetcher;
        $this->cache = $cache;
    }

    public function fetch($url)
    {
        $item = $this->cache->getItem('feed_'.$url);
        if (!$item->isHit()) {
            $item->set($this->fetcher->fetch($url));
            $this->cache->save($item);
        }
        return $item->get();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您实例化提要提取器时，您将它包装在装饰器中，如下所示:

```
<?php

$fetcher = new FetcherCachingDecorator(
        new App\Services\RssFetcher,
        $cache
); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这很好地解决了我们的问题。通过将我们的提要提取器包装在这个装饰器中，我们保持了缓存层与提取器的任何一个实现完全分离，因此如果我们需要用另一个实现替换当前的实现，我们根本不需要接触缓存层。只要我们使用依赖注入来解析这个接口，我们只需要再看一点代码来实例化它。

此外，同样的方法可以应用于其他目的，您可以根据需要多次包装服务类。例如，如果我们想记录我们得到的所有响应，我们可以写一个类似这样的日志装饰器:

```
<?php

namespace App\Services;

use App\Contracts\Services\FeedFetcher;
use Psr\Log\LoggerInterface;

class FeedLoggingDecorator implements FeedFetcher
{
    protected $fetcher;

    protected $logger;

    public function __construct(FeedFetcher $fetcher, LoggerInterface $logger)
    {
        $this->fetcher = $fetcher;
        $this->logger = $logger;
    }

    public function fetch($url)
    {
        $response = $this->fetcher->fetch($url);
        $this->logger->info($response);
        return $response;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样的想法也可以应用于 API 客户端。例如，假设我们有一个 API 客户端的接口:

```
<?php

namespace Foo\Bar\Contracts;

use Foo\Bar\Objects\Item;
use Foo\Bar\Objects\ItemCollection;

interface Client
{
    public function getAll(): ItemCollection;

    public function find(int $id): Item;

    public function create(array $data): Item;

    public function update(int $id, array $data): Item;

    public function delete(int $id);
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，任何好的 API 客户端都应该尊重 HTTP 头，并使用这些头来进行缓存，但是根据使用情况，您可能也想自己缓存这些请求。例如，如果对第三方 API 存储的实体的唯一更改是您所做的，或者它们不需要 100%最新，那么您最好在它们到达实际的 API 客户端之前缓存这些响应。在这种情况下，您可以编写一个这样的装饰器来进行缓存:

```
<?php

namespace Foo\Bar\Services;

use Foo\Bar\Contracts\Client;
use Psr\Cache\CacheItemPoolInterface;

class CachingDecorator implements Client
{
    protected $client;

    protected $cache;

    public function __construct(Client $client, CacheItemPoolInterface $cache)
    {
        $this->client = $client;
        $this->cache = $cache;
    }

    public function getAll(): ItemCollection
    {
        $item = $this->cache->getItem('item_all');
        if (!$item->isHit()) {
            $item->set($this->client->getAll());
            $this->cache->save($item);
        }
        return $item->get();
    }

    public function find(int $id): Item
    {
        $item = $this->cache->getItem('item_'.$id);
        if (!$item->isHit()) {
            $item->set($this->client->find($id));
            $this->cache->save($item);
        }
        return $item->get();
    }

    public function create(array $data): Item
    {
        $this->cache->clear();
        return $this->client->create($data);
    }

    public function update(int $id, array $data): Item
    {
        $this->cache->clear();
        return $this->client->update($id, $data);
    }

    public function delete(int $id)
    {
        $this->cache->clear();
        return $this->client->delete($id);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

任何改变远程 API 上的数据状态的方法都将清除缓存，而任何获取数据的方法都将首先检查缓存，只有当缓存为空时才显式地从 API 获取数据，并再次缓存它。我不会深入讨论如何为此编写一个日志装饰器，但是自己弄清楚应该很简单。

装饰器模式是一种非常强大的方式，它可以向类添加功能，而不需要将其绑定到特定的实现。如果您熟悉中间件的工作方式，decorator 的工作方式与此非常相似，您可以将您的服务包装成任意多的层，以便完成特定的任务，并且它们通过允许您为不同的任务使用不同的 decorator 来遵守单一责任原则。