# 适用于 php 微服务的解决方案

> 原文：<https://dev.to/caiofior/a-suitable-solution-for-php-microservicies-429e>

我是一个 2009 年从零开始诞生的老 php web 应用的维护者(没有框架，sic。)托管在 Debian 服务器上。

在这段时间里，它已经升级，增加了新的功能，现在是一大堆代码。但这很有效。

有一个重新设计用户界面的计划，至少是客户端。这可能是模块化应用程序的机会，涵盖许多领域(与客户的关系，与供应商的联系，金融账户，客户关系管理)。

这可能是一个将庞大的应用程序转换成模块化应用程序的机会。

一个想法是:使用微服务！

问题是:php 中适合的解决方案是什么？

此刻我找到了这些解决方案:

1)**[react PPH](https://reactphp.org/)**，用[作曲](https://getcomposer.org/)很容易安装，但需要很多依赖项(如 [Symfony](https://symfony.com/) )才能工作；

2) **[Swoole PHP](https://www.swoole.co.uk/)** ，是 [PECL](https://pecl.php.net/) 必须安装的 PHP 模块。它很快，但安装在 Debian 生产服务器上并不容易；

3) **[Amphp](https://amphp.org/amp/)** ，用 composer 安装很容易但有且不需要很多依赖才能工作；

4) **Web 服务器**，它处理不同端口上的请求，还捕捉上传、删除请求。

有什么建议可以很容易地应用在 Debian 机器上吗？