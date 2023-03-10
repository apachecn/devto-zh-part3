# 如何公开您的本地开发服务器

> 原文：<https://dev.to/jkostolansky/how-to-expose-your-local-development-server-3h69>

如果您需要将您的本地开发 web 服务器公开给 Internet，有多种工具可供您使用。比如: [ngrok](https://ngrok.com) ， [pagekite](https://pagekite.net) ， [forward](https://forwardhq.com) ， [localtunnel](https://localtunnel.me) ，...

或者，如果你有自己的 VPS，你可以免费建立自己的。它可以支持 SSL 并在 NAT 后工作。以下是方法。

## 服务器设置

你需要在 VPS 上安装一个 nginx 服务器和一个指向它的域名，比如`dev.example.com`。

然后添加这个(或者类似的)nginx 配置:

```
server {
    listen 80;
    server_name dev.example.com;
    rewrite ^ https://dev.example.com$request_uri? permanent;
}

server {
    listen 443 ssl http2;

    server_name dev.example.com;

    access_log off;
    error_log off;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header Host $http_host;
    }

    ssl_certificate /path/to/ssl/dev.example.com/fullchain.pem;
    ssl_certificate_key /path/to/ssl/dev.example.com/privkey.pem;
} 
```

Enter fullscreen mode Exit fullscreen mode

有趣的部分是`proxy_pass`。它会将`dev.example.com`指向服务器上的本地端口`8000`。

确保在`/etc/ssh/sshd_config`中允许以下选项，并在需要时重新加载 SSH 服务器:

```
GatewayPorts clientspecified 
```

Enter fullscreen mode Exit fullscreen mode

## 本地设置

现在您需要在本地机器和服务器之间创建一个 SSH 隧道。

在您的开发机器上，将这个函数放在您的`.bashrc`文件中(如果您使用的是 ZSH，则放在`.zshrc`)。在这个例子中，`me`是一个远程 linux 用户，通过 SSH 访问 VPS。

```
function devtunnel {
    ssh -nNT -R "127.0.0.1:8000:localhost:${1}" me@dev.example.com
} 
```

Enter fullscreen mode Exit fullscreen mode

这将把远程端口`8000`转发到作为参数指定的本地端口；`-n`防止从 stdin 读取，`-N`表示不想执行远程命令，`-T`禁用伪 tty 分配，`-R`表示反向端口转发。

## 用法

完成这个设置后，公开您的本地开发服务器就很容易了。如果您是 Rails 开发人员，您将在端口`3000`上运行本地 web 服务器。要公开它，在本地运行这个:

```
devtunnel 3000 
```

Enter fullscreen mode Exit fullscreen mode

之后，您(或其他人)可以访问`dev.example.com`来访问您的本地服务器。尽情享受吧！

* * *

原文:[如何公开你的本地开发服务器](https://www.kostolansky.sk/posts/expose-your-local-development-server/)