# 保护您的系统免受第三方依赖性的影响

> 原文：<https://dev.to/aleksikauppila/protect-your-system-from-changes-in-3rd-party-dependencies-8m3>

使用第三方代码总是有风险的。我为我的项目选择的一个开源库可能不再维护了。或者，升级另一个依赖项可能会有向后不兼容的更改。这个图书馆可能有一个糟糕的界面，但它提供的服务超过了负面影响。

日常工作通常是解决与业务相关的问题。客户通常不愿意开发和维护通用工具或库。所以我们使用开源库。

我们还想变得更聪明，保护我们的应用程序免受第三方依赖性的影响。我们不想让我们的依赖项保持最新，就像我们保持操作系统一样。

首先，让我们看一个如何处理第三方依赖的糟糕例子。这里我将使用流行的 HTTP 客户端`guzzlehttp/guzzle`作为例子。我们想使用 HTTP 客户端向外部系统发出请求。

```
use GuzzleHttp\Client;

class ExternalService
{
    private $httpClient;

    public function __construct()
    {
        $this->httpClient = new Client();
    }
    // ...
} 
```

在这个例子中，我们在构造函数中自由地声明了一个依赖项。这将阻止我们对这个类进行单元测试。我们应该在这里使用[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection#Constructor_injection)。

这个例子中的另一个问题是，现在我们已经把自己绑在了一个具体的依赖项上。我们应该考虑[依赖倒置原则](https://en.wikipedia.org/wiki/Dependency_inversion_principle)，它声明我们不应该依赖于低级类的实现细节。

我们如何纠正这种情况？

### 设计你想要使用的界面

我们将定义一个 HTTP 客户端接口。

```
use Psr\Http\Message\ResponseInterface;

interface HttpClient
{
    public function get(string $uri, array $options): ResponseInterface;
    public function post(string $uri, array $options): ResponseInterface;
    public function put(string $uri, array $options): ResponseInterface;
    public function patch(string $uri, array $options): ResponseInterface;
    public function delete(string $uri, array $options): ResponseInterface;
} 
```

### 为 Guzzle 的 HTTP 客户端编写一个适配器

我们将利用 Guzzle 的功能，但将它隐藏在我们自己的界面后面。

```
use GuzzleHttp\ClientInterface;
use MyApp\HttpException;

final class GuzzleHttpClientAdapter implements HttpClient
{
    private $client;

    public function __construct(ClientInterface $client)
    {
        $this->client = $client;
    }

    public function get(string $uri, array $options): ResponseInterface
    {
        return $this->request("GET", $uri, $options);
    }

    public function post(string $uri, array $options): ResponseInterface
    {
        return $this->request("POST", $uri, $options);
    }

    // ... other required methods

    private function request(string $method, string $uri, array $options): ResponseInterface
    {
        try {
            return $this->client->request($method, $uri, $options);
        catch (\Exception $exception) {
            // This is an exception we defined in our application
            throw new HttpException($exception->getMessage(), $exception->getCode(), $exception);
        }
    }
} 
```

通过这种方式，我们有效地将第三方库的使用限制在整个应用程序中的单个方法调用上。更改或升级库不会对我们的整个应用程序产生连锁反应。

* [适配器模式](https://designpatternsphp.readthedocs.io/en/latest/Structural/Adapter/README.html)

### 在客户端代码中使用新接口

最后，当我们的应用程序中需要 HTTP 客户端时，我们必须使用接口声明依赖关系。**如果我们设计好了接口，改变依赖关系将只需要我们写一个新的适配器**并在需要的地方注入它。`HttpClient`的用户将完全意识不到这些变化，也不会受到这些变化的影响。

```
use MyApp\HttpClient;

class ExternalService
{
    private $httpClient;

    public function __construct(HttpClient $httpClient)
    {
        $this->httpClient = $httpClient;
    }
}

// ...

$extService = new ExternalService(new GuzzleHttpClientAdapter(new Client())); 
```