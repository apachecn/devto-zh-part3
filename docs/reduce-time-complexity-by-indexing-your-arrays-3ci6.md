# 通过索引您的阵列降低时间复杂性

> 原文：<https://dev.to/anwar_nairi/reduce-time-complexity-by-indexing-your-arrays-3ci6>

# 数据库中的索引

索引，也许这是一个你在构建或使用数据库时已经听说过的术语。

> 放索引，否则你的请求会花费太多时间。

好的，这有效，我的主键设置好了。当我用另一个表列连接我的主键时，我的请求会更快。但是为什么呢？

```
Publisher

id    name        age
=====================
1     John        38
2     Elizabeth   24
3     Mike        17 
```

Enter fullscreen mode Exit fullscreen mode

我们有出版商，他们在网络应用程序上发布一些文章。

```
Article

id    title                                createdAt    publisherId
===================================================================
1     Unit tests for your next library     2019-01-26   2
2     WebP: when performance matter        2019-02-25   2
3     Indexes: why would I need it         2019-03-09   3 
```

Enter fullscreen mode Exit fullscreen mode

所以你会加入他们来获得每个出版商的职位:

```
SELECT
  publisher.name,
  article.title,
  article.createdAt
FROM
  publisher
JOIN
  article on article.publisherId = publisher.id 
```

Enter fullscreen mode Exit fullscreen mode

有了预期的结果:

```
name        title                            createdAt
Elizabeth   Unit tests for your next library 2019-01-26
ELizabeth   WebP: when performance matter    2019-02-25
Mike        Indexes: why would I need it     2019-03-09 
```

Enter fullscreen mode Exit fullscreen mode

到此为止，没什么不对。现在，想象我们有 50 个出版商和 1000 篇文章。

为了让数据库找出哪些文章属于哪个出版商，它获取你的文章`publisherId`，然后，对于这些文章中的每一篇，将对每个出版商进行循环，直到找到与 id 匹配的出版商，并提取所需的数据。

```
article.publisherId    publisher.id
===================================
59                     1 ? No
59                     2 ? No
59                     3 ? No
...
59                     59 ? Yes -> pulls out related data

32                     1 ? No
32                     2 ? No
32                     3 ? No
...            
32                     32 ? Yes -> pulls out related data

11                     1 ? No
11                     2 ? No
11                     3 ? No
...
11                     11 ? Yes -> pulls out related data
... 
```

Enter fullscreen mode Exit fullscreen mode

在最坏的情况下，让我们假设只有 50 号出版商发布了 1000 篇文章。这意味着对于每个帖子，n 个帖子，我们将循环每个发布者，n 个发布者，来找到关系。这是一个 n×n 的复杂度。我们假设请求每轮需要 0.5 毫秒(500 秒)。

> (0.5 毫秒* 1000 篇文章)* (0.5 毫秒* 50 个发布者)
> 500 毫秒* 25 毫秒
> 12 500 毫秒
> 12.5 秒

**当前查询复杂度** : O(n)

为了解决这个问题，数据库引擎支持主键索引，以帮助引擎快速找出哪些记录属于哪个标识符。因此，数据库引擎将*索引*您的行，而不是以数组的形式存储您的记录，以这种方式(使用 JSON 符号来表示，而不是数据库如何真正存储数据):

```
Publisher

{
  1: {
    "name:": "John",
    "age": 38
  },
  2: {
    "name": "Elizabeth",
    "age": 24
  },
  3: {
    "name": "Mike",
    "age": 17
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这使得数据库能够直接“攻击”索引 id，而不是遍历每个记录，直到找到正确的键！

所以如果你想要 id 为 50 的出版商名称，你可以在第一轮 :
中**完成**

```
const name = Publisher[50].name; 
```

Enter fullscreen mode Exit fullscreen mode

回到我们由 50 个出版商制作的 1000 篇文章，我们现在可以直接获得出版商(不需要为他们每个人循环)，所以只有 1000 轮。我们的数据库仍然需要每轮 0.5 毫秒(500 秒)。

> (0.5 毫秒* 1000 篇文章)* (1 毫秒* 1 个发布者)
> 500 毫秒* 1 毫秒
> 500 毫秒
> 0.5 秒

**当前查询复杂度** : O(n)

我们已经从 12 秒到将近半秒。没那么糟！

## 索引一个数组

索引数组是完全相同的过程。想象一下，我们正在拉取路由(URL)及其相关内容，就像在 CMS 中一样。

```
use App\Article;

$articles = Article::all();

print_r($articles); 
```

Enter fullscreen mode Exit fullscreen mode

导致控制台:

```
[
  [
    id" => 1, 
    "url" => "/advantages-of-indexing-arrays", 
    "content" => "..."
  ],
  [
    id" => 2, 
    "url" => "/webp-is-the-new-fast", 
    "content" => 
    "..."
  ],
  [
    id" => 3, 
    "url" => "/vue-js-async-components", 
    "content" => "..."
  ],
  ...
] 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在用户正在请求“/7-tips-to-be-more-productive-on-laravel-5”路线，您的数据库包含 1000 篇文章。

```
use App\Article;
use App\Request;
use App\Response;

class ArticleController {
  // Shows the desired article content to the client
  public function show(Request $request): Response {
    $articles = Article::all();
    $url = $request->url();

    foreach ($articles as $article) {
      if ($article->url === $url) {
        // found it, pull content and display the view

        break;
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在该配置中，控制器将循环查找每篇文章，当找到正确的文章时，显示内容。在最坏的情况下，1000 篇文章，用户请求最后一篇文章，循环 1000 次。

每轮 1 毫秒，我们得到:

> 1000 篇文章* 1 毫秒
> 1000 毫秒
> 1 秒

**当前控制器复杂度** : O(n)

O(n)，这意味着 n 篇文章(我们事先不知道我们有多少篇文章)。

将结果发送到客户端浏览器需要 1 秒，浏览器呈现页面可能需要更多时间。我们可以通过索引这个数组做得更好。

```
use App\Article;
use App\Request;
use App\Response;

class ArticleController {
  // Shows the desired article content to the client
  public function show(Request $request): Response {
    $articles = $this->articles();
    $url = $request->url();

    if (isset($articles[$url])) {
      // found it in one try!
    }
  }

  // The indexing happens here
  protected function articles(): array {
    $articles = Article::all();
    $indexedArticles = [];

    foreach ($articles as $article) {
      $indexArticles[$article->url] = $article;
    }

    return $indexArticles;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以直接“攻击”数组，并获得文章数据。 [`isset()`](http://php.net/manual/en/function.isset.php) 如果键在数组中则返回 true，否则返回 false。它还直接以键为目标，所以它不会循环整个数组。

> 1 条路线* 1 毫秒
> 1 毫秒

**电流控制器复杂度** : O(1)

O(1)，因为无论我们在数据库中有多少文章，它要么会被立即找到，要么不会。

## 提醒道

这个例子是部分正确的，因为我没有考虑获取文章的复杂性(可能我的文章类为每一行执行额外的处理，所以后端可能花费更多的时间)。

此外，您肯定不希望在每个服务器请求时索引您的数组**。您可能会使用 Redis 或在一个文件中缓存它(例如以 JSON 的形式)。因为最终，索引是一个需要遍历数组的过程。**

## 结论

我希望这篇文章能让你对索引有更多的了解，并且在使用它们的时候会让我们的请求更快。

当您的第一个循环需要再次循环并找到一个元素时，这种技术非常有用(就像数据库需要进行连接一样)。

如果您需要根据几个标准(如列值)进行连接，这是相同的过程。

```
{
  "GET": {
    "/": { "controller": "HomeController@index" },
    "/newsletter": { "controller": "NewsletterController@index" }
  },
  "POST" => {
    "/newsletter": { "controller": "NewsletterController@store" }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过许多标准攻击该对象(在本例中，有两个标准，url 和协议):

```
const routes = []; // filled by the array above
const protocol = "POST";
const url = "/newsletter";

const controller = routes[protocol][url].controller; 
```

Enter fullscreen mode Exit fullscreen mode

快乐的优化！

照片由[rawpixel.com](https://www.pexels.com/@rawpixel?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从[派克斯](https://www.pexels.com/)拍摄