# 使用 SSL 的 Dockerized PHP 应用程序

> 原文：<https://dev.to/samuanv/dockerized-php-application-with-ssl-3a38>

大家好！

我正在将几年前开发的一个旧系统迁移到一个容器化的环境中，我需要一些 SSL 方面的帮助，以便通过 HTTPS 为它提供服务。我对这个话题的了解很少。

该系统由两个容器组成:

*   前端。AngularJS 应用程序位于 NGINX 图像上方。
*   后端。PHP API REST 服务于图片上方 [php:5.6-apache](https://github.com/docker-library/php/blob/c77c579341cfbfee90e669535ea3057679a1005b/5.6/stretch/apache/Dockerfile) 。

两个容器都运行在一个 EC2 实例中，或者由 Docker Swarm 声明。该实例链接到一个域。我知道我可以从[获得证书，让我们加密](https://letsencrypt.org/)，但是我的问题如下:

*   我应该对前端和后端使用相同的证书吗(两者都在同一个域中，但端口不同)？
*   有没有办法自动续费或者集成到我的 pipeline 里？或者在容器环境中有什么方法来处理这个问题。

最后，如果有人读过解释这一点的文章，请写在评论中:)

非常感谢你的帮助！