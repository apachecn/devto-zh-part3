# 介绍浓缩咖啡

> 原文：<https://dev.to/mnavarrocarter/introducing-espresso-4p14>

当我开始使用 Node 时，我爱上了很多东西。首先是模块系统:一种相当聪明的方法，可以导出状态和行为，同时保持封装，而不用担心是否有容器 DI。但是，我最喜欢的一件事也是 Express.js 的灵活性和强大功能，它是 Node 的 antonokia http 层。

我真的很喜欢它，所以我很想用在 PHP 上。PHP 确实有一些类似的微框架，如 expressive 和 Slim，但其中还有一些东西我并不完全喜欢。

所以我决定制作 Espresso，一个 PHP express . js 的克隆。目前为版本`1.0.0-alpha1`。这意味着 api 已经定义好并正常工作，但缺少的是大量的单元测试，然后我才感到有信心用生产标签释放它。

这篇文章的想法是双重的。第一个是提高对项目的认识，并通过测试、玩游戏、查找错误和提供“拉入”请求来寻找愿意为项目做出贡献的人。第二，向潜在用户展示如何使用。

## 创建 espresso 实例

Espresso 是可互操作的。在 PHP 世界中，这意味着它遵循 PHP-FIG 定义的 HTTP 标准。因此，要使用它，最低要求是要有 PSR-7 实施和 PSR-7 响应功能。

我个人推荐安装神奇的`zendframework/zend-diactoros`和`http-interop/response-sender`。

然后，要安装 espresso 和这两个提示，请将此命令复制到您的项目文件夹中，然后可以开始:

```
composer require espresso/app dev-master zendframework/zend-diactoros http-interop/response-sender 
```

> 注:`dev-master`表示只在 alpha 阶段需要。

创建 espresso 实例就像:
一样简单

```
// No te olvides de requerir el autoload de composer antes!
$app = new Espresso(
    [\Zend\Diactoros\ServerRequestFactory::class, 'fromGlobals'],
    '\Http\Response\send'
); 
```

这两个参数使 espresso 能够调用其方法`run`。该方法所做的是，利用 callable 创建“`ServerRequestInterface`”实例，从中间件管道中获取“`ResponseInterface`”实例，然后使用提供的 callable 发出该响应。

构建 espresso 实例后，很容易注入中间件:

```
$app->use(new LoggingMiddleware()); 
```

## 记录特快路线

为了记录特快路线，你有六种方法可供选择，它们相当经典:`get`、`post`、`put`、`patch`、`delete`和`any`。以上每种方法均采用路径中的*和`MiddlewareInterface`的任意数量的实例。* 

```
// Este handler sirve la página de inicio
$app->get('/', new HomePageHandler());
// Este handler sirve el panel de administración, pero primero se
// asegura que el usuario esté autenticado.
$app->get('/admin', new BasicAuthMiddleware(), new AdminPanelHandler()); 
```

当然，你可以将*胎盘*移至你所定义的路线。这些将注入给命名占位符:
下的`ServerRequestInterface`属性

```
$app->get('/users/:id', function ($req) {
    $id = $req->getAttribute('id'); // Esto devuelve el valor de id.
}); 
```

> 注意:此外，也可以通过静音或“`RequestHandlerMiddleware`”实例。

与 PHP 中的其他微框架不同，espresso 没有由*路由收集器*和*调度程序*组成的传统路由系统。相反，每条路径本身都是中间件，并接收到一个“`ServerRequestInterface`”实例，从中获取信息以查看是否存在匹配。

如果我们直接在 espresso 实例中定义应用程序的每个路径，这可能会显得效率低下。但是，Expo 具有 Express.js 以来采用的另一个概念:能够在路径下嵌入可路由组件。例如，让我们想想一个用户模块:

```
$userModule = new RoutableHttpComponent();

// Definimos algunas rutas relativas a este módulo
$userModule->get('/', function() {
    // Devolver una lista de usuarios aquí
});
$userModule->post('/', function() {
    // Crear un usuario aquí
});
$userModule->get('/:id', function() {
    // Devolver un usuario específico aquí
});

// Y luego las importamos en la aplicación bajo un path en común
$app->use('/users', $userModule); 
```

事实上，espresso 只不过是拥有`run`方法和一些配置方法的这种`RoutableHttpComponent`的延伸。你可以只用`RoutableHttpComponent`做你自己的应用程序。

## 表达及依赖注入

如果使用实现 PSR-11 定义的接口的依赖注入容器，则可以让 espresso 使用它来解决传递给上述方法的任何问题。

这可以用
的浓缩方法来实现:t1【中文字幕 ocr:贾宝玉字幕校对:贾宝玉字幕校对:贾宝玉字幕校对:贾宝玉字幕校对:贾宝玉字幕校对:贾宝玉字幕校对:贾宝玉字幕校对:贾宝玉字幕校对:贾宝玉字幕校对:贾宝玉字幕校对:贾宝玉

```
$app->setContainer($aContainerInstance); 
```

然后，您可以只通过引用服务名来定义路径和注入中间件:

```
$app->get('/', 'app.homepage_handler'); 
```

在内部，espresso 将解析此服务，并以默认的方式将其实例化为“*lazy”*。

每个组件也可以接收容器的一个实例。

```
$module = new RoutableHttpComponent();
$module->setContainer($aContainerInstance); 
```

## 其他用途

express instance 有几个公用程式，可让您的生活更轻松。我正在考虑再补充许多，如果能得到反馈将是很好的。

### 会话

第一个实用程序是通过组成会话的 cookies 仅标识客户端的功能。我们可以通过调用方法`$app->session();`来做到这一点。此方法有三个参数，只有第一个参数是必需的:

1.  一个 32 位的*密匙*。可在终端上用:`php -r "echo bin2hex(random_bytes(16));"`生成
2.  会话 cookie 的名称。默认设置为“`_espresso`”。
3.  会话超时为字符串“`DateTime`”。默认为“`+1week`”。

请注意，espresso 会话处理不使用 PHP 本机处理程序，而是加密将会话数据存储在客户端 cookie 中的字符串`json`。任何修改都会立即检测到，这将恢复会话并使任何先前的验证失效。如果会话被修改，cookie 将重新设置。

这种方法的优点在于其简便性、有效性、安全性和可扩展性。缺点是您不能在会话中放置太多数据，因为 cookies 的容量相当有限。

### 身体解析器

如果您正在构建 rest API 或处理表单输入，espresso 提供了一种快速设置“*解析“*”请求“*”正文的方法。只要调用方法`$app->parseBody()`并传递一个字符串，无论是`json`还是`form`(或两者兼选)，您的输入就自动作为方法`ServerRequestInterface::getParsedBody()`中的一个安排可用。* 

```
$app->parseBody('json'); 
```

## 【你可以贡献！】

还有几件事我想加进浓咖啡里，我很期待听到想法！如果你有什么想提出的特征，可以在[github 存储库](https://github.com/espresso-php/app)开一个问题，或者可以安装[composer 包](https://packagist.org/packages/espresso/app)并稍微发挥一下框架。

当你这样做的时候，我会写单元测试，并增加一些额外的功能。一旦获得 90%的复盖率(大概不到两周)，我将发布版本`1.0.0-rc1`。6 月中旬，我希望能够进行`1.0.0`的发射。*