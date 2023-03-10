# 用 Nginx 托管一个. Net 核心应用，让我们加密

> 原文：<https://dev.to/ianknighton/hosting-a-net-core-app-with-nginx-and-let-s-encrypt-1m50>

在上一篇文章中，我们构建了一个简单的。Net 核心应用程序来处理我们的活动注册支付处理。它在我们的本地机器上像冠军一样工作，但是如果没有人能访问它，它对我们没有任何好处。

今天，我们将向公众开放。

我主要是从微软文档中的这篇有用的文章开始的。然而，我发现了一些我必须解决的问题。

## App 的快速更新

使用 Nginx，我们将需要能够反向代理，所以我们需要在我们的项目中添加一点代码来处理这个问题。在`startup.cs`中，我们将增加`Configure`方法。

```
app.UseForwardedHeaders(new ForwardedHeadersOptions
    {
        ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
    }); 
```

Enter fullscreen mode Exit fullscreen mode

您可能需要添加 using 语句。

```
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.HttpOverrides; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以继续构建服务器了。

## 设置服务器

我从一台安装了 Ubuntu 18.04 的 Linode 服务器开始。在我的情况下，这个页面只需要提供三个月，不会看到大量的流量，所以小尺寸和较小的成本(每月 5 美元)真的很适合我正在尝试做的事情。

完成正常设置(更新、升级、安全 ssh 等...)然后我们会安装我们需要的其他部分。

### 在服务器上安装 Dotnet

为此，我再次引用了微软的文档，但是为了简洁起见，我将命令浓缩在一起。

```
wget -q https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo add-apt-repository universe
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install dotnet-sdk-2.2 
```

Enter fullscreen mode Exit fullscreen mode

### 复制代码并构建

根据您希望达到的程度，您可能希望建立一个完整的 CI 渠道。在我的例子中，我不打算做任何迭代，所以我只是使用`scp`复制代码并在服务器上运行它。

```
scp -r ~/path/to/directory ssh@server:~/appname 
```

Enter fullscreen mode Exit fullscreen mode

该命令(一旦填充了您的信息)应该将整个文件夹从您的机器复制到 SSH 用户主目录中的服务器。

SSH 进入服务器并构建/发布应用程序。

```
dotnet build
dotnet publish --configuration Release 
```

Enter fullscreen mode Exit fullscreen mode

Publish 应该将所有内容移动到`bin/Release/netcore2.2/`目录下的一个文件夹中。

一旦完成并成功，我就使用符号链接将其放入`/var/www`。没必要做，但这是老习惯了。

```
sudo ln -s ~/appname/bin/Release/netcore2.2/publish /var/www/appname 
```

Enter fullscreen mode Exit fullscreen mode

这将主目录中的文件夹链接到了`/var/www/`目录，因此如果您确实需要进行修复，就不用担心四处复制了。

### 安装和设置 CertBot

Certbot 非常简单，它是我曾经写过的一个漂亮的工具。

首先通过`apt`安装。

```
sudo apt install -y certbot 
```

Enter fullscreen mode Exit fullscreen mode

完成后，我们可以申请证书。我更喜欢用`standalone`的方法。

```
sudo certbot certonly --standalone -d yourdomain.com -d www.yourdomain.com 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，您应该能够运行`sudo certbot certificate`并看到证书在您机器上的位置。把它放在某个地方很重要，我们下一步会用到它。

### 安装并配置 Nginx

我们有代码，我们有证书，现在我们只需要把它提供给崇拜它的大众。

首先，安装 Nginx:

```
sudo apt install nginx 
```

Enter fullscreen mode Exit fullscreen mode

第二，确保你可以通过防火墙访问它。假设您正在使用`ufw`，我们可以只更新权限。

```
sudo ufw allow 'Nginx Full' 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您导航到您的服务器的 IP 地址，您应该会看到“欢迎使用 nginx”屏幕。

一旦你确认你可以访问服务器，你需要确保你的域名指向这个 IP。这将因你的 DNS 提供商的不同而大相径庭，但我相信你。

有了域，我们现在可以为我们的站点设置 Nginx 了。我将省去所有这些如何工作的无聊细节，但这里是我们需要做的。

在`/etc/nginx/sites-available/` :
中创建文件

```
sudo nano /etc/nginx/sites-available/yourdomain.conf 
```

Enter fullscreen mode Exit fullscreen mode

在文本编辑器中，以此配置复制:

```
server { 
 listen 80;  server_name yourdomain.com;  return 301 https://$host$request_uri;
}

server {
 listen 443;  server_name yourdomain.com;    ssl_certificate           /etc/letsencrypt/live/yourdomain.com/fullchain.pem;  ssl_certificate_key       /etc/letsencrypt/live/yourdomain.com/privkey.pem;    ssl on;  ssl_session_cache  builtin:1000  shared:SSL:10m;  ssl_protocols  TLSv1 TLSv1.1 TLSv1.2;  ssl_ciphers HIGH:!aNULL:!eNULL:!EXPORT:!CAMELLIA:!DES:!MD5:!PSK:!RC4;  ssl_prefer_server_ciphers on;    gzip  on;  gzip_http_version 1.1;  gzip_vary on;  gzip_comp_level 6;  gzip_proxied any;  gzip_types text/plain text/html text/css application/json application/javascript application/x-javascript text/javascript text/xml application/xml application/rss+xml application/atom+xml application/rdf+xml;  gzip_buffers 16 8k;  gzip_disable “MSIE [1-6].(?!.*SV1)”;    access_log  /var/log/nginx/yourdomain .access.log;   location / { proxy_pass            https://localhost:5001;  proxy_http_version 1.1;  proxy_set_header   Upgrade $http_upgrade;
 proxy_set_header   Connection keep-alive;  proxy_set_header   Host $host;
 proxy_cache_bypass $http_upgrade;
 proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
 proxy_set_header   X-Forwarded-Proto $scheme;
 } 
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，我在这里很简短，但是这应该做了我们需要的所有事情，将请求发送到`yourdomain.com`，将它们转发到 SSL，并将它们连接到运行在服务器上的应用程序。

为了启用它，我们将创建另一个符号链接，然后重启 Nginx。

```
sudo ln -s /etc/nginx/sites-available/yourdomain.conf /etc/nginx/sites-enabled/yourdomain.conf
sudo systemctl restart nginx 
```

Enter fullscreen mode Exit fullscreen mode

为了确保一切正常，我们将启动该应用程序，并尝试通过浏览来访问它。

```
dotnet run /var/www/yourapp/yourapp.dll 
```

Enter fullscreen mode Exit fullscreen mode

在您确认这是可行的之后，您可以关闭应用程序。下一步是让它自己工作。

### 设置系统 d

是的，在`systemd`周围有一些[悲剧](https://www.youtube.com/watch?v=o_AIw9bGogo)，但它仍然是方便的。

为您的应用程序创建一个`.service`文件:

```
sudo nano /etc/systemd/system/yourapp.service 
```

Enter fullscreen mode Exit fullscreen mode

将以下信息添加到文件中:

```
[Unit]
Description=Event Registration Example

[Service]
WorkingDirectory=/var/www/yourapp
ExecStart=/usr/bin/dotnet /var/www/yourapp/yourapp.dll
Restart=always #  Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

我在微软文档中唯一的改动是删除了`User=www-data`行。虽然拥有有限范围的用户是最佳实践，但是我不需要担心这个服务作为根用户运行。这是一个非关键系统，除了重定向之外几乎什么都不处理，所以我认为它足够安全。

现在，我们可以通过 systemd 启用并启动服务。

```
sudo systemctl enable yourapp.service
sudo systemctl start yourapp.service
sudo systemctl status yourapp.service 
```

Enter fullscreen mode Exit fullscreen mode

如果一切按计划进行，您应该会收到一个正在运行的状态，并且能够访问您的页面。如果一切顺利，你仍然可以访问日志。

```
sudo journalctl -fu yourapp.service 
```

Enter fullscreen mode Exit fullscreen mode

*就是这样！*

希望这有所帮助。有很多方法可以优化或更好地保护这一点，但对于这种简单的事情，我并没有太努力。它已经运行了几个星期，处理了一些我们提前接触到的人的付款。

我确实做了一些微妙的造型变化，但仅此而已。完美的工作，并相对容易修改前进。

感谢你的到来！