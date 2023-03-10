# 用 Twig and Shoot 在 PHP 中延迟加载模板数据

> 原文：<https://dev.to/erikbooij/lazily-loading-template-data-in-php-with-twig-and-shoot-3gka>

*免责声明:这是我最初在个人博客*上分享的一篇文章的重写(新的和改进的)

如果您曾经使用过具有许多嵌套模板的相对较大的代码库，那么您可能会发现自己处于这样一种情况:要么沿着模板树向下传递变量，用许多只与深度嵌套模板相关的变量污染全局(模板)范围，要么寻找其他创造性的解决方案将数据注入这些子模板(例如用 Twig 扩展)。

在 [Coolblue](https://www.coolblue.nl) 我们正在使用 Twig 进行模板渲染，我们已经明确地发现自己处于那个位置。多年来，我们尝试了许多方法来解决这个问题，包括但不限于将完全预渲染的视图/模板注入到其他模板中。没有什么感觉是真正干净、可维护或面向未来的，但幸运的是，现在有一个解决方案，基本上解决了我们在这方面的所有问题。

[Shoot](https://github.com/shootphp/shoot) 是由 [Victor Welling](https://github.com/victorwelling) 开发的树枝扩展，他也是 Coolblue 的开发者。Shoot 允许你做的是将一个`presentation model`绑定到一个模板上。该模型定义并限制了呈现模板时范围内的变量。这些表示模型是非常普通的老式 PHP 对象，扩展了 Shoots `PresentationModel`，为每个变量包含一个受保护的字段。神奇之处在于，这些模型可以通过实现`HasPresenterInterface`来声明自己“有一个展示者”。演示者在运行时通过访问模型的(可选)依赖项和一个 [PSR-7 服务器请求](https://www.php-fig.org/psr/psr-7/#321-psrhttpmessageserverrequestinterface)来填充模型中的字段。基本上，这意味着模板现在可以获取自己的数据。

# 好处

与传统的模板和模板数据处理相比，使用 Shoot 的好处在于:

*   性能(仅当模板*实际上*被呈现时，数据才被检索)
*   更好的维护性
*   改进的可测试性
*   更多的隔离(不再有模板范围的泄漏)
*   增加模板重用

在 Shoot README 中，我们可以看到一个清晰的概述，说明如何根据渲染模板从预加载所有数据转变为“延迟加载”数据:

```
+---------------+          +---------------+
|    Request    |          |    Request    |
+-------+-------+          +-------+-------+
        |                          |     +---------+
        |                          |     |         |
+-------v-------+          +-------v-----v-+     +-+-------------+
|   Load data   |          |  Render view  +----->   Load data   |
+-------+-------+          +-------+-------+     +---------------+
        |                          |
        |                          |
+-------v-------+          +-------v-------+
|  Render view  |          |   Response    |
+-------+-------+          +---------------+
        |
        |
+-------v-------+
|   Response    |
+---------------+ 
```

# 如何使用拍摄

为了实际连接您的模板和一个表示模型，您只需将`{% model %}`标签添加到模板中，指向您的模型的完全限定类名:

```
{% model 'ShootDemo\\Presentation\\BlogPostModel' %}

<html lang="en">
<head>
    {% if post_exists %}{{ post_title }}{% else %}404 - Not Found{% endif %}
    <meta charset="UTF-8">
</head>
<body>
{% if post_exists %}
    <h1>{{ post_title }}</h1>
    {% for paragraph in post_content %}
        <p>{{ paragraph }}</p>
    {% endfor %}
{% else %}
    <h1>Post not found</h1>
    <p>
        This post could not be retrieved. Check out the other posts:
    </p>
{% endif %}

<a href="/?postId=1">Post 1</a> &nbsp; <a href="/?postId=2">Post 2</a>
</body>
</html> 
```

如你所见，我们指向下面的模型:`ShootDemo\Presentation\BlogPostModel`。该模板中使用了三个变量，这些变量都需要在模型中定义。

```
class BlogPostModel extends PresentationModel
{
    /** @var string[] */
    protected $post_content = '';

    /** @var bool */
    protected $post_exists = false;

    /** @var string */
    protected $post_title = '';
} 
```

这个模型现在动态地向模板提供带有这些值的变量，而不需要从父代传递它们。虽然这是一个静态模型，但帖子永远不会存在。我们可以基于当前的请求，通过让模型实现`HasPresenterInterface` :
，使它更上一层楼，变得动态

```
class BlogPostModel extends PresentationModel implements HasPresenterInterface
{
    /** @var string[] */
    protected $post_content = '';

    /** @var bool */
    protected $post_exists = false;

    /** @var string */
    protected $post_title = '';

    /**
     * @return string
     */
    public function getPresenterName(): string
    {
        return BlogPostPresenter::class;
    }
} 
```

该接口定义了一个方法`getPresenterName(): string`。该方法应该返回别名，通过该别名可以从符合 [PSR-11](https://www.php-fig.org/psr/psr-11/) 的容器中检索实际的呈现者。因为我们通过完全限定的类名注册我们的演示者，这也是我们在这里返回的。它提供了在 IDE 中轻松遍历的额外好处。演示者可能看起来像这样:

```
class BlogPostPresenter implements PresenterInterface
{
    /** @var BlogPostRepositoryInterface */
    private $blogPostRepository;

    /**
     * @param BlogPostRepositoryInterface $blogPostRepository
     */
    public function __construct(BlogPostRepositoryInterface $blogPostRepository)
    {
        $this->blogPostRepository = $blogPostRepository;
    }

    /**
     * @param ServerRequestInterface $request
     * @param PresentationModel      $presentationModel
     *
     * @return PresentationModel
     */
    public function present(ServerRequestInterface $request, PresentationModel $presentationModel): PresentationModel
    {
        $postId = (int)($request->getQueryParams()['postId'] ?? -1);

        try {
            $blogPost = $this->blogPostRepository->fetchBlogPost($postId);
        } catch (UnableToFetchBlogPostException $ex) {
            // Unable to load blog post return presentation model, explicitly setting post_exists to false
            return $presentationModel->withVariables([
                'post_exists' => false,
            ]);
        }

        $variables = [
            'post_content' => $blogPost->content(),
            'post_exists'  => true,
            'post_title'   => $blogPost->title(),
        ];

        return $presentationModel->withVariables($variables);
    }
} 
```

就是这样，您的模板现在会自动填充该请求的相关值。每当呈现模板时，它将加载其模型，从而自动调用呈现者来更新模型的属性。不要再试图确定你的模板在你的控制器/请求处理器中需要什么。

如您所见，通过在 DI 容器中配置，presenter 可以拥有自己的依赖项，如`$blogPostRepository`。它还可以在`->present()`方法中访问请求。它收到的第二个参数是我们刚刚定义的模型。现在，只要返回一个`PresentationModel`的实例，你就可以在这个方法中自由地编写任何你想要的逻辑。这里的一个常见模式是对传递给演示者的模型使用`->withVariables()`方法，通过传递一个值的关联数组。

请记住，这个示例仍然相当简单，从控制器向下传递值在这里可能并不困难，但是模板位于何处/有多深并不重要。这对于嵌套 20 层或跨不同页面重用的模板完全一样。

# 如何安装拍摄

也许开始使用 Shoot 最好的一点是，它只需要对您的设置进行很小的更改，因为您可以像当前一样简单地继续渲染模板。如果你没有在你的模板上定义一个模型，什么都不会改变，因此它在很大程度上是基于每个模板的选择加入。

可以通过 Composer 进行安装:

```
$ composer require shoot/shoot 
```

之后就是实例化 shot`Pipeline`并将其附加到 Twig:
的问题

```
$presenterMiddleware = new PresenterMiddleware($container);
$pipeline = new Pipeline([$presenterMiddleware]);
$installer = new Installer($pipeline);

// Add Shoot to Twig
$twig = $installer->install($twig); 
```

正如你所看到的，Shoot 有自己的中间件，最重要的是`PresenterMiddleware`，它支持我上面描述的行为。还有其他与 short 一起发布的 short 中间件，比如一个`SuppressionMiddleware`来抑制从模板中的`{% optional %}`标签中抛出的`RuntimeException`，一个`LoggingMiddleware`来记录哪些模板正在渲染，包括渲染需要多长时间，还有一个`InspectorMiddleware`来记录关于模板渲染的信息到你的浏览器控制台。所有这些都是可选的，尽管您可能想要添加`PresenterMiddleware`来启用我们正在讨论的惰性数据加载。

现在，您需要将 shots[PSR-15 中间件](https://www.php-fig.org/psr/psr-15/)添加到您的应用中间件堆栈中。这将授予 Shoot 对您的 PSR-7 请求的访问权限，它可以在演示者中使用该权限来动态填充演示模型。在下面的例子中，我使用的是 [Idealo 中间件栈](https://github.com/idealo/php-middleware-stack) :

```
$middlewareStack = new Stack(
    new EmptyResponse(),
    new ShootMiddleware($pipeline),
    $requestHandler
); 
```

现在，Shoot 已经完全设置好了，接下来只需要实际执行您的中间件堆栈并发出响应。

# 入门

我做了一个演示项目，你可以下载。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [特殊运算](https://github.com/ErikBooij) / [射击演示](https://github.com/ErikBooij/shootdemo)

### 一个微小的拍摄演示项目

<article class="markdown-body entry-content container-lg" itemprop="text">

# 拍摄演示

这是一个很小的 [Shoot](https://github.com/shootphp/shoot) 的演示实现，为我的博文《为[使用 shot 来延迟加载模板数据](https://dev.to/erikbooij/lazily-loading-template-data-in-php-with-twig-and-shoot-3gka)提供了一个参考实现。

## 安装

简单地克隆/下载，运行`composer install`，将你的网络服务器指向`public/index.php`(或者`composer run`，它将在端口 80 启动 PHP 的内置网络服务器)。

</article>

[View on GitHub](https://github.com/ErikBooij/shootdemo)

当您获得本地副本后，只需运行两个命令(第二个命令需要提升权限，因为它默认绑定到端口 80):

```
$ composer install
$ sudo composer run 
```

现在可以去 [http://localhost/？postId=1](http://localhost/?postId=1) 查看实际拍摄。

# 跟进

使用 Shoot 还可以做更多的事情，比如传递预先填充的模型，或者将数据从您的(父)模板反馈给演示者。如果有兴趣的话，我会在下一篇文章中讨论这个问题(请随意通过喜欢这篇文章或关注我来表达您的兴趣)。

如果你有意见，问题或者需要一些支持，我希望能在评论中看到你。干杯！