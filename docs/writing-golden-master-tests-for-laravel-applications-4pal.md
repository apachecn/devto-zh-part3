# 为 Laravel 应用程序编写 golden master 测试

> 原文：<https://dev.to/matthewbdaly/writing-golden-master-tests-for-laravel-applications-4pal>

去年我写了一篇文章说明如何为 PHP 应用程序编写金主测试。这种方法可行，但是有一些问题:

*   因为它使用 Goutte 这样的无头浏览器，所以不可避免地会很慢(我为之编写测试的传统应用程序的典型测试运行时间是 3-4 分钟)
*   它不允许不同的内容，所以对内容的任何改变都会破坏测试

这些因素限制了它在许多 PHP 应用程序中的实用性。然而，对于 Laravel 应用程序来说，您处于一个更好的位置:

*   您可以使用 Browserkit，而不是无头浏览器，这样可以大大加快响应速度
*   您可以建立一个测试数据库，每次都用相同的数据填充它，确保唯一可以改变的是如何处理数据来创建所需的 HTML

在这里，我将向您展示如何采用这种方法来处理 Laravel 应用程序。

我们依靠 Browserkit 测试这种方法，所以你需要安装:

```
$ composer require --dev laravel/browser-kit-testing 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要创建我们的基础 golden master 测试用例:

```
<?php

namespace Tests;

use Tests\BrowserTestCase;

class GoldenMasterTestCase extends BrowserTestCase
{
    use CreatesApplication;

    public $baseUrl = 'http://localhost';

    protected $snapshotDir = "tests/snapshots/";

    protected $response;

    protected $path;

    public function goto($path)
    {
        $this->path = $path;
        $this->response = $this->call('GET', $path);
        $this->assertNotEquals(404, $this->response->status());
        return $this;
    }

    public function saveHtml()
    {
        if (!$this->snapshotExists()) {
            $this->saveSnapshot();
        }
        return $this;
    }

    public function assertSnapshotsMatch()
    {
        $path = $this->getPath();
        $newHtml = $this->processHtml($this->getHtml());
        $oldHtml = $this->getOldHtml();
        $diff = "";
        if (function_exists('xdiff_string_diff')) {
            $diff = xdiff_string_diff($oldHtml, $newHtml);
        }
        $message = "The path $path does not match the snapshot\n$diff";
        self::assertThat($newHtml == $oldHtml, self::isTrue(), $message);
    }

    protected function getHtml()
    {
        return $this->response->getContent();
    }

    protected function getPath()
    {
        return $this->path;
    }

    protected function getEscapedPath()
    {
        return $this->snapshotDir.str_replace('/', '_', $this->getPath()).'.snap';
    }

    protected function snapshotExists()
    {
        return file_exists($this->getEscapedPath());
    }

    protected function processHtml($html)
    {
        return preg_replace('/(<input type="hidden"[^>]+\>|<meta name="csrf-token" content="([a-zA-Z0-9]+)">)/i', '', $html);
    }

    protected function saveSnapshot()
    {
        $html = $this->processHtml($this->getHtml());
        file_put_contents($this->getEscapedPath(), $html);
    }

    protected function getOldHtml()
    {
        return file_get_contents($this->getEscapedPath());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`goto()`方法设置对象的当前路径，然后获取页面。它验证是否找到了该页，然后返回该对象的实例，以允许方法链接。

另一个值得注意的方法是`saveHtml()`方法。这将检查快照是否存在，如果不存在，则保存它。快照本质上只是从该路径返回的 HTML，但是某些内容可能需要被去掉，这是在`processHtml()`方法中完成的。在本例中，我们去掉了隐藏字段和 CSRF 令牌元标记，因为 CSRF 令牌每次都会重新生成，并且会破坏快照。

我们要看的最后一个方法是`assertSnapshotsMatch()`方法。这将获得当前的 HTML，以及该路由的任何快照的 HTML，然后比较它们。如果它们不一致，断言就会失败。此外，如果`xdiff_string_diff`可用，它将显示两个文件的差异——注意，这些文件有时会很大，但对调试很有帮助。

另外，请注意我们的快照目录- `tests/snapshots`。如果你做了一个突破性的改变，想要删除一个快照，那么你可以在那里找到它——这种格式用下划线代替了正斜杠，并附加了一个文件扩展名`.snap`,但是你可以根据自己的需要随意定制。

接下来，我们将在`tests/GoldenMaster/ExampleTest.php` :
为不需要认证的路由创建一个测试

```
<?php

namespace Tests\GoldenMaster;

use Tests\GoldenMasterTestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;
use App\User;

class ExampleTest extends GoldenMasterTestCase
{
    use RefreshDatabase;

    /**
     * @dataProvider nonAuthDataProvider
     */
    public function testNonAuthPages($data)
    {
        $this->goto($data)
            ->saveHtml()
            ->assertSnapshotsMatch();
    }

    public function nonAuthDataProvider()
    {
        return [
            ['/register'],
            ['/login'],
        ];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意数据提供程序的使用。我们希望能够遍历路线列表，并依次验证每条路线，因此将数据提供者方法设置为`nonAuthDataProvider()`是有意义的，它将返回路线数组。如果你以前没有使用过数据提供者，当你需要用不同的数据一遍又一遍地测试同样的事情时，它们是减少测试中样板文件的一个简单方法，你可以在这里了解更多[。](https://tighten.co/blog/tidying-up-your-phpunit-tests-with-data-providers)

现在，看到了使用的方法，应该很容易理解`testNonAuthPages()`。它经过以下步骤:

*   参观所经过的路线，如`/register`
*   将 HTML 保存到快照(如果尚未保存)
*   断言当前内容与快照匹配

使用这种方法，您可以很容易地测试许多路由的意外变化。如果您已经在 Jest 中使用了快照测试，这是一种类似的方法。

## 认证路线

这对于认证路由不太适用，所以还需要做一些修改。您会得到一个响应，但是如果您查看 HTML，它会清楚地显示用户被重定向到所有这些页面，所以测试它们没有多大意义。

如果您的内容在用户之间没有区别，您可以向您的测试添加特性`Illuminate\Foundation\Testing\WithoutMiddleware`来禁用认证，并允许测试在不被重定向的情况下获取内容。

但是，如果您的内容在用户之间有所不同，您需要创建一个用户对象，并使用 Laravel 测试中已经可用的`actingAs()`方法来设置用户，如下所示:

```
<?php

namespace Tests\GoldenMaster;

use Tests\GoldenMasterTestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;
use App\User;

class ExampleTest extends GoldenMasterTestCase
{
    use RefreshDatabase;

    /**
     * @dataProvider authDataProvider
     */
    public function testAuthPages($data)
    {
        $user = factory(User::class)->create([
            'email' => 'eric@example.com',
            'name' => 'Eric Smith',
            'password' => 'password'
        ]);
        $this->actingAs($user)
            ->goto($data)
            ->saveHtml()
            ->assertSnapshotsMatch();
    }

    public function authDataProvider()
    {
        return [
            ['/'],
        ];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将允许我们以用户身份访问特定页面，而无需被重定向。

## 总结

这可能是一种捕捉应用程序中意外中断的有用技术，特别是那些很少或没有常规测试覆盖的应用程序。虽然我是在 Zend 1 遗留代码基础上开发这项技术的，但是利用 Laravel 中可用的工具使这项技术变得更快、更有用。如果您现有的 Laravel 应用程序没有像您希望的那样得到很好的测试，并且您进行了一些实质性的更改，可能会破坏一些功能，那么设置这种 golden master 测试是尽快发现任何问题的一种快速而简单的方法。