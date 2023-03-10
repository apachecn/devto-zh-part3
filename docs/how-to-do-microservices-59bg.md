# 微服务怎么做？

> 原文：<https://dev.to/alejandro/how-to-do-microservices-59bg>

我对微服务架构和 MVC 框架上的实现有很多概念上的疑问，在这个例子中是 Laravel。

假设我有一个博客平台，上面有给定的 ER 数据库。

[![database structure](img/1ff7641c1895e327de3e9b9b947eb2b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aLN7Bqak--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yu8iizosm4uipr6apbsv.png)

而我想把这个系统改成微服务架构。根据我所读到的，我可以这样说:

[![microservices architecture](img/8a1ae1881c7749f43c8b688d7fe63705.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MxedsKXD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xbbxv0cap3tz5th1ynkn.png)

在这种情况下，每个微服务都是一个 Laravel 安装(也可以是 Node.js，.网络核心等)。

现在，我的问题是如何访问每个服务，例如，假设博客应用程序是一个 Laravel 应用程序，用户进入[http://blog.test/1](http://blog.test/1)，因此用户希望看到 id 为 1 的帖子。

控制器应该如何工作？

据我所知，控制器可能是这样的:

```
<?php
namespace App\Http\Controllers;

use Illuminate\Routing\Controller as BaseController;
use Illuminate\Http\Request;

class BlogController extends BaseController
{
    public function show(Request $request, $id) {
        $client = new \GuzzleHttp\Client();

        // Get the blog information
        $blogResponse = $client->request('GET', "http://post-api.blog.test/api/blog/{$id}");
        $blogString = $blogResponse->getBody();
        $blog = json_decode($blogString);

        // Get the comments information
        $commentsResponse = $client->request('GET', "http://comments-api.blog.test/api/comments/{$id}");
        $commentsString = $commentsResponse->getBody();
        $comments = json_decode($commentsString);

        // Get the the author information
        $userResponse = $client->request('GET', "http://users-api.blog.test/api/user/{$blog['users_id']}");
        $userString = $userResponse->getBody();
        $user = json_decode($userString);

        return view('blog.show', ['blog' => $blog, 
            'comments' => $comments, 
            'author' => $user]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我的问题是:

*   这是正确的实现方式吗？
*   我知道我可以缓存一些响应，以便更快地访问资源，但是 Http APIs 是实现它的一种方式吗？还是有另一种我没有看到的技术？

我知道你可能不需要为博客做微服务，但这只是一个如何实现这种模式的例子。

谢谢！