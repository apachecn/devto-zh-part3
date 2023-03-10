# nginx:同一服务器上的 2 个域(dns)

> 原文：<https://dev.to/matalmeida/nginx-2-dominios-dns-no-mesmo-servidor-4kij>

[![NGINX](img/461a487f41db99353b6e50a59ca5b0ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7plNLh4u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2A5o1SQhA0NhMSkme9LLdaDQ.png)

这几天我在 DigitalOcean 学习如何使用 github student pack(github 学生包)的快捷批处理，我一直在想如何在同一快捷批处理中运行两个或更多站点。我想得太快了不可能每个网站都必须在不同的服务器上运行。我浏览了几个网站，首先发现了一个巴西人的博客(10/10 这个博客，访问 ai)，他教我如何为 1 个应用程序创建反向代理。

### **1 个应用程序(使用 ubuntu)的反向代理摘要**

首先必须安装 nginx 并激活它:

```
$ sudo apt-get install nginx 
$ sudo systemctl enable nginx
$ sudo systemctl start nginx 
$ sudo systemctl status nginx 
```

Enter fullscreen mode Exit fullscreen mode

如果在访问服务器 IP 或您配置的任何 DNS 时都成功，则会出现类似以下内容的页面:

[![tela nginx](img/424b80c5418453c0fb17dc67a3ec6355.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T_i7neY4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AT4MTsLk9EEyFdm1Iw1FSVw.png)

现在，我们将为您的应用程序配置反向代理，该代理可能已经在服务器上的某个端口上运行。我们将创建一个配置文件

```
$ sudo nano /etc/nginx/sites-avaliable/meusite1.com 
```

Enter fullscreen mode Exit fullscreen mode

并粘贴以下代码:

```
server {
    listen       80;
    server_name  localhost;

    location / {
        proxy_pass  http://127.0.0.1:<porta-do-servidor-site1>;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

备注:不要忘了`;`如果以后不坏的话。

现在，我们将删除 engine 附带的默认配置文件，并从刚创建的配置文件中创建符号链接(symlink)，然后重新启动 engine

```
$ sudo rm /etc/nginx/sites-enabled/default
$ sudo ln -s /etc/nginx/sites-available/meusite1.com /etc/nginx/sites-enabled/meusite1.com
$ sudo systemctl restart nginx 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您访问服务器的 IP 或 DNS 时，无需明确使用应用程序正在运行的端口号。

* * *

### **克里安多代理 reverso pro segundo sub domino**

这部分会更快。你会做基本上和前面步骤中已经做的一样的事情。而是创建一个不同的配置文件。

你可以把已经做好的文件复制到第一个:

```
$ sudo cp /etc/nginx/sites-avaliable/meusite1.com /etc/nginx/sites-avaliable/meusite2.com
$ sudo nano/etc/nginx/sites-avaliable/meusite2.com 
```

Enter fullscreen mode Exit fullscreen mode

并修改第二个应用程序正在运行的端口号:

```
server {
    listen       80;
    server_name  localhost;

    location / {
        proxy_pass  http://127.0.0.1:<porta-do-servidor-site2>;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在只需将此文件放在“**sites-enabled”**文件夹中，并在 nginx 中重新启动:

```
$ sudo ln -s /etc/nginx/sites-available/meusite2.com /etc/nginx/sites-enabled/meusite2.com
$ sudo systemctl restart nginx 
```

Enter fullscreen mode Exit fullscreen mode

好了！你的两个领域现在正在运作。只需使用第二个应用程序的 IP 和端口在 DNS 中配置子网即可。

* * *

Vlw，如有疑问，请致电 twitter [@mat_almeida](https://twitter.com/mat_almeida)

这是我之前在媒体上发表的一篇帖子中的‘t0’，我想把它带给 dev.to 会很有帮助