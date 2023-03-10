# 在 DigitalOcean 的 Docker 容器中运行 Ghost

> 原文：<https://dev.to/stefanbc/run-ghost-inside-a-docker-container-on-digitalocean-3m1a>

最近我为《幽灵》创作了一个新主题，你可以在这里找到并且现在可以在我的[网站上体验](https://stefancosma.xyz)。我想要一种展示它的方式，这样任何想要贡献它或者只是简单使用它的人都可以感受一下它在真实环境中的样子。

最初我看了看 Heroku，但我很快就陷入了一个错误的兔子洞，因为 Docker 与 Heroku 并不友好，我不喜欢我不得不穿过铁圈才能保持应用程序在线。请记住，这是一个开源项目，不得不为保留 Heroku 应用程序付费不是我当时准备做的事情。

但是，斯特凡，你已经有一个服务器，在那里你的网站！你为什么不用那个？最初，我想让我的服务器远离其他系统，只用于我的网站。我改变主意了，我们在这里。

背景故事讲够了，让我们开始实际实现吧。下面是我当前的堆栈和我遵循的适合我的设置的过程。它可能对您和您的堆栈有不同的作用。

我的整个堆栈是这样的:

1.  我注册了`stefancosma.xyz`域名，并使用 Cloudflare 作为 DNS 提供商。
2.  网站本身是由 DigitalOcean droplet 托管的，运行他们的 Ghost 图像(一个旧版本，但我一直保持更新)。

没什么特别的，对吧？

所以，我做的第一件事就是让 Docker 做我的水滴。运行完`apt-get update`后，我用这个安装了 Docker:

```
apt-get install docker.io 
```

一旦完成，我就为 Ghost 创建了一个新的 Docker 容器。幸运的是，已经有一个幽灵的 Docker 图像。你可以点击查看[。](https://hub.docker.com/_/ghost)

现在是棘手的部分。我运行这个命令向外界公开 Docker 容器中的 Ghost 实例。

```
docker run -d --name name_your_container -p 3001:2368 -e url=https://ghost_url ghost 
```

因此，该命令的作用是运行一个具有自定义名称的新容器，并在主机的`3001`端口上公开 Ghost 端口`2368`。当然，如果你愿意，你可以在你的机器上使用不同的端口。另一件很酷的事情是，这个 Docker 映像接受任何 Ghost 配置参数作为参数，在这里找到了。码头工人的形象名称是`ghost`。

所以现在，如果您访问 droplets IP，然后访问`3001`端口，您应该会看到一个新的 Ghost 实例启动并运行。

我做的最后一件事是在 Nginx 中创建一个新的子域，并将其添加到 Cloudflare 中。我为 Nginx 使用的配置类似于下面的配置:

```
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    server_name subdomain.domain.com;

    ssl_certificate certificate.cert;
    ssl_certificate_key key.key;
    include conf.conf;

    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:3001;

    }

    location ~ /.well-known {
        allow all;
    }

    client_max_body_size 50m;
} 
```

除了上面的配置，我还添加了一个 CNAME 记录，它的子域名指向根域。

仅此而已。一切顺利。

没什么异常。你会怎么做？发推特 [@stefanbc](https://twitter.com/stefanbc) 或者发邮件给我 [hello@stefancosma.xyz](mailto:hello@stefancosma.xyz) 。

直到下一次，代码长，繁荣。