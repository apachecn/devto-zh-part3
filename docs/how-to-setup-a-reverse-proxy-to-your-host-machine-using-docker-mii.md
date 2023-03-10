# 如何使用 Docker 设置主机的反向代理

> 原文：<https://dev.to/kevbradwick/how-to-setup-a-reverse-proxy-to-your-host-machine-using-docker-mii>

最近，我发现自己需要一个用于本地开发环境的反向代理。我有两个应用程序运行在不同的端口上，我希望它们看起来是由一个顶级域提供服务的。当您希望跨子域共享 cookies 时，这可能很有用。

例如，我在端口 8000 上运行了一个 Django 应用程序，在 8080 上的 Webpack dev 服务器上运行了一个 React 前端。我想分别从 **api-app.localhost** 和 **web-app.localhost** 访问它们。

很自然地，我向 Docker 寻求解决方案，这就是我想到的。下面是 Docker 合成文件，它使用 Alpine Nginx 映像来设置反向代理。