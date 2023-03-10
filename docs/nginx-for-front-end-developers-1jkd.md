# 面向前端开发人员的 Nginx

> 原文：<https://dev.to/ganeshmani/nginx-for-front-end-developers-1jkd>

请阅读我博客上的这篇文章来支持我的工作:[Nginx For Front-end Developers](https://cloudnweb.dev/2019/05/nginx-for-front-end-developers/)

这篇文章用一种更简单的方式向前端开发人员解释 Nginx

Nginx 是一个强大高效的 web 服务器，主要解决了 [C10k 问题](https://en.wikipedia.org/wiki/C10k_problem)。它能以极快的速度提供数据。我们可以将 Nginx 用于其他目的，比如反向代理、负载平衡和缓存文件。

你可以问我作为一个前端开发人员为什么需要学习 Nginx。在现代前端开发世界中，你在前端工作的一切都被编译成一个 HTML、JS 和 css 文件。因此，了解 web 服务器在生产中如何处理您的文件将会非常有用。

### Nginx 架构:

基本的 nginx 架构由一个主进程和它的工作进程组成。主服务器应该读取配置文件并维护工作进程，而工作进程执行实际的请求处理。

[![](img/1f0225ec2b7f69e2bdf5bd354f0271ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QIrHFFjG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/05/Screen-Shot-2019-05-26-at-1.34.56-AM-1024x580.png)

Nginx 是一种[主从](https://en.wikipedia.org/wiki/Master/slave_(technology))、[事件驱动](https://en.wikipedia.org/wiki/Event-driven_architecture)、[非阻塞](https://anturis.com/blog/nginx-vs-apache/)架构。

*   **主** -主负责维护和验证配置。它还负责创建和终止工作进程。
*   工作进程负责处理共享套接字中的请求。每个工作进程都可以处理成千上万个请求，因为这些进程是单线程和非阻塞的
*   **缓存加载器** -缓存加载器根据请求元数据用磁盘中存在的数据更新工作实例。
*   **缓存管理器** -缓存管理器负责验证和配置缓存到期

### Nginx 安装:

让我们看看如何在开发机器上安装 nginx。我用的是 macOS。放心安装在 linux，windows。

要在 mac 上安装 nginx，你需要在你的机器上安装 [Homebrew](https://brew.sh) 。

注意:对于 Ubuntu 或 windows，请遵循本官方[安装指南](https://www.nginx.com/blog/setting-up-nginx/)

```
$ brew install nginx
$ nginx -v
```

现在，你可以在屏幕上看到这样的东西

[![](img/13af553c711d6cabe44bc1b8b62d10da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o3SK4c69--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/05/Screen-Shot-2019-05-26-at-1.13.48-AM.png)

并且要检查 web 服务器是否在运行，在浏览器中运行 **http://localhost:8080** 。您应该会看到 nginx 默认页面

[![](img/7e68e23ba658a72363dd2fa16ba8bad3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6NMJrObY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/05/Screen-Shot-2019-05-26-at-1.39.05-AM-1024x461.png)

瞧啊。！！！！我们已经成功地在本地机器上安装了 nginx。

现在我们将看到如何使用 nginx 服务器部署 React 应用程序。我们将部署一个来自 [Shante Autsin](https://www.linkedin.com/in/shante-austin/) 的[随机报价生成器](https://github.com/ShanteDenise/React-Random-Quote-Generator)应用程序

源代码:[https://github . com/shante Denise/React-Random-Quote-Generator](https://github.com/ShanteDenise/React-Random-Quote-Generator)

### 使用 nginx 服务器部署应用程序

在机器上安装了 nginx 之后，我们可以访问位于 **/usr/local/etc/nginx** 位置的 nginx 配置文件

[![](img/bc3f8e90e7ff4ca48affc46b0344bf6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PczrVpwx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/05/Screen-Shot-2019-05-26-at-1.22.07-AM-1024x278.png)

在配置服务器之前。我们需要构建 react 应用程序并将文件移动到 nginx 目录。

在 macOS 中，nginx 的默认文件位置是 **/usr/local/var/www** 。您需要将构建移动到 nginx 文件夹中。

```
$ sudo mv -v /<build directory> /usr/local/var/www/demo
```

之后，我们需要在 **nginx.conf** 文件中配置服务器

```
$ sudo nano nginx.conf
```

主要是 nginx 配置文件中有两个块。 **http** 和**服务器**阻塞。nginx 配置文件只有一个 http 块，我们可以在 http 块中创建几个服务器块。我们将在另一篇文章中看到 nginx 的指令和块概念。

在配置文件中添加以下代码

```
http {
         ...
  server {
        listen       8080;
        server_name  localhost;
    location / {
            root   /var/www/demo;
            index  index.html index.htm;
        }
```

之后，需要重启 nginx 服务。

```
$ sudo brew services restart nginx
```

最后，在浏览器中运行 **localhost:8080** ，您将看到应用程序运行如下。

<figure>[![](img/e0ac6fe28781a770b65674507dec47f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RKo8QLSf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/05/Screen-Shot-2019-05-26-at-3.08.54-AM-1024x595.png) 

<figcaption>随机报生成器</figcaption>

</figure>

耶！！！..现在应用程序使用 nginx web 服务器运行。

在下一篇文章中，我们将看到 nginx 是如何工作的，以及我们如何定制 nginx 服务器。！！

了解更多关于 [web 开发的信息](https://cloudnweb.dev/category/web-dev/)