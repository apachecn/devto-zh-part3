# 邮包取送器

> 原文：<https://dev.to/robertomanchado/hello-post---------546g>

有时我们需要测试一下我们的方法或项目
，在这种情况下我们一定会发电子邮件。

不管是在测试或开发环境中，还是在以往的经验中，
协议一直是，创建一个虚构的电子邮件并将其发送到您的收件箱中，或者
电子邮件、protonmail、yahoo？，，，，等等。

非常专业。

最近，我一直在使用一种工具——[【邮件捕手】](https://mailcatcher.me/)，这正是
的用途。
这是一台 **SMTP** 服务器，它拦截我们发送到端口
的输出消息；默认设置为**[【http://127 . 0 . 0 . 1:1025](http://127.0.0.1:1025)**。

以 instalarlo 的形式，más cómoda es utilize and a image de Docker**(Docker file)**utilize and
un ficher**。yml**y docker-作曲 instalado。Esto es:

```
version: "3"
services:
  mailcatcher:
    restart: on-failure:10
    image: dockage/mailcatcher:0.7.1
    ports:
    - "1086:1080"
    - "1030:1025" 
```

我们主机上的端口 **1086** ，对应于坞站容器
1080，而 **1030** 对应于容器 **1025** 。

因此，为了能够向这个“虚拟”收件箱发送电子邮件，我们可以配置**我们的文件**。env**(假设我们与 symfony 4 合作)。∞如下:**

```
###> symfony/mailer ###
MAILER_DSN=smtp://127.0.0.1:1030
###< symfony/mailer ### 
```

我们启动容器:

```
docker-compose up
```

并且我们现在可以从 [SwiftMailer](https://swiftmailer.symfony.com/docs/introduction.html) 、[**symfony**的 Mailer](https://symfony.com/doc/current/mailer.html) 组件、
PHP 或其他电子邮件发送工具发送电子邮件，这些电子邮件将被
截获

罗伯特·曼查多