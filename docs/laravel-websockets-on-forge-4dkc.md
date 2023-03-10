# Forge 上的 Laravel Websockets

> 原文：<https://dev.to/stayallive/laravel-websockets-on-forge-4dkc>

> 本文最后一次更新于 2021 年 5 月 15 日，是为 Laravel 8.x 和 Laravel WebSockets 1.x 编写的，可能适用于也可能不适用于未来的 Laravel 或 Laravel WebSockets 版本。

似乎关于如何在 Forge 上使用 SSL 的文档和/或解释不够充分或清晰。
一些人询问如何在 Laravel 中安装 Laravel WebSockets 并将其部署到 Forge *和* SSL(无论是通过 Let's Encrypt 还是 Cloudflare)。

在这篇文章中，我将尽力解释我是如何做到的，但请记住，我使用的是一个新的 Laravel 项目，其中没有真正的代码。但是所有项目的要点应该是一样的，应该可以帮助你建立，但是要对你复制的代码持批评态度，确保它适用于你。

> 注意:我在这个“指南”中假设了一些事情。读者知道如何使用 composer、安装 Laravel(和软件包)以及如何设置 Forge 服务器(带有守护程序、防火墙规则和加密证书)。

别挡路了，我们跳进来吧！

## 设置

我正在使用 Laravel 安装程序来安装一个新的 Laravel 安装。如何操作的说明可以在官方的 [Laravel 文档](https://laravel.com/docs/5.7/installation#installing-laravel)中找到。

在我的本地机器上运行`laravel new laravel-ws-example`之后。
我直接进入并安装 Laravel WebSockets 包，使用[安装指南](https://beyondco.de/docs/laravel-websockets/getting-started/installation)来安装这个包，发布迁移和配置文件。

我通过替换`<body>`向`welcome.blade.php`文件添加了一点样板代码，这样我们可以在以后使用它来验证一切正常:

```
<body class="antialiased">
    <div class="relative flex items-top justify-center min-h-screen bg-gray-100 dark:bg-gray-900 sm:items-center py-4 sm:pt-0">
        <div class="max-w-6xl mx-auto sm:px-6 lg:px-8">
            <div class="flex-center position-ref full-height">
                <div class="content dark:text-white text-center">
                    <h1>
                        Laravel WebSockets
                    </h1>

                    Currently <span id="online">...</span> browsers are viewing this page!
                </div>
            </div>
        </div>
    </div>
    <script>
        window.PUSHER_APP_KEY = '{{ config('broadcasting.connections.pusher.key') }}';
        window.APP_DEBUG = {{ config('app.debug') ? 'true' : 'false' }};
    </script>
    <script src="{{ mix('js/app.js') }}"></script>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

正如你可能知道的，我们的目标是显示有多少浏览器标签是打开的，一旦我们确认一切设置正确，就可以实时查看页面。

我还在我的`.env`文件中添加了以下配置设置，它配置了 Laravel WebSockets 使用的 ID、密钥和秘密:

```
BROADCAST_DRIVER=pusher
PUSHER_APP_ID=PFKJ5W3TYTFnv7p5yVRWsBPd
PUSHER_APP_KEY=wttTXkwAPaP8pu2M25MFNv2u
PUSHER_APP_SECRET=4czbE8JbHNDRSZTUSGdEw9QQ 
```

Enter fullscreen mode Exit fullscreen mode

> *注意:这里的 ID、key 和 secret 是示例值，请使用例如[random.org](https://www.random.org/passwords/?num=3&len=24&format=html&rnd=new)生成您自己的值。*

你可能会问，为什么`.env`键以`PUSHER_`为前缀，为什么我们使用`pusher`广播驱动？
这是因为 Laravel WebSockets 旨在替代[推杆](https://pusher.com/)。

我们添加到`.env`的值在`websockets.php`配置文件和`broadcasting.php`文件中使用。

重要的是不要改变`websockets.php`中的任何其他值。
您可能认为您需要在那里配置一个 SSL 证书文件，您不需要！
我们将使用 NGINX 来终止 SSL，这意味着 NGINX 处理 SSL 部分，并将普通 HTTP 流量转发到 websockets 服务器，稍后会详细介绍。

我将下面的连接添加到`broadcasting.php`文件中，假设我在运行应用程序的同一台机器上的端口`6001`上运行我的 websocket 服务器(这是默认设置，在本例中也是如此):

```
'pusher' => [
    'driver'  => 'pusher',
    'key'     => env('PUSHER_APP_KEY'),
    'secret'  => env('PUSHER_APP_SECRET'),
    'app_id'  => env('PUSHER_APP_ID'),
    'options' => [
        'host'   => '127.0.0.1',
        'port'   => 6001,
        'scheme' => 'http',
        'useTLS' => false, // this is no error, we are talking without SSL to the WebSocket server from Laravel but your end-users will connect with SSL
    ],
], 
```

Enter fullscreen mode Exit fullscreen mode

然后，我按照官方文档[安装 Laravel Echo】我的 Echo 看起来像这样:](https://laravel.com/docs/8.x/broadcasting#client-pusher-channels) 

```
window.Echo = new Echo({
    broadcaster:       'pusher',
    key:               window.PUSHER_APP_KEY,
    wsHost:            window.location.hostname,
    wsPort:            window.APP_DEBUG ? 6001 : 6002,
    wssPort:           window.APP_DEBUG ? 6001 : 6002,
    forceTLS:          !window.APP_DEBUG,
    disableStats:      true,
    enabledTransports: ['ws', 'wss'],
}); 
```

Enter fullscreen mode Exit fullscreen mode

重要的是要注意，我添加了`wssPort`并将`forceTLS`设置为`APP_DEBUG`不是的值，因为我们想要 SSL 的优点，但不是本地的。

你会注意到我从`window.PUSHER_APP_KEY`获得了应用密钥，从`window.APP_DEBUG`获得了调试状态，我已经使用下面的代码片段在`welcome.blade.php`中进行了设置(在我加载我的 JavaScript 之前)。它从我们之前设置的广播连接中读取应用密钥。

```
<script>
    window.PUSHER_APP_KEY = '{{ config('broadcasting.connections.pusher.key') }}';
    window.APP_DEBUG = {{ config('app.debug') ? 'true' : 'false' }};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

我继续添加一段概念验证代码，它只是简单地计算一个存在通道中的所有用户，我将它添加到我的`app.js`中，不要忘记在修改 JS 文件后运行`npm run dev`。

```
let onlineUsers = 0;

function update_online_counter() {
    document.getElementById('online').textContent = '' + onlineUsers;
}

window.Echo.join('common_room')
    .here((users) => {
        onlineUsers = users.length;

        update_online_counter();
    })
    .joining((user) => {
        onlineUsers++;

        update_online_counter();
    })
    .leaving((user) => {
        onlineUsers--;

        update_online_counter();
    }); 
```

Enter fullscreen mode Exit fullscreen mode

这是让我们的示例应用程序启动并运行所需的全部设置！

*注意:存在通道应该是经过认证的，但在这个例子中，我不想支持用户登录，所以我带着我的问题去了谷歌，在[栈溢出](https://stackoverflow.com/questions/43341820/laravel-echo-allow-guests-to-connect-to-presence-channel)帖子上找到了一些指导，并将其用于我们的用例。这允许一个未经认证的`common_room`频道，每个人和他们的狗都可以加入。请不要在你的应用程序中这样做，除非你知道你在做什么！*

## 送上铁匠铺！

所以下一步是将这个应用程序部署到 Forge，我将我的应用程序上传到了 [GitHub](https://github.com/stayallive/laravel-websockets-example) 。我还在 Forge 服务器上为它创建了一个站点，并使用 Forge apps 特性部署它。部署之后，我添加了一个加密证书。这些都可以在 Forge 界面上完成，不需要 SSH (Forge 太棒了！).

有两种方法可以继续，第一种是使用与应用程序相同的域，但使用与 443 不同的端口(我们将这样做),或者使用单独的(子)域。

第一种更容易，因为它不需要任何额外的设置，除了对 NGINX 配置的一些添加(这只是一些复制，大约是你工作的 99%)。

第二种方式需要你为 socket 服务器创建一个独立的域(或者 Forge 术语中的站点),允许你在一个完全不同的服务器上运行它，或者在端口`443`上运行它，如果那是你的障碍的话。

我们将使用您的应用程序所在的域，并使 websocket 服务器在不同的端口上可用。

为此，在 Forge 中编辑您站点的 NGINX 配置，并执行以下操作。复制那里的服务器块并将端口从`443`修改为`6002`(为什么不修改`6001`将在后面解释)并用来自 [Laravel WebSockets 文档](https://beyondco.de/docs/laravel-websockets/basic-usage/ssl#usage-with-a-reverse-proxy-like-nginx)的配置替换`location / { /* ... */ }`块。

如果你这样做了，NGINX 配置应该看起来有点像这样:

```
# FORGE CONFIG (DO NOT REMOVE!)
include forge-conf/laravel-ws-example.bouma.blog/before/*;

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name laravel-ws-example.bouma.blog;
    server_tokens off;
    root /home/forge/laravel-ws-example.bouma.blog/public;

    # FORGE SSL (DO NOT REMOVE!)
    ssl_certificate /etc/nginx/ssl/laravel-ws-example.bouma.blog/123456/server.crt;
    ssl_certificate_key /etc/nginx/ssl/laravel-ws-example.bouma.blog/123456/server.key;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers TLS13-AES-256-GCM-SHA384:TLS13-CHACHA20-POLY1305-SHA256:TLS_AES_256_GCM_SHA384:TLS-AES-256-GCM-SHA384:TLS_CHACHA20_POLY1305_SHA256:TLS-CHACHA20-POLY1305-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA;
    ssl_prefer_server_ciphers on;
    ssl_dhparam /etc/nginx/dhparams.pem;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.html index.htm index.php;

    charset utf-8;

    # FORGE CONFIG (DO NOT REMOVE!)
    include forge-conf/laravel-ws-example.bouma.blog/server/*;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    access_log off;
    error_log  /var/log/nginx/laravel-ws-example.bouma.blog-error.log error;

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}

server {
    listen 6002 ssl http2;
    listen [::]:6002 ssl http2;
    server_name laravel-ws-example.bouma.blog;
    server_tokens off;
    root /home/forge/laravel-ws-example.bouma.blog/public;

    # FORGE SSL (DO NOT REMOVE!)
    ssl_certificate /etc/nginx/ssl/laravel-ws-example.bouma.blog/123456/server.crt;
    ssl_certificate_key /etc/nginx/ssl/laravel-ws-example.bouma.blog/123456/server.key;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers TLS13-AES-256-GCM-SHA384:TLS13-CHACHA20-POLY1305-SHA256:TLS_AES_256_GCM_SHA384:TLS-AES-256-GCM-SHA384:TLS_CHACHA20_POLY1305_SHA256:TLS-CHACHA20-POLY1305-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA;
    ssl_prefer_server_ciphers on;
    ssl_dhparam /etc/nginx/dhparams.pem;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.html index.htm index.php;

    charset utf-8;

    # FORGE CONFIG (DO NOT REMOVE!)
    include forge-conf/laravel-ws-example.bouma.blog/server/*;

    location / {
        proxy_pass             http://127.0.0.1:6001;
        proxy_read_timeout     60;
        proxy_connect_timeout  60;
        proxy_redirect         off;

        # Allow the use of websockets
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    access_log off;
    error_log  /var/log/nginx/laravel-ws-example.bouma.blog-error.log error;

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}

# FORGE CONFIG (DO NOT REMOVE!)
include forge-conf/laravel-ws-example.bouma.blog/after/*; 
```

Enter fullscreen mode Exit fullscreen mode

*注意:[文档记录了](https://beyondco.de/docs/laravel-websockets/basic-usage/ssl#usage-with-a-reverse-proxy-like-nginx)如何在与你的应用程序相同的域上运行 websockets 服务器的方法，但在不同的路径上，但我个人不喜欢这样，因为这会与你的应用程序路径冲突。然而，这是一种选择！*

下一步是在防火墙上打开端口`6002`,这样它就可以从互联网上到达。你可以从你的 Forge 服务器的 Network 标签中添加一个名为“Websockets”的规则或者一些描述性的东西和 port `6002`，并把 IP 的字段留空(所有的 IP 都允许连接到这个端口)。

*注意:如果您的服务器由 AWS EC2 托管，请确保分配的安全组也允许入站 TCP 流量到达端口`6002`。*

在这之后，只剩下一件事要做了！启动 websockets 服务器作为一个守护进程，这样它会在崩溃或服务器重启时自动重启！

要做到这一点，在 Forge 中，进入服务器的 Daemons 选项卡，用命令`php artisan websockets:serve`添加一个新的守护进程，并将目录设置为服务器上您的站点所在的位置，在我的例子中是`/home/forge/laravel-ws-example.bouma.blog`。如果你用 [Envoyer](https://envoyer.io) / [Deployer](https://deployer.org/) 或者类似的东西进行部署，你可能需要一个像`/home/forge/laravel-ws-example.bouma.blog/current`这样的路径来指向你的应用程序的当前路径。

*注意:这个守护进程不会在每次部署你的应用程序时重新启动，这可能不是一个好主意(因为 websockets 服务器的每次重新启动都会断开所有客户端的连接),但是请记住，新的应用程序密钥或 Laravel WebSockets 包的更新确实需要你重新启动守护进程才能生效，这一点要记住！*

为了完整起见，为了让你的 socket 服务器有一个单独的(子)域，你可以添加一个新的站点(例如`socket.yourapp.com`)，在启用 Let's Encrypt(你甚至不需要将你的应用程序代码部署到站点，它只是一个配置占位符)后，你需要对该站点进行的唯一更改是用来自 [Laravel WebSockets 文档](https://docs.beyondco.de/laravel-websockets/1.0/basic-usage/ssl.html#usage-with-a-reverse-proxy-like-nginx)的位置块替换 NGINX 配置中的位置块。并且在你的 Pusher/Echo 客户端中使用端口`443`而不是`6002`。

## 但是为什么港口有`6000``6002`和`433`，真是乱七八糟！

我听到了！让我解释一下，希望以后一切都有意义。

事情是这样的，在你的服务器上打开一个端口一次只能由一个应用程序来完成(从技术上来说这是不正确的，但是让我们保持简单)。因此，如果我们让 NGINX 监听端口`6001`，我们就不能在端口`6001`上启动我们的 websockets 服务器，因为它会与 NGINX 冲突，反之亦然，因此我们让 NGINX 监听端口`6002`，并让它通过普通 http 代理(毕竟 NGINX 是一个反向代理)所有到端口`6001`(web sockets 服务器)的流量。剥离 SSL，这样 websockets 服务器就不需要知道如何处理 SSL。

因此 NGINX 将处理所有的 SSL 魔术，并以普通的 http 将流量转发到您的服务器上的端口【the websockets 服务器在那里监听请求。

我们没有在`websockets.php`配置中配置任何 SSL，而是将`broadcasting.php`中的`scheme`定义为`http`并使用端口`6001`的原因是为了绕过 NGINX，直接与 websockets 服务器进行本地通信，而不需要 SSL，这样更快(也更容易配置和维护)。

## 关于端口的说明

Websockets 不允许连接任何你能想到的端口...由于[栈溢出](https://stackoverflow.com/a/4314070/1580028)发现它们中的很多都被(浏览器)阻塞了，在测试时我使用了端口`6000`，它似乎也被阻塞了，这就是为什么我使用了端口`6002`，它工作得很好。

我很难弄清楚发生了什么，因为当你使用一个被浏览器阻止的端口时，没有可见的错误被抛出:(但是在查看 pusher 事件时，我看到了一个错误，它含糊地说我选择的端口不允许用于 websockets 连接。

您可以通过在开发人员控制台中运行`window.Echo.connector.pusher.connection.timeline.events`并检查条目来查看 Pusher 客户端的事件。

## 已经给我看代码了！

这就是:[github.com/stayallive/laravel-websockets-example](https://github.com/stayallive/laravel-websockets-example)。这里你可以看到它正在运行:[laravel-ws-example . bouma . blog](https://laravel-ws-example.bouma.blog)。

## 等等！Cloudflare 呢？

那又怎么样？:)

不，真的，这是一个很容易的变化，当你在 Cloudflare 后面时(启用橙色图标)，使 websocket 工作。请注意，他们对你可能访问的连接数量有“限制”，尽管他们似乎很好地处理了过度使用，而不仅仅是黑洞你的网站。

Cloudflare [列出了一些 http(s)端口，您可以使用](https://support.cloudflare.com/hc/en-us/articles/200169156-Which-ports-will-Cloudflare-work-with-)而不是`443`进行 SSL。因此，将上面示例中的端口`6002`更改为端口`2053`将导致 Cloudflare 代理背后的工作环境。如果你想让它和 SSL 一起工作，确保你使用的是列表中的 https 端口，例如`2053`而不是`2052`，因为它不支持 SSL。所有端口都支持 websockets。

我在 Forge 的应用程序中使用了一个原始证书，而不是来自 Let's Encrypt 的证书，并更改了上面提到的端口(不要忘记在防火墙中打开正确的端口)，它只是工作(tm)。

这里可以看到它正在运行:[laravel-ws-example-cloud flare . bouma . blog](https://laravel-ws-example-cloudflare.bouma.blog/)。

## 通知我！

我希望这有助于在你的 Laravel 应用中在 Forge 上设置你自己的 websockets 服务器。让我知道进展如何，你在哪里卡住了/松开了。