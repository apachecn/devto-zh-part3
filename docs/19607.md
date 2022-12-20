# quick auth PHP–一个用于 web 认证的脚本。多用户。不需要数据库。

> 原文：<https://dev.to/llagerlof/quickauthphp--one-script-for-web-authentication-multi-user-no-database-required-5fa2>

## 目标

有时你有一个小的 web 应用程序，你想保密，但是没有时间实现一个合适的数据库认证系统，也没有时间*。htaccess* 适合你的需求(或者你讨厌原生浏览器认证，像我一样)。如果你是这样，QuickAuthPHP 是一个超级快速的解决方案。

## 项目网站

[github.com/llagerlof/QuickAuthPHP](https://github.com/llagerlof/QuickAuthPHP)

## 它有什么作用？

*   将单个登录表单添加到服务器上的任何网页。
*   您可以拥有多个登录名和密码。

## 好东西

*   一个易于配置和安装的脚本。
*   不碍事。你只需要在你的网页中调用 _once()脚本。
*   没有依赖。
*   无需更改页面中的 HTML、Javascript 或 CSS。
*   安全。

## 你会需要什么？

*   PHP 5.5 以上(最好是 7.x)的工作 web 服务器。
*   文件[auth.php](https://github.com/llagerlof/QuickAuthPHP)。