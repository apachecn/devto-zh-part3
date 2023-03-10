# ★介绍 laravel-websockets，一个用 PHP 实现的简单易用的 WebSocket 服务器

> 原文：<https://dev.to/freekmurze/introducing-laravel-websockets-an-easy-to-use-websocket-server-implemented-in-php-1oj>

laravel-websockets 是一个 laravel 包，可以完全处理 websockets 的服务器端。它完全取代了对像 [Pusher](https://pusher.com/) 或基于 JavaScript 的 [laravel-echo-server](https://github.com/tlaverdure/laravel-echo-server) 这样的服务的需求。它有[丰富的文档](https://docs.beyondco.de/laravel-websockets/)和[一个你可以玩的演示应用](https://github.com/beyondcode/laravel-websockets-demo)。 [Marcel](https://twitter.com/marcelpociot) ，beyondcode 的开发者和共同所有者，和我在过去的几周里一直在一起工作。在这篇博文中，我们将向您介绍这个包。

[![console](img/b172981b81e0cc9f2975f0280ff17df3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PWA91K78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/laravel-websockets/server.png)

## 什么是 WebSockets？

简单来说，WebSocket 连接是浏览器和服务器之间的持久连接。它允许双向通信:服务器可以向浏览器发送消息，而浏览器——客户机——可以通过相同的连接做出响应。这不同于常规的 Ajax，后者只是单向通信:只有客户端可以向服务器请求东西。

WebSockets 主要用于实时应用，比如聊天应用。这样的聊天应用程序可以这样工作。

1.  用户 A 将浏览器指向聊天应用。浏览器创建一个到服务器的 WebSocket 连接。请记住，服务器保持连接打开。
2.  用户 B、C、D、...做同样的事情。服务器现在有多个打开的连接。
3.  第一个用户通过开放的 WebSocket 连接向服务器传输消息。
4.  服务器看到通过第一个连接传入的消息，并将该消息发送到它打开的所有其他连接。
5.  用户 B、C、D 的浏览器，...通过 WebSockets 获得一个即将到来的消息，并可以用它做一些事情(在聊天应用程序的情况下:显示它)。

所有这些都是实时发生的，不需要轮询。如果有很多消息，有效载荷也会小很多。不需要通过连接发送头等。

## 什么是 laravel-websockets？

我们的 [laravel-websockets](https://github.com/beyondcode/laravel-websockets) 包可以为您处理 websockets 的服务器端。它包含一个用 PHP 实现的 web 服务器，可以处理传入的 WebSocket 连接。

laravel-websockets 建立在一个处理 websockets 的底层包 [Ratchet](http://socketo.me/) 之上。直接使用 Ratchet 是可行的，但是要让它工作还需要一些研究和设置。我们的软件包把所有这些都带走了:你将能够在一分钟内运行一个 WebSockets 服务器。

Marcel 和我添加了许多不错的功能，当使用棘轮本身时，这将需要你相当长的时间来实现。它支持多租户，因此您可以建立一个 webSockets 服务器，并将其用于许多不同的应用程序。我们还添加了一个实时调试仪表板，这样你就可以检查通过 webSockets 传输了什么。所有这些都由一个实时图表提供支持，该图表为您提供对 WebSocket 应用程序指标的关键见解，如峰值连接数、发送的消息量和接收的 API 消息量。

我们还实现了[推送消息协议](https://pusher.com/docs/pusher_protocol)。通过这样做，所有现有的支持 Pusher 的包和应用程序也将与我们的包一起工作。Laravel 的广播能力正好可以。 [laravel-echo](https://laravel.com/docs/5.7/broadcasting#receiving-broadcasts) ，一个处理 webSockets 客户端的 JavaScript 库，也是 100%兼容。您可以使用 Pusher 提供的所有功能，例如[私人频道](https://pusher.com/docs/client_api_guide/client_private_channels)、[在线频道](https://pusher.com/docs/client_api_guide/client_presence_channels)，甚至[Pusher HTTP API](https://pusher.com/docs/rest_api)。

除了作为 Pusher 的免费替代品，这个包也给了 Laravel 包开发者一个很大的优势。现在，将 WebSocket 功能添加到您的应用程序或包中要容易得多——因为我们的包完全不需要安装第三方应用程序或服务器。推进器也有一个相当保守的最大有效载荷尺寸。在我们的软件包中，您可以指定自己的最大有效载荷大小。

## 如何使用

在深入了解这一切是如何工作的细节之前，让我们先看看如何实际使用它。

首先，要求作曲者

```
composer require beyondcode/laravel-websockets 
```

这个包附带了一个迁移，用于在运行 WebSocket 服务器时存储统计信息。您可以使用
发布迁移文件

```
php artisan vendor:publish --provider="BeyondCode\LaravelWebSockets\WebSocketsServiceProvider" --tag="migrations" 
```

使用
运行迁移

```
php artisan migrate 
```

接下来，您必须发布配置文件。

```
php artisan vendor:publish --provider="BeyondCode\LaravelWebSockets\WebSocketsServiceProvider" --tag="config" 
```

这是将要发布的文件。

```
return [

    /*
     * This package comes with multi tenancy out of the box. Here you can
     * configure the different apps that can use the webSockets server.
     *
     * Optionally you can disable client events so clients cannot send
     * messages to each other via the webSockets.
     */
    'apps' => [
        [
            'id' => env('PUSHER_APP_ID'),
            'name' => env('APP_NAME'),
            'key' => env('PUSHER_APP_KEY'),
            'secret' => env('PUSHER_APP_SECRET'),
            'enable_client_messages' => false,
            'enable_statistics' => true,
        ],
    ],

    /*
     * This class is responsible for finding the apps. The default provider
     * will use the apps defined in this config file.
     *
     * You can create a custom provider by implementing the
     * `AppProvider` interface.
     */
    'app_provider' => BeyondCode\LaravelWebSockets\Apps\ConfigAppProvider::class,

    /*
     * This array contains the hosts of which you want to allow incoming requests.
     * Leave this empty if you want to accept requests from all hosts.
     */
    'allowed_origins' => [
        //
    ],

    /*
     * The maximum request size in kilobytes that is allowed for an incoming WebSocket request.
     */
    'max_request_size_in_kb' => 250,

    /*
     * This path will be used to register the necessary routes for the package.
     */
    'path' => 'laravel-websockets',

    'statistics' => [
        /*
         * This model will be used to store the statistics of the WebSocketsServer.
         * The only requirement is that the model should extend
         * `WebSocketsStatisticsEntry` provided by this package.
         */
        'model' => \BeyondCode\LaravelWebSockets\Statistics\Models\WebSocketsStatisticsEntry::class,

        /*
         * Here you can specify the interval in seconds at which statistics should be logged.
         */
        'interval_in_seconds' => 60,

        /*
         * When the clean-command is executed, all recorded statistics older than
         * the number of days specified here will be deleted.
         */
        'delete_statistics_older_than_days' => 60
    ],

    /*
     * Define the optional SSL context for your WebSocket connections.
     * You can see all available options at: http://php.net/manual/en/context.ssl.php
     */
    'ssl' => [
        /*
         * Path to local certificate file on filesystem. It must be a PEM encoded file which
         * contains your certificate and private key. It can optionally contain the
         * certificate chain of issuers. The private key also may be contained
         * in a separate file specified by local_pk.
         */
        'local_cert' => null,

        /*
         * Path to local private key file on filesystem in case of separate files for
         * certificate (local_cert) and private key.
         */
        'local_pk' => null,

        /*
         * Passphrase for your local_cert file.
         */
        'passphrase' => null
    ],
]; 
```

最后一步是填充一些环境变量。确保在您的`.env`中填写`APP_NAME`、`PUSHER_APP_ID`、`PUSHER_APP_KEY`、`PUSHER_APP_SECRET`。你可能想知道为什么我们在这里使用`PUSHER_`前缀。稍后会详细介绍。

这样一来，您就可以通过执行这个命令来启动服务器。

```
php artisan websockets:serve 
```

现在你可以使用一个像 laravel-echo 这样的库来连接服务器。我不会详细介绍整个过程。从客户端开始，查看[我们的文档](https://docs.beyondco.de/laravel-websockets/1.0/basic-usage/pusher.html#pusher-configuration)、[laravel-echo 文档](https://laravel.com/docs/5.7/broadcasting#installing-laravel-echo)和这个[演示应用](https://github.com/beyondcode/laravel-websockets-demo)。

[![demo](img/f227b8c09fd014ecfb441cf1785384ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1tePzWqu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/laravel-websockets/demo.png)

## 引擎盖下如何工作？

如果您对所有这些工作原理的具体细节不感兴趣，而只是想使用它，那么您可以跳到 debug dashboard 上的部分。

当执行`php artisan websockets:serve`时，你会注意到这个命令永远不会结束。这是因为在命令的[中，我们启动了一个棘轮服务器，它开始列出连接(默认情况下在端口 6001 上)。](https://github.com/beyondcode/laravel-websockets/blob/dc87bfb221540724387712cdeb39b5f783efd6c0/src/Console/StartWebSocketServer.php) 

```
protected function startWebSocketServer()
{
    $this->info("Starting the WebSocket server on port {$this->option('port')}...");

    $routes = WebSocketsRouter::getRoutes();

    /**? Start the server ? */
    (new WebSocketServerFactory())
        ->useRoutes($routes)
        ->setHost($this->option('host'))
        ->setPort($this->option('port'))
        ->setConsoleOutput($this->output)
        ->createServer()
        ->run();
} 
```

我们给服务器的东西之一是`$routes`。那个变量是`\Symfony\Component\Routing\RouteCollection`的一个实例。那是我们在[创造的路由器等级](https://github.com/beyondcode/laravel-websockets/blob/dc87bfb221540724387712cdeb39b5f783efd6c0/src/Server/Router.php)。

因为在我们的`StartWebSocketsCommand`中，我们已经[称](https://github.com/beyondcode/laravel-websockets/blob/dc87bfb221540724387712cdeb39b5f783efd6c0/src/Console/StartWebSocketServer.php#L65)为`echo`方法，这些路由将包含[这些路由](https://github.com/beyondcode/laravel-websockets/blob/dc87bfb221540724387712cdeb39b5f783efd6c0/src/Server/Router.php#L32-L40)。

```
public function echo()
{
    $this->get('/app/{appKey}', WebSocketHandler::class);

    $this->post('/apps/{appId}/events', gerEventController::class);
    $this->get('/apps/{appId}/channels', hChannelsController::class);
    $this->get('/apps/{appId}/channels/{channelName}', hChannelController::class);
    $this->get('/apps/{appId}/channels/{channelName}/users', hUsersController::class);

} 
```

这就是有趣的地方。第一个路由是将处理传入 webSockets 的路由。路由 URL `/app/{appKey}`是那个特定的 URL，因为[是推送器使用的](https://pusher.com/docs/pusher_protocol#websocket-connection)。使用这个 URL 对于让所有 Pusher 兼容的 JavaScript 客户机工作是很重要的。

其他的监听传入的 HTTP 连接。如果你想到它，这是相当惊人的:我们的小服务器可以监听 WebSockets 和 HTTP 连接。PHP 是不是很牛逼？

### 传入 WebSocket 连接

让我们更深入地了解 WebSocket 连接是如何处理的。

```
$this->get('/app/{appKey}', WebSocketHandler::class); 
```

`Router`中的那个`get`方法将调用`getRoute`方法。在该方法中，我们将实例化的`WebSocketHandler`包装在一个`WsServer`类中。那个`WsServer`类是棘轮提供的。它提供了处理 WebSockets 的基本工作的代码。

```
// inside the `BeyondCode\LaravelWebSockets\Server\Router` class

public function get(string $uri, $action)
{
    $this->addRoute('GET', $uri, $action);
}

protected function getRoute(string $method, string $uri, $action): Route
{
    /**
     * If the given action is a class that handles WebSockets, then it's not a regular
     * controller but a WebSocketHandler that needs to converted to a WsServer.
     *
     * If the given action is a regular controller we'll just instanciate it.
     */
    $action = is_subclass_of($action, MessageComponentInterface::class)
        ? $this->createWebSocketsServer($action)
        : app($action);

    return new Route($uri, ['_controller' => $action], [], [], null, [], [$method]);
}

protected function createWebSocketsServer(string $action): WsServer
{
    $app = app($action);

    if (WebsocketsLogger::isEnabled()) {
        $app = WebsocketsLogger::decorate($app);
    }

    return new WsServer($app);
} 
```

现在您已经知道了如何为 WebSockets 部分设置路由，让我们看看`WebSocketHandler`实际上做了什么。

你可以把[`WebSocketHandler`类](https://github.com/beyondcode/laravel-websockets/blob/d7114a778a7e00758456a09da2c8807498bbd030/src/WebSockets/WebSocketHandler.php)看作一个控制器，但是是针对 WebSockets 而不是 HTTP。它包含各种生命周期方法:

*   `onOpen`:当新客户端打开 webSockets 连接时调用
*   `onMessage`:当客户端通过 webSocket 发送新消息时调用。
*   `onClose`:当客户端断开连接时调用(通常是通过导航到另一个页面，或者关闭标签页或浏览器窗口)

#### onub

这是`onOpen`方法的代码。

```
public function onOpen(ConnectionInterface $connection)
{
    $this
        ->verifyAppKey($connection)
        ->generateSocketId($connection)
        ->establishConnection($connection);
} 
```

当一个新的连接进来时，会发生三件事。首先是 app 验证。还记得用来处理 webSocket 连接的路由吗？

```
$this->get('/app/{appKey}', WebSocketHandler::class); 
```

`verifyAppKey`功能将检查给定的`appKey`是否正确。在默认安装中，如果在`websockets.php`配置文件中有一个用这个键定义的应用程序，那么`App::findByAppKey`就会。

```
protected function verifyAppKey(ConnectionInterface $connection)
{
    $appKey = QueryParameters::create($connection->httpRequest)->get('appKey');

    if (!$app = App::findByKey($appKey)) {
        throw new UnknownAppKey($appKey);
    }

    $connection->app = $app;

    return $this;
} 
```

我们在连接本身上存储了一个对`app`的引用，这样我们就可以在连接上的后续事件中使用它(例如当有消息进来时)。

其次，我们将生成一个套接字 id。

```
protected function generateSocketId(ConnectionInterface $connection)
{
    $socketId = sprintf("%d.%d", random_int(1, 000000), random_int(1, 1000000000));

    $connection->socketId = $socketId;

    return $this;
} 
```

`socketId`必须遵循特定的格式。用点分隔的两个数字。我们将把这个号码存储在连接本身上。我们将使用该 id 来识别这个特定的连接。请记住，我们的服务器进程永远不会结束，所以这个 id 将保持连接，只要它是开放的。

在`opOpen`发生的最后一件事是向浏览器发回连接成功的响应。同样，我们在这里遵循 pusher 协议，并发出一个`pusher:connection_established`事件，以兼容所有现有的 Pusher 包。我们还将在仪表板上记录这个事件。

```
protected function establishConnection(ConnectionInterface $connection)
{
    $connection->send(json_encode([
        'event' => 'pusher:connection_established',
        'data' => json_encode([
            'socket_id' => $connection->socketId,
            'activity_timeout' => 30,
        ])
    ]));

    DashboardLogger::connection($connection);

    return $this;
} 
```

所有的工作都完成了，我们有了一个开放的连接。

现在让我们来看看通过这个连接发送消息时会发生什么。

#### onMessage

让我们来看看[`WebSocketHandler`](https://github.com/beyondcode/laravel-websockets/blob/89ace081399f197de46c9b5e7466611da62f4add/src/WebSockets/WebSocketHandler.php#L36-L41)
中的`onMessage`函数

```
public function onMessage(ConnectionInterface $connection, MessageInterface $message)
{
    $message = PusherMessageFactory::createForMessage($message, $connection, $this->channelManager);

    $message->respond();
} 
```

在这里，我们得到信息并作出回应。让我们深入研究一下[中的`PusherMessageFactory`类](https://github.com/beyondcode/laravel-websockets/blob/89ace081399f197de46c9b5e7466611da62f4add/src/WebSockets/Messages/PusherMessageFactory.php#L11-L21)中的`createForMessage`方法。

```
use BeyondCode\LaravelWebSockets\WebSockets\Channels\ChannelManager;
use Ratchet\ConnectionInterface;
use Ratchet\RFC6455\Messaging\MessageInterface;

class PusherMessageFactory
{
    public static function createForMessage(
        MessageInterface $message,
        ConnectionInterface $connection,
        ChannelManager $channelManager): PusherMessage
    {
        $payload = json_decode($message->getPayload());

        return starts_with($payload->event, 'pusher:')
            ? new PusherChannelProtocolMessage($payload, $connection, $channelManager)
            : new PusherClientMessage($payload, $connection, $channelManager);
    }
} 
```

在上面的类中，我们将根据有效载荷中的事件名称选择正确的消息类型。`PusherChannelProtocolMessage`处理主要围绕频道订阅的消息。`PusherClientMessage`将处理订阅了频道的客户端发送的消息，稍后会详细介绍。

让我们看看`PusherChannelProtocolMessage`会发生什么。更具体地说，我们将看看它的`respond`方法，因为那是在`WebSocketHandler`
类中被调用的

```
public function respond()
{
    $eventName = camel_case(str_after($this->payload->event, ':'));

    if (method_exists($this, $eventName)) {
        call_user_func([$this, $eventName], $this->connection, $this->payload->data ?? new stdClass());
    }
} 
```

`$this->payload->event`将包含一个类似`pusher:subscribe`的字符串。所以在上面的代码中`$eventName`会包含`subscribe`。

让我们先谈一谈订阅。推动器与不同的`channels`一起工作。你可以把频道想象成收音机上的频道。如果你把收音机调到特定的频道，你就会听到那个频道发送的信息。根据[推送协议](https://pusher.com/docs/pusher_protocol#pusher-subscribe)，想要订阅频道的客户端必须使用`subscribe`方法。

上面的`respond`函数中的`call_user_func`调用将尝试调用一个名为 like event 的函数。因此对于事件`subscribe`，将调用`subscribe`方法。

```
protected function subscribe(ConnectionInterface $connection, stdClass $payload)
{
    $channel = $this->channelManager->findOrCreate($connection->app->id, $payload->channel);

    $channel->subscribe($connection, $payload);
} 
```

在这里我们第一次接触到[`ChannelManager`](https://github.com/beyondcode/laravel-websockets/blob/89ace081399f197de46c9b5e7466611da62f4add/src/WebSockets/Channels/ChannelManager.php)。这个类负责跟踪哪些连接订阅了哪些信道。

让我们来看看`ChannelManager`上那个叫做`findOrCreate`的

```
public function findOrCreate(string $appId, string $channelName): Channel
{
    if (!isset($this->channels[$appId][$channelName])) {
        $channelClass = $this->determineChannelClass($channelName);

        $this->channels[$appId][$channelName] = new $channelClass($channelName);
    }

    return $this->channels[$appId][$channelName];
} 
```

在这个函数中，我们将为给定的`appId`和`channelName`查找一个`Channel`实例。如果它不存在，我们将创建一个新的并存储在`channels`实例变量中。因为我们的服务器进程永远不会结束，并且对于所有传入的连接都是相同的，所以将它存储在内存中就足够了，我们不需要将它保存在其他地方。

让我们进入`determineChannelClass`讨论 Pusher 定义的不同渠道类型。

```
protected function determineChannelClass(string $channelName): string
{
    if (starts_with($channelName, 'private-')) {
        return PrivateChannel::class;
    }

    if (starts_with($channelName, 'presence-')) {
        return PresenceChannel::class;
    }

    return Channel::class;
} 
```

推动器有三种不同的通道类型:

*   [私有频道](https://pusher.com/docs/client_api_guide/client_private_channels):在客户端可以实际订阅频道之前，将执行认证检查
*   出席频道(presence channels):与私人频道相同，但具有额外的功能来确定谁在频道上(如果你正在构建类似聊天室的东西，这很方便)
*   [公共频道](https://pusher.com/docs/client_api_guide/client_public_channels):这些是任何人都可以订阅的频道，不进行认证检查。

这三种通道类型在我们的代码中实现为 [`PrivateChannel`](https://github.com/beyondcode/laravel-websockets/blob/89ace081399f197de46c9b5e7466611da62f4add/src/WebSockets/Channels/PrivateChannel.php) 、 [`PresenceChannel`](https://github.com/beyondcode/laravel-websockets/blob/89ace081399f197de46c9b5e7466611da62f4add/src/WebSockets/Channels/PresenceChannel.php) 和 [`Channel`](https://github.com/beyondcode/laravel-websockets/blob/89ace081399f197de46c9b5e7466611da62f4add/src/WebSockets/Channels/Channel.php) 。在`determineChannelClass`中，我们更新并返回正确类型的通道。

让我们稍微回到前面提到的[`PusherChannelProtocolMessage`类](https://github.com/beyondcode/laravel-websockets/blob/89ace081399f197de46c9b5e7466611da62f4add/src/WebSockets/Messages/PusherChannelProtocolMessage.php#L51)中的`subscribe`方法。代码又来了。

```
protected function subscribe(ConnectionInterface $connection, stdClass $payload)
{
    $channel = $this->channelManager->findOrCreate($connection->client->appId, $payload->channel);

    $channel->subscribe($connection, $payload);
} 
```

现在我们知道了`$channel`拥有一个正确的通道类的实例，让我们来看看`subscribe`的实现。先来看看[普通`Channel`](https://github.com/beyondcode/laravel-websockets/blob/89ace081399f197de46c9b5e7466611da62f4add/src/WebSockets/Channels/Channel.php) 班的情况。

```
public function subscribe(ConnectionInterface $connection, stdClass $payload)
{
    $this->saveConnection($connection);

    $connection->send(json_encode([
        'event' => 'pusher_internal:subscription_succeeded',
        'channel' => $this->channelName
    ]));
}

protected function saveConnection(ConnectionInterface $connection)
{
    $hadConnectionsPreviously = $this->hasConnections();

    $this->subscriptions[$connection->socketId] = $connection;

    if (! $hadConnectionsPreviously) {
        DashboardLogger::occupied($connection, $this->channelName);
    }

    DashboardLogger::subscribed($connection, $this->channelName);
} 
```

订阅通道只不过是存储连接的`socketId`(还记得[我们在`onOpen`方法中设置了这个 id 吗？](https://github.com/beyondcode/laravel-websockets/blob/89ace081399f197de46c9b5e7466611da62f4add/src/WebSockets/WebSocketHandler.php#L74)在`subscriptions`通道的实例变量中？).`saveConnection`还发送一条连接消息，让客户端知道订阅成功了。推送协议指定的`pusher_internal:subscription_succeeded`事件名称为[。](https://pusher.com/docs/pusher_protocol#client-only-events)

这样，客户端被订阅，传递给`onMessage`的消息被处理。

但是，如果我们没有使用常规的渠道 put 和`PrivateChannel`会发生什么呢？我们来看看`PrivateChannel`。

```
namespace BeyondCode\LaravelWebSockets\WebSockets\Channels;

use Ratchet\ConnectionInterface;
use stdClass;

class PrivateChannel extends Channel
{
    public function subscribe(ConnectionInterface $connection, stdClass $payload)
    {
        $this->verifySignature($connection, $payload);

        parent::subscribe($connection, $payload);
    }
} 
```

因此,`subscribe`方法将与常规通道中的方法完全相同，但是在实际订阅之前，签名将被验证。由于这篇博文很长，我不打算进行实际验证。如果你想了解更多，请查看[方法的实现](https://github.com/beyondcode/laravel-websockets/blob/89ace081399f197de46c9b5e7466611da62f4add/src/WebSockets/Channels/Channel.php#L34-L45)和[关于认证的推送文件](https://pusher.com/docs/client_api_guide/client_private_channels)。

这样，客户就订阅了一个频道。现在让我们回顾一下当客户端向通道发送消息时会发生什么。

[根据推送协议](https://pusher.com/docs/pusher_protocol#channel-client-events)，客户端可以使用包含以`client-`开头的事件名称的消息相互发送消息。让我们再来看看 [`PusherMessageFactory`](https://github.com/beyondcode/laravel-websockets/blob/286629fd98011763bb41ee6e47a8a82e9d1f5e90/src/WebSockets/Messages/PusherMessageFactory.php) 。

```
return starts_with($payload->event, 'pusher:')
    ? new PusherChannelProtocolMessage($payload, $connection, $channelManager)
    : new PusherClientMessage($payload, $connection, $channelManager); 
```

我们已经讲过了`PusherChannelProtocolMessage`，现在让我们把头伸向 [`PusherClientMessage`](https://github.com/beyondcode/laravel-websockets/blob/7bc6e32a760fb6cdeec61b4e12ddaa8da5c6a814/src/WebSockets/Messages/PusherClientMessage.php) 的`respond`法。

```
public function respond()
{
    if (!starts_with($this->payload->event, 'client-')) {
        return;
    }

    if (! $this->connection->app->clientMessagesEnabled) {
        return;
    }

    DashboardLogger::clientMessage($this->connection, $this->payload);

    $channel = $this->channelManager->find($this->connection->app->id, $this->payload->channel);

    optional($channel)->broadcastToOthers($this->connection, $this->payload);
} 
```

让我们检查一下代码，如果消息的事件名称不是以`client-`开头，它就不是客户端到客户端的消息，我们将退出。第二个 if 检查客户端到客户端的消息传递是否被启用(它[可以在每个应用](https://github.com/beyondcode/laravel-websockets/blob/286629fd98011763bb41ee6e47a8a82e9d1f5e90/config/websockets.php#L18)的基础上被禁用)。接下来，我们将在调试仪表板上记录消息。

这样一来，真正的工作就可以开始了。我们将要求`ChannelManager`为消息找到正确的`Channel`。如果它能找到正确的`Channel`，我们将调用它的`broadcast`。

`Channel` 上`broadcastToOthers`的[实现并没有那么难。下面的代码遍历订阅该通道的每个连接，并向该连接发送有效负载。发送原始消息的客户端不需要接收它。我们使用前面设置的 socket id](https://github.com/beyondcode/laravel-websockets/blob/7bc6e32a760fb6cdeec61b4e12ddaa8da5c6a814/src/WebSockets/Channels/Channel.php#L89-L105)[过滤掉发送者的连接](https://github.com/beyondcode/laravel-websockets/blob/7bc6e32a760fb6cdeec61b4e12ddaa8da5c6a814/src/WebSockets/WebSocketHandler.php#L80)。

```
public function broadcastToOthers(ConnectionInterface $connection, $payload)
{
    $this->broadcastToEveryoneExcept($payload, $connection->socketId);
}

public function broadcastToEveryoneExcept($payload, ?string $socketId = null)
{
    if (is_null($socketId)) {
        return $this->broadcast($payload);
    }

    foreach ($this->subscribedConnections as $connection) {
        if ($connection->socketId !== $socketId) {
            $connection->send(json_encode($payload));
        }
    }
} 
```

因此，我们向该频道的所有客户发送了一条消息。

#### 盎司

[`WebSocketHandler`](https://github.com/beyondcode/laravel-websockets/blob/89ace081399f197de46c9b5e7466611da62f4add/src/WebSockets/WebSocketHandler.php#L43-L46) 负责的第三种生命周期方法是`onClose`。当客户端离开时，例如当标签页或浏览器窗口关闭时，它被调用。实现非常简单。

```
public function onClose(ConnectionInterface $connection)
{
    $this->channelManager->removeFromAllChannels($connection);
} 
```

在`ChannelManager`的`removeFromAllChannels`方法中，我们将移除所有通道的连接。我们还将删除不再有连接的频道和应用，这样我们就不会泄漏内存。

```
public function removeFromAllChannels(ConnectionInterface $connection)
{
    if (!isset($connection->app)) {
        return;
    }

    /**
     * Remove the connection from all channels.
     */
    collect(array_get($this->channels, $connection->app->id, []))->each->unsubscribe($connection);

    /**
     * Unset all channels that have no connections so we don't leak memory.
     */
    collect(array_get($this->channels, $connection->app->id, []))
        ->reject->hasConnections()
        ->each(function (Channel $channel, string $channelName) use ($connection) {
            unset($this->channels[$connection->app->id][$channelName]);
        });

    if (count(array_get($this->channels, $connection->app->id, [])) === 0) {
        unset($this->channels[$connection->app->id]);
    };
} 
```

### 传入 HTTP 连接

上面我们已经解释过`php artisan websockets:serve`我们启动了一个棘轮服务器。如果你正在从头到尾阅读这篇文章，你应该已经知道棘轮服务器是如何处理 webSockets 的。现在让我们把重点放在 HTTP 部分。

Laravel 对[广播事件](https://laravel.com/docs/master/broadcasting)有极好的支持。你有没有想过当 Laravel 广播一些东西时，引擎盖下会发生什么？简单地说，它将使用某个有效负载对某个端点执行 HTTP 调用。

[我们的文档指定](https://docs.beyondco.de/laravel-websockets/1.0/basic-usage/pusher.html#pusher-configuration)您应该将推送器配置的`host`更改为‘127 . 0 . 0 . 1’(或者您运行我们的包的服务器的主机名)。这将使 Laravel 向我们包而不是 Pusher 发送 HTTP 调用。

还记得我们服务器里的那些路线吗？当 Laravel 广播一个事件时，它会发送一个 POST 请求到这个路由:

```
$this->post('/apps/{appId}/events', TriggerEventController::class); 
```

让我们看看那个 [`TriggerEventController`](https://github.com/beyondcode/laravel-websockets/blob/master/src/HttpApi/Controllers/TriggerEventController.php)
的代码

```
class TriggerEventController extends Controller
{
    public function __invoke(Request $request)
    {
        $this->ensureValidSignature($request);

        foreach ($request->json()->get('channels', []) as $channelName) {
            $channel = $this->channelManager->find($request->appId, $channelName);

            optional($channel)->broadcastToEveryoneExcept([
                'channel' => $channelName,
                'event' => $request->json()->get('name'),
                'data' => $request->json()->get('data'),
            ], $request->json()->get('socket_id'));

            DashboardLogger::apiMessage(
                $request->appId,
                $channelName,
                $request->json()->get('name'),
                $request->json()->get('data')
            );
        }

        return $request->json()->all();
    }
} 
```

我们不希望任何人都能调用这个端点。当发送请求时，Laravel 将在请求中添加一个散列版本的应用程序签名以及一些其他参数。在 [`ensureValidSignature`方法](https://github.com/beyondcode/laravel-websockets/blob/89ace081399f197de46c9b5e7466611da62f4add/src/HttpApi/Controllers/Controller.php#L87-L105)中，我们验证签名是否正确。

如果签名是正确的，我们将让`ChannelManager`找到传入请求的所有通道。这很有意思。这个渠道经理是如何实例化的？它是怎么知道频道的？我们的服务器是一个永无止境的过程。它处理 WebSockets 和 HTTP 请求。`ChannelManager`被[绑定为单例](https://github.com/beyondcode/laravel-websockets/blob/89ace081399f197de46c9b5e7466611da62f4add/src/WebSocketsServiceProvider.php#L43-L45)，所以它只能有一个实例。所以这个`ChannelManager`保存了当前订阅一个频道的客户端的所有连接。因此，在处理这个 HTTP 请求时，我们可以通过连接的 WebSockets 发送消息。这在我们的书里是相当惊人的！

在由`ChannelManager`找到的每个通道上，我们将调用`broadcastToEveryoneExcept`方法。下面是实现:

```
public function broadcastToEveryoneExcept($payload, ?string $socketId = null)
{
    if (is_null($socketId)) {
        return $this->broadcast($payload);
    }

    foreach ($this->subscribedConnections as $connection) {
        if ($connection->socketId !== $socketId) {
            $connection->send(json_encode($payload));
        }
    }
} 
```

如前所述，一个信道包含所有订阅它的连接。如果 Laravel 没有被特定的客户端指示广播消息，那么该消息可能是由服务器本身发起的，并且它应该被广播到所有订阅的连接。

如果 Laravel 的广播是由一个特定的客户端发起的，那么 Laravel 会将该连接的 socketId 与有效负载一起发送出去。如果是这种情况，我们将向所有连接的客户端发送消息，除了发出消息的客户端。

这就是 HTTP 端的工作方式！

## 如果不想使用推送协议怎么办？

虽然我们的包的主要目的是使 Pusher Javascript 客户机或 Laravel Echo 的使用尽可能简单，但是您并不局限于 Pusher 协议。可能有这样的情况，您需要的只是一个简单的、框架式的 websocket 服务器，您希望完全控制传入的有效负载以及您想对它做什么——而不需要“通道”。

您可以轻松创建自己的自定义 WebSocketHandler 类。你需要做的就是实现棘轮`Ratchet\WebSocket\MessageComponentInterface`。

一旦实现，你将拥有一个类似于这样的类:

```
namespace App;

use Ratchet\ConnectionInterface;
use Ratchet\RFC6455\Messaging\MessageInterface;
use Ratchet\WebSocket\MessageComponentInterface;

class MyCustomWebSocketHandler implements MessageComponentInterface
{

    public function onOpen(ConnectionInterface $connection)
    {
        // TODO: Implement onOpen() method.
    }

    public function onClose(ConnectionInterface $connection)
    {
        // TODO: Implement onClose() method.
    }

    public function onError(ConnectionInterface $connection, \Exception $e)
    {
        // TODO: Implement onError() method.
    }

    public function onMessage(ConnectionInterface $connection, MessageInterface $msg)
    {
        // TODO: Implement onMessage() method.
    }
} 
```

在类本身中，您可以完全控制 WebSocket 连接的所有生命周期事件，并且可以拦截传入的消息并对它们做出反应。

唯一缺少的部分是，您需要告诉我们的 WebSocket 服务器在特定的路由端点加载这个处理程序。这可以通过使用`WebSocketsRouter`外观来实现。

这个类负责向实际的 webSocket 服务器注册路由。您可以使用`websocket`方法来定义一个定制的 WebSocket 端点。该方法需要两个参数:WebSocket 处理的路径应该可用，以及 WebSocket 处理程序类的完全限定类名。

例如，这可以在您的`routes/web.php`文件中完成。

```
WebSocketsRouter::webSocket('/my-websocket', \App\MyCustomWebSocketHandler::class); 
```

一旦您添加了自定义 WebSocket 路由，请确保重新启动我们的 WebSockets 服务器以使更改生效。

## 调试仪表板

Pusher 的众多伟大特性之一是“调试控制台”。一个仪表板，允许您实时查看所有 WebSocket 连接、事件和 API 请求。我们希望将这一功能也纳入我们的产品包中。这就是为什么它还包含一个调试仪表板，具有与 Pusher 相同的功能。

这是它的样子: [![The debug dashboard](img/cc520de7ebb1a6aa9f55c605b6367b7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aSAYtFEh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/laravel-websockets/dashboard.jpg)

WebSocket 仪表板的默认位置是在`/laravel-websockets`。路线会自动注册。如果您想改变仪表板的 URL，您可以在您的`config/websockets.php`文件中用`path`设置来配置它。

要访问调试仪表板，您可以在浏览器中访问您的 Laravel 项目的仪表板 URL。由于您的 WebSocket 服务器支持多个应用程序，因此您可以选择要连接和检查的应用程序。

按下“连接”按钮，您可以建立 WebSocket 连接，并实时查看 WebSocket 服务器上发生的所有事件。

默认情况下，只有当您的应用程序环境设置为`local`时，才允许访问 WebSocket 仪表板。

但是，您可以通过覆盖正在使用的 Laravel 门来改变这种行为。Laravel 附带的`AuthServiceProvider`是一个很好的地方。

```
public function boot()
{
    $this->registerPolicies();

    Gate::define('viewWebSocketsDashboard', function ($user = null) {
        return in_array([
            // 
        ], $user->email);
    });
} 
```

如果您很快想尝试一个事件，您也可以使用调试仪表板发送一个事件到一个特定的通道。

只需输入通道、事件名称，并提供有效的 JSON 有效负载，将它发送给给定通道中所有连接的客户机。

您可以在 debug dashboard 上找到的另一个特性是能够实时查看 WebSocket 服务器的关键指标。

在底层，WebSocket 服务器将存储当前峰值连接数、接收到的 WebSocket 消息的数量以及在固定间隔内定义的接收到的 API 消息的数量的快照。默认设置是每 60 秒存储一次快照——但是您也可以在配置文件中更改这个时间间隔。

[![Real-Time statistics on the dashboard](img/ffe9ecd9602f7dc601002ab60119f2dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jW2uI_9Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://freek.dev/uploads/media/laravel-websockets/statistics.gif)

## 是否缩放？

这不是一个容易回答的问题，因为您的里程可能会有所不同。但是通过适当的服务器端配置，您的 WebSocket 服务器可以轻松容纳大量并发连接。

这是一个在最小的数字海洋水滴上完成的示例基准，也有几个其他的 Laravel 项目在运行。在这个特定的服务器上，最大并发连接数最终达到了 15，000。

[![Graph](img/ada45ced2344de94aa14b44320d3e3df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LE7DOsIb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/laravel-websockets/simultaneous_users.png)

这是另一个在 2GB 数字 Ocean droplet 上用 2 个 CPU 运行的基准测试。这个服务器设置的最大并发连接数接近 60，000。

[![Graph](img/4d18b4c9187baf92832f1c3d2c2d809f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZpqmlMK2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/laravel-websockets/simultaneous_users_2gb.png)

## 结对编程好玩！

我们真的很高兴创造这个包。这个想法是在一次关于马塞尔正在制作的黄昏仪表板的转换中产生的。该仪表板使用 webSockets。我们都发现必须有一种更简单的方法来使用 webSockets。我们想让类似`Route::websocket`的东西正常工作。

从那以后事情进展得很快。Marcel 在构建黄昏仪表板时已经有了一些使用棘轮的经验，因此他创建了一个概念证明，证明我们在理论上可以取代 Pusher 提供的所有功能。Freek 对代码做了一些润色，实现了一些次要功能。与此同时，Marcel 还添加了很酷的调试仪表板，并处理文档。

有几个晚上，我们用 Slack 共享屏幕，一起编程。多租户支持和一些围绕 pusher 消息的功能是通过 pair 编程实现的。这对我们俩来说都是一次很好的经历。

当软件包功能完成时，我们保持联系，每天一起润色软件包和文档。接近尾声的时候，一个大的重构完成了。许多名称空间和目录结构都进行了更改，以使整个包更加清晰。

> Blogpost 驱动的开发
> 
> -2011 年 12 月 1 日，T3

在写博文的时候，我们又检查了一遍所有的代码，仍然润色了一些代码，甚至添加了一些小功能。我们很快就考虑创建一个名为`beyond-spatie`的组织来放这个包，但是我们决定如果它在一个现有的组织上会更好，于是选择了 [beyondcode](https://github.com/beyondcode/laravel-websockets) ，马塞尔的公司。

我们在包裹里放了很多爱，我们为我们一起完成的工作感到骄傲。

## 在关闭

如果你阅读了以上所有内容，那么恭喜你，我们知道这是很难接受的。尽管很多事情发生在屏幕后面，但我们认为这个包很容易使用。如果你想了解更多关于这个包的信息，一定要阅读我们写的大量文档。前往 GitHub 上的[这个回购](https://github.com/beyondcode/laravel-websockets)阅读源代码。

如果你害怕在生产中使用它，不要害怕！事实上，这个包已经在[运行了一周，哦，天哪！](https://ohdear.app)，弗里克在监视 SaaS。该服务广泛使用 WebSockets 来使每个屏幕显示实时信息。所有这些都由我们的 laravel-websockets 包支持，它每天处理 50000 多个广播事件。

甚至[我们自己的文档](https://docs.beyondco.de/laravel-websockets/1.0/getting-started/introduction.html)也使用我们软件包支持的 WebSockets。在每个页面的右上角，您可以看到有多少其他用户也在阅读这些文档。如果你点击这个图标，它不仅会在你的浏览器上显示，也会在其他读者面前显示。好玩！

如果您想在生产环境中运行这个包，请务必查看关于如何使用 SSL 运行它的章节，以及关于一些部署问题的章节。

我们都有很多编写软件包的经验。如果你喜欢 laravel-websockets ,一定要看看我们的其他作品。你会在 GitHub 上找到 Marcel 之前在 [Beyondcode 的组织上的包裹列表。你会在](https://github.com/beyondcode/laravel-websockets)[Spatie 网站](https://spatie.be/open-source/packages)的开源部分找到 Freek 和他的团队创建的包。