# ★缓存 Laravel 应用程序的整个响应

> 原文：<https://dev.to/freekmurze/caching-the-entire-response-of-a-laravel-app-2kan>

当一个请求进来时，你的应用程序将返回一个响应。为了创建响应，您的应用程序必须做一些工作。很可能会执行查询。这都需要一些时间。如果同样的请求进来，我们可以返回应用程序先前构建的响应，这不是很好吗？

这正是我们的包 [laravel-responsecache](https://github.com/spatie/laravel-responsecache) 能为你做的。它可以通过缓存整个响应来加快应用程序的速度。我们最近发布了[一个新的主要版本](https://github.com/spatie/laravel-responsecache/releases/tag/6.0.0)，它有一个新的很酷的特性。它现在可以缓存仍然有一些小的动态片段的页面，比如 csrf 令牌。

## 基本用法

在你[安装了](https://github.com/spatie/laravel-responsecache#installation)这个包之后(这可以通过一个简单的`composer require`来完成)，所有对你的应用程序的`GET`请求都会被默认缓存一周。当然，您可以自定义时间段。

事实上，您可以通过实现自己的自定义缓存配置文件来自定义整个缓存行为。这样的概要文件是一个实现了`CacheProfile`接口的类。它负责决定是否应该缓存请求/响应。这个界面看起来是这样的:

```
interface CacheProfile
{
    /*
     * Determine if the response cache middleware should be enabled.
     */
    public function enabled(Request $request): bool;

    /*
     * Determine if the given request should be cached.
     */
    public function shouldCacheRequest(Request $request): bool;

    /*
     * Determine if the given response should be cached.
     */
    public function shouldCacheResponse(Response $response): bool;

    /*
     * Return the time when the cache must be invalidated.
     */
    public function cacheRequestUntil(Request $request): DateTime;

    /**
     * Return a string to differentiate this request from others.
     *
     * For example: if you want a different cache per user you could return the id of
     * the logged in user.
     *
     * @param \Illuminate\Http\Request $request
     *
     * @return mixed
     */
    public function cacheNameSuffix(Request $request);
} 
```

默认情况下，这个包使用`CacheAllSuccessfulGetRequests`实现。它将缓存所有成功的`GET`请求一周。它还会注意只缓存基于文本的响应，比如 HTML 和 JSON。这是实现:

```
namespace Spatie\ResponseCache\CacheProfiles;

use Illuminate\Http\Request;
use Illuminate\Support\Str;
use Symfony\Component\HttpFoundation\Response;

class CacheAllSuccessfulGetRequests extends BaseCacheProfile
{
    public function shouldCacheRequest(Request $request): bool
    {
        if ($request->ajax()) {
            return false;
        }

        if ($this->isRunningInConsole()) {
            return false;
        }

        return $request->isMethod('get');
    }

    public function shouldCacheResponse(Response $response): bool
    {
        if (! $this->hasCacheableResponseCode($response)) {
            return false;
        }

        if (! $this->hasCacheableContentType($response)) {
            return false;
        }

        return true;
    }

    public function hasCacheableResponseCode(Response $response): bool
    {
        if ($response->isSuccessful()) {
            return true;
        }

        if ($response->isRedirection()) {
            return true;
        }

        return false;
    }

    public function hasCacheableContentType(Response $response)
    {
        $contentType = $response->headers->get('Content-Type', '');

        return Str::startsWith($contentType, 'text');
    }
} 
```

## 使用替代品

对于某些页面来说，缓存整个响应可能会有问题。假设您的页面包含一个表单。为了能够安全地提交它，我们需要一个新的 csrf 令牌。如果我们要缓存整个页面，它的 HTML 将包含一个旧的 csrf 令牌，当表单被提交时，服务器将不再接受它。

为了解决这个问题，新发布的版本 6 引入了对替换器的支持。replacer 是一个可以在响应被缓存之前替换一小部分响应的类。例如，它可以用占位符替换当前的 csrf 令牌。当请求第二次进入时，替换器还可以在响应被发送到浏览器之前修改缓存的响应。因此，它可以在那时用新的 csrf 令牌替换占位符。

默认情况下，我们的包附带了一个 csrf 令牌替换器。这是它看起来的样子。

```
namespace Spatie\ResponseCache\Replacers;

use Symfony\Component\HttpFoundation\Response;

class CsrfTokenReplacer implements Replacer
{
    protected $replacementString = '<csrf-token-here>';

    public function prepareResponseToCache(Response $response): void
    {
        if (! $response->getContent()) {
            return;
        }

        $response->setContent(str_replace(
            csrf_token(),
            $this->replacementString,
            $response->getContent()
        ));
    }

    public function replaceInCachedResponse(Response $response): void
    {
        if (! $response->getContent()) {
            return;
        }

        $response->setContent(str_replace(
            $this->replacementString,
            csrf_token(),
            $response->getContent()
        ));
    }
} 
```

有了这个，您现在甚至可以缓存包含表单的页面，并且仍然允许它们被安全地提交。

## 替代品

缓存响应有一些很好的替代方法。

Joseph Silber 创建了 [Laravel 页面缓存](https://github.com/JosephSilber/page-cache)，它可以将其缓存写入磁盘，并让 Nginx 读取它们。因为缓存结果时不再需要 PHP 来响应，所以性能优势非常显著。这是以稍微困难一点的安装过程为代价的(您必须修改 nginx 设置)，并且您的页面上不能有任何动态内容。

另一个值得一试的选择是 Barry Vd。Heuvel 的 [laravel-httpcache](https://github.com/barryvdh/laravel-httpcache) 。它允许你的应用程序利用 [HttpCache](https://symfony.com/doc/current/http_cache.html) 。

Varnish 是一个反向代理，可用于缓存内容。要使它工作，需要进行一些设置，但是一旦你完成了这些，你就会有一个非常好的解决方案。我以前用过它，并在这篇博文中写下了我的使用体验[。剧透:我能够让一个简单的服务器每秒处理 6 000 个请求。](https://freek.dev/using-varnish-on-a-laravel-forge-provisioned-server)

## 在关闭

我们的软件包不应该掩盖性能问题。所有的应用程序都应该被优化，这样它们就可以在一个可接受的时间范围内做出响应，而不需要使用响应缓存。请记住，在尝试提供快速体验时，还有许多其他方面需要考虑。

尽管有许多好的(和更快的)选择，我相信 laravel-responsecache 是最容易开始响应缓存的包。

如果你喜欢这个包，请看看我们团队之前发布的包列表。