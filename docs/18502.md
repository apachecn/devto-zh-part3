# ★手工模拟

> 原文：<https://dev.to/freekmurze/handcrafting-mocks-2bl0>

在我开发的一个应用程序中，我想实现自动推文。当然，这个逻辑也是要检验的。在这篇博文中，我想向你展示如何轻松手工制作你自己的模型。

## 设置事物

让我们先来看看如何用 PHP 发 tweet。在这个例子中，我们将使用 Laravel。在那个框架中，在服务提供商中设置东西是很常见的。为了进行认证，我们将使用[流行的`abraham/twitteroauth`包](https://packagist.org/packages/abraham/twitteroauth)。在下面的服务提供者中，我们确保每当我们解析一个`App\Services\Twitter`的实例时(我们很快就会看到)。它通过构造函数获得一个已配置的`TwitterOAuth`。

```
namespace App\Services\Twitter;

use Abraham\TwitterOAuth\TwitterOAuth;
use Illuminate\Support\ServiceProvider;

class TwitterServiceProvider extends ServiceProvider
{
    public function register()
    {
        $this->app->bind(Twitter::class, function () {
            $connection = new TwitterOAuth(
                config('services.twitter.consumer_key'),
                config('services.twitter.consumer_secret'),
                config('services.twitter.access_token'),
                config('services.twitter.access_token_secret')
            );

            return new Twitter($connection);
        });
    }
} 
```

让我们看看实际的 Twitter 类。我们只需调用`statuses/update`端点就可以实际发布一些东西。

```
namespace App\Services\Twitter;

use Abraham\TwitterOAuth\TwitterOAuth;

class Twitter
{
    /** @var \Abraham\TwitterOAuth\TwitterOAuth */
    protected $twitter;

    public function __construct(TwitterOAuth $twitter)
    {
        $this->twitter = $twitter;
    }

    public function tweet(string $status)
    {
        return $this->twitter->post('statuses/update', ['status' => $status]);
    }
} 
```

想象一下，当一篇博客文章发表后，你想在推特上发布一些东西。有多种方法来编码这种行为。在下面的例子中，如果选择在博客文章发表后触发一个事件。

```
use Illuminate\Database\Eloquent\Model;

class BlogPost extends Model {

  // ...

   public function publish()
   {
      $this->published_at = now();
      $this->save();

      event(new BlogPostPublished($this));

      return $this;
   }

   public function tweetText(): string
   {
       return "Here's my new blogpost: {$this->url}";
   }
} 
```

在我们的应用程序中，我们已经[设置了一个事件监听器](https://laravel.com/docs/5.7/events)，它将在`BlogPostPublished`事件被触发时发送推文。我不打算在这篇博文中包含这段代码，因为它与我们将要执行的测试无关。

## 如何测试推文是否发出？

在我们的测试中，我们希望确保在发布内容时，tweet 确实被发送了。当然，我们不希望在运行测试时真的发 tweet。这会让我们的测试变慢，让我们的追随者发疯。

```
namespace Tests\Models;

use App\Models\Event;
use Tests\TestCase;

class BlogPostTest extends TestCase
{
    /** @test */
    public function it_will_send_a_tweet_when_publishing_a_post()
    {
        $blogPost = factory(BlogPost::class)->create();

        $blogPost->publish();

        // how to assert the tweet was sent?
    }
} 
```

现在你的第一个想法可能是使用 PHPUnit 内置的嘲弄解决方案或[嘲弄](https://github.com/mockery/mockery)。虽然这些选项完全有效，但我不喜欢添加特定断言在大多数情况下可读性不强的事实。

你也可以用门面来嘲笑 Twitter。这种技术在[亚当·瓦森的试驾拉腊维尔赛道](https://course.testdrivenlaravel.com/)上演示过。这个想法是你用你控制的另一个实现替换掉实际的`Twitter`类。这种交换是通过外观完成的。虽然这也是一种有效的方法，但是您需要做一些设置来使其工作。

## 手工制作仿制品

对于这个小应用程序，我希望测试尽可能的简单。事实证明，创建自己的模拟非常简单。您可以扩展原始类并用自己的实现替换方法。

实现这一点的技巧是在其中添加一个空的构造函数。这个空构造函数将允许创建这个类，而不必传递基类所需的`TwitterOAuth`。

```
namespace Tests\Mocks;

use PHPUnit\Framework\TestCase;

class Twitter extends \App\Services\Twitter\Twitter
{
    /** @var array */
    protected $sentTweets = [];

    /*
     * This avoids having to pass the constructor parameters defined in the base class
     */
    public function __construct()
    {
    }

    public function tweet(string $status)
    {
        $this->sentTweets[] = $status;
    }

    public function assertTweetSent(string $status)
    {
        TestCase::assertContains($status, $this->sentTweets, "Tweet `{$status}` was not sent");
    }
} 
```

有了这个手工制作的模拟，测试 tweet 是否被发送变得非常容易。

```
namespace Tests\Models;

use App\Models\BlogPost;
use Tests\TestCase;
use App\Services\Twitter\Twitter;
use Tests\Mocks\Twitter as TwitterMock;

class BlogPostTest extends TestCase
{
    /** @test */
    public function it_will_send_a_tweet_when_publishing_a_post()
    {
        $twitter = $this->app->bind(Twitter::class, function () {
            return new TwitterMock();
        });

        $blogPost = factory(BlogPost::class)->create();

        $blogPost->publish();

        $twitter->assertTweetSent($blogPost->tweetText());
    }
} 
```

如果您想在其他测试中模仿 Twitter，您可以将绑定逻辑移动到基类`TestCase`的`fakeTwitter`方法中。

```
namespace Tests;

use App\Services\Twitter\Twitter;

use Illuminate\Foundation\Testing\TestCase as BaseTestCase;
use Tests\Mocks\Twitter as TwitterMock;

abstract class TestCase extends BaseTestCase
{
    // …

    protected function fakeTwitter(): TwitterMock
    {
        $twitter = $this->app->bind(Twitter::class, function() {
           return new TwitterMock();
        });   

        return $twitter;
    }
} 
```

通过使用 Laravel 的固有特性，您可以更好地解决这个问题。它包含一个在容器中轻松交换实现的方法。

```
namespace Tests;

use App\Services\Twitter\Twitter;

use Illuminate\Foundation\Testing\TestCase as BaseTestCase;
use Tests\Mocks\Twitter as TwitterMock;
use Illuminate\Foundation\Testing\Concerns\InteractsWithContainer;

abstract class TestCase extends BaseTestCase
{
    use InteractsWithContainer;

    // ...

    protected function fakeTwitter(): TwitterMock
    {
        return $this->swap(Twitter::class, new TwitterMock());
    }
} 
```

有了这个`fakeTwitter`方法，您的测试可以被重构为:

```
namespace Tests\Models;

use App\Models\BlogPost;
use Tests\TestCase;
use App\Services\Twitter\Twitter;
use Tests\Mocks\Twitter as TwitterMock;

class BlogPostTest extends TestCase
{
    /** @var \Tests\Mocks\Twitter */
    protected $twitter;

    public function setUp()
    {
        parent::setUp();

        $this->twitter = $this->fakeTwitter();
    }

    /** @test */
    public function it_will_send_a_tweet_when_publishing_a_post()
    {
        $blogPost = factory(BlogPost::class)->create();

        $blogPost->publish();

        $this->twitter->assertTweetSent($blogPost->tweetText);
    }

    // moarrr tests
} 
```

如果您在其他测试中需要额外的断言，很容易将它们添加到您的模拟中。

```
public function assertTweetsSentCount(int $count)
{
    TestCase::assertCount($count, $this->tweets);
} 
```

## 使用接口

在上面的例子中，`Twitter`类以最简单的方式被模仿:通过扩展类。如果你愿意，你也可以使用一个接口。

首先，创建一个包含`tweet`方法接口。

```
namespace App\Services\Twitter\Contracts;

interface Twitter 
{
    public function tweet(string $status)
} 
```

您应该让实际的`Twitter`类实现该接口。

```
namespace App\Services\Twitter;

use App\Services\Twitter\Contracts\Twitter as TwitterInterface;

class Twitter implements TwitterInterface
{
   // ...
} 
```

您的 mock 不应该再扩展`Twitter`类，而是实现接口。您不再需要添加那个空的构造函数。

```
namespace Tests\Mocks;

use App\Services\Twitter\Contracts\Twitter as TwitterInterface;

class Twitter implements TwitterInterface
{
   // ...
} 
```

在服务提供者中，您应该使用该接口来绑定具体的类。

```
namespace App\Services\Twitter;

use Illuminate\Support\ServiceProvider;
use App\Services\Twitter\Contracts\Twitter as TwitterInterface;

class TwitterServiceProvider extends ServiceProvider
{
    public function register()
    {
        $this->app->bind(TwitterInterface::class, function () {
            $connection = // ...
            return new Twitter($connection);
        });
    }
} 
```

当然，您也应该在测试中使用`fakeTwitter`函数中的接口。

```
namespace Tests;

use App\Services\Twitter\Twitter;

use Illuminate\Foundation\Testing\TestCase as BaseTestCase;
use Tests\Mocks\Twitter as TwitterMock;
use App\Services\Twitter\Contracts\Twitter;

abstract class TestCase extends BaseTestCase
{
    // ...

    protected function fakeTwitter(): TwitterMock
    {
        return $this->swap(Twitter::class, new TwitterMock());
    }
} 
```

使用接口的最大好处是，实际的 Twitter 类和被模仿的类保证有正确的方法。

你是否应该使用这个取决于你。你喜欢仅仅扩展一个类的简单性吗？这样做，但是要注意你应该保持你的类和 mock 手动同步。

## 总之

当然，这只是测试这类代码的众多可能性之一。想用这个界面，太好了。想用常规的 mock，那也不错(好处:不用维护自己定制的 mock 类)。

你对此有什么想法？请在下面的评论中告诉我！

非常感谢[弗雷德里克·范布拉班特](https://twitter.com/maybefrederick)和[布伦特·鲁斯](https://twitter.com/brendt_gd)审阅这篇文章。