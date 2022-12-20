# 我为什么要关心 NGINX？

> 原文：<https://dev.to/raddevon/why-should-i-care-about-nginx-4g9g>

我童年最美好的回忆之一是去玩具反斗城买一个电子游戏。尽管这个过程很奇怪，但我仍然深情地想起它，因为这是玩这个全新游戏的期望的一部分。

过程是这样的。首先，你会去电子游戏区，在那里你会发现满墙都是塑料卡片，上面有每个游戏的封面。翻开卡片，你可以看到游戏盒的背面。一旦你找到了你想要的游戏，你就从挂在游戏卡下面的口袋里抽出一张纸条，上面写着游戏的价格和 UPC。

[![Toys "R" Us game slips](img/342891e2656e624cf4ef6a0527c55208.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AlN_tDup--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raddevon.com/wp-content/uploads/2018/12/toys-r-us-game-slips.jpg)

你拿着这张单子通过收银台，为你的游戏付钱。然后，你会走出收银台，拿着你的游戏卡到顾客接待室的窗口。在那里，一名员工会拿着你的纸条，从架子上拿起游戏，从窗口递给你。

[![Toys "R" Us customer pickup](img/8f9f8cc8d18d5dab8d212f6ab3320819.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5SwtSDil--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raddevon.com/wp-content/uploads/2018/12/toys-r-us-customer-pickup.jpg)

## NGINX 是什么？

这一切和 [NGINX](https://nginx.org/) 有什么关系？NGINX 是一个网络服务器。这是一个运行在计算机上的软件，**监听 HTTP 请求并完成它们**。

它的作用类似于玩具反斗城在提货室工作的员工。你先提出一个请求。在玩具反斗城，这是你购买游戏的单据。在网络上，这是一个 [HTTP](https://dev.to/why-should-i-care-about-http/) 请求。

就像客户提货员工一样，服务器知道去哪里满足该请求。它获得响应，并通过“窗口”将其传递回发出请求的用户。

> 你想成为一名专业的网络开发人员吗？我们聊聊吧。在我的网站上注册一个免费的辅导课程 [Rad Devon](https://raddevon.com/) 我会帮助像你这样的人从💩网络开发人员的工作！

## 为什么我需要了解 NGINX？

NGINX 不是唯一的 web 服务器，但它是一个非常受欢迎的服务器，并且正在上升。它是开源的，这意味着你可以在自己的网站上免费使用它。它也是开源的，意味着你可以自己扩展它或者修复错误。尽管它整体排名第二(在阿帕奇之后)，但在流量最大的网站中**它是[第一](https://w3techs.com/technologies/cross/web_server/ranking)。**

作为一名 web 开发人员*，如果*不了解 web 服务器**，你可以做很多事情，但是你最终会碰到天花板**。如果你需要从一个 URL 重定向到另一个，那是不可能的。如果你想让服务器上的/blog 成为 Python app，让/chat 成为 Node app，那是不行的。如果您想在请求中添加标题，您必须配置 web 服务器来完成这项工作。

## NGINX 配置示例

NGINX 可以帮助的一个真正常见的用例是从一台服务器驱动两个(或更多)具有不同域的站点。NGINX 配置可能是这样的:

```
server {
    listen 80;
    server_name www.raddevon.com;
    root /var/www/raddevon;
}

server {
    listen 80;
    server_name www.radworks.io;
    root /var/www/radworks;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个配置创建了两个虚拟服务器，都在端口 80 上侦听。(这是默认的 HTTP 端口。)如果一个 HTTP 请求进入 raddevon.com 的一个资源，NGINX 将在那个服务器的根路径中寻找那个资源(在这个例子中是`/var/www/raddevon`)。如果请求 radworks.io 上的资源，NGINX 会在`/var/www/radworks`寻找这些资源。

这两个都是静态站点，但是您可以非常容易地运行一个节点 web 服务器，并代理其中一个或另一个。代理允许 NGINX 成为中间人，将请求传递给运行在同一台机器上的另一个 web 服务器。 [Express](https://expressjs.com/) 举例来说，默认设置一个监听端口 3000 上的请求的服务器。如果我将 Rad Devon 作为一个 Express 应用程序运行，我可以接受任何针对 raddevon.com 的请求，并将这些请求代理到同一台机器上的端口 3000。这可能是这样的:

```
server {
    listen 80;
    server_name www.raddevon.com;
    location / {
        proxy_pass http://127.0.0.1:3000/;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我去掉了 root 指令，取而代之的是一个 location 块，它定义了当有人在某个地址点击 raddevon.com 时 NGINX 应该做什么(在本例中是由`/`指示的根 url)。我使用了`proxy_pass`指令来指定它应该将请求发送到位于 [http://127.0.0.1:3000](http://127.0.0.1:3000) 的服务器。127.0.0.1 是环回地址，这意味着它指的是这个网络接口(“这个”意味着发出请求的那个)。3000 再次成为 Express 用于其 web 服务器的默认端口。

我可以在同一台机器上运行两个不同的 Express 应用程序，方法是将其中一个的端口改为 3000 以外的端口。3001 就可以了。然后，我可以在 NGINX 配置中使用`server`块从两个不同的域进行代理，或者我可以通过在单个`server`块中使用多个`location`块从同一域的不同路径代理到它们。

## 如何配置 NGINX

同样地，你的 HTML 是带有`.html`扩展名的文件中的简单文本，NGINX 配置文件是简单的文本文件，但是没有`.txt`扩展名。基本配置文件被命名为`nginx.conf`。一旦安装了 NGINX，它在计算机上的位置会根据您运行的操作系统而变化，但是`/etc/nginx/nginx.conf`是文件的一个常见位置。如果这不起作用，搜索“nginx 配置”应该可以找到。

在您选择的编辑器中打开该文件，并开始配置您的服务器。NGINX 文档可能令人望而生畏，但如果你想更深入地了解你的配置，它们仍然是一个极好的资源。

## 从了解 NGINX 中快速取胜

虽然不一定要理解像 NGINX 这样的 web 服务器是如何构建 web 的，但它确实给了你一些有用的超能力，就像我上面提到的一样:创建重定向的能力，根据路由代理到不同应用程序的能力，操纵发往这些服务器的请求头的能力，以及从一台服务器运行多个域的能力。

简而言之，理解 NGINX 并知道如何配置它可以让您完全控制 web 服务器的行为方式。**通过学习 NGINX 突破那个天花板**！