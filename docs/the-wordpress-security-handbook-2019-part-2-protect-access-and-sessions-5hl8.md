# Wordpress 安全手册 2019 -第 2 部分:保护访问和会话

> 原文：<https://dev.to/lucagrandicelli/the-wordpress-security-handbook-2019-part-2-protect-access-and-sessions-5hl8>

## **8。始终使用 HTTPS**

HTTPS 协议在用户和服务器之间建立了一个安全的连接，消除了可能的中间人攻击(MITM)。当中间服务更改或获取双方之间交换的信息时，就会发生这些攻击。这是因为 HTTPS 加密用于所有敏感数据。

> 如果你仍然不确定是否使用 HTTPS，[请阅读此](https://letsencrypt.org/)。

为了在您的网站上使用此协议，您必须在您的 web 服务器上安装 SSL 证书，并在管理面板中更改 URL。有很多服务，甚至免费证书提供了许多 hosing 供应商。一劳永逸:[让我们加密 SSL 证书](https://letsencrypt.org/)

还有几个 WordPress 插件可以在你网站的所有资源上强制 HTTPS 连接，以避免在同一页面上显示 HTTP 和 HTTPS 内容时出现警告或错误。最后，你必须强制网站管理面板中的每个新会话使用 SSL 协议。

为此，只需在 wp-config.php 文件中输入以下代码:

```
define('FORCE_SSL_LOGIN', true);
define('FORCE_SSL_ADMIN', true); 
```

Enter fullscreen mode Exit fullscreen mode

> **注意** : *记住，你需要在安装中有一个活动的 SSL，例如由[让我们加密]((https://letsencrypt.org/))T5】提供的 SSL*

* * *

## **9。禁用会话建议**

如上所述，但值得重复的是，一个好的安全策略总是“给”攻击者尽可能少的信息。这个提示将帮助您通过禁用登录页面的建议来最大限度地减少对您网站的访问，如果用户名或密码不正确，默认情况下会显示这些建议:

```
function no_wordpress_login_errors() {
    return "This website is protected.";
}
add_filter( 'login_errors', 'no_wordpress_login_errors' ); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## **10。更改登录访问 URL**

让我们面对现实吧:许多对网站的攻击确实经常以登录页面为目标。这是因为机器人被编程为识别 WordPress 安装，添加路径 */wp-admin* 并访问管理区域。更改这个默认的 URL 主干将会给攻击者增加额外的难度。

在 [WordPress 知识库](https://it.wordpress.org/plugins/)中有几个插件可以让你改变登录页面的位置，例如 mydomain.com/mynewadminurl*。*

 *WPS 插件隐藏登录是执行这项任务最常用的系统之一。然而，有其他的插件用于这个目的，甚至许多安全系统也包括这个功能。

* * *

## **11。限制登录尝试**

您可以将您的网站配置为在用户输入错误登录凭据的次数达到给定次数时，阻止用户访问登录页面几分钟、几小时或永远。这样，机器人将很难通过暴力攻击获得访问权限

一般来说，像 [Wordfence](https://www.wordfence.com/) 这样的安全插件就包含了这个功能，还有以下几个:

*   [限制登录尝试(miniorange)](https://it.wordpress.org/plugins/miniorange-limit-login-attempts/)
*   [限制重新加载的登录尝试次数](https://it.wordpress.org/plugins/limit-login-attempts-reloaded/)
*   [登录器](https://it.wordpress.org/plugins/loginizer/)

> **一些防火墙插件也包含这个功能，比如 [All In One WP 安全&防火墙](https://it.wordpress.org/plugins/all-in-one-wp-security-and-firewall/)。**

* * *

## **12。使用防火墙插件**

防火墙是一种提供额外安全层的软件，能够通过检测和分析入站连接来保护 web 连接或 WordPress 安装。防火墙插件非常有效且易于管理，因为配置是在单一区域进行的。它们通常包括一个 *WAF* (Web 应用防火墙)，一个实时分析和阻止攻击的工具。

目前有三种主要的防火墙插件:

*   [多合一 WP 安全&防火墙](https://it.wordpress.org/plugins/all-in-one-wp-security-and-firewall/)
*   [Wordfence](https://www.wordfence.com/) ( *请注意实时流量功能，这可能会导致服务器过载，使您无法获得服务*
*   [更好的 WP 安全性](https://it.wordpress.org/plugins/better-wp-security/)

安全功能会因安装的插件而异，但通常以下功能始终可用:

*   扫描文件以查找更改、错误和病毒
*   检测和阻止恶意访问的 WAF 防火墙
*   实时交通显示
*   阻止通过 IP 访问网站的工具
*   WordPress 登录页面的验证码和限制登录尝试的功能
*   密码验证
*   访问 WordPress 管理面板的双因素验证
*   阻止特定国家的可能性
*   验证文件和文件夹权限的工具

## **13。使用安全头**

通过实现一系列集成到 web 服务器并发送到浏览器的 HTTP 头来提高网站的安全性。

从 **X-Frame-Options** 标题开始，它可以防止页面在外部框架或 iframe 中打开，从而防止网站上的点击劫持攻击(字面意思为“点击盗窃”)，这种技术会导致用户在看似正常的网站上泄露机密信息。

通过将以下代码行添加到您的*中。htaccess* 文件，你的网站会告诉浏览器框架只能从同一个域打开或者从同一个域起源:

```
Header set X-Frame-Options SAMEORIGIN 
```

Enter fullscreen mode Exit fullscreen mode

如果您的网站集成了第三方服务，您可以指定拒绝对其余服务的访问。例如:

```
Header set X-Frame-Options "ALLOW-FROM https://example.com/" 
```

Enter fullscreen mode Exit fullscreen mode

通过在您的*中添加以下代码行，增强您的网站在旧浏览器上免受 XSS(跨站点脚本)攻击的保护。htaccess* :

```
Header set X-XSS-Protection "1; mode=block" 
```

Enter fullscreen mode Exit fullscreen mode

为了降低 XSS 攻击的风险，请使用浏览器的内容安全策略，该策略指定了网站或第三方可以动态加载的内容。例如，如果您希望您的网站只接受来自同一个域的内容，请将下面一行代码添加到您的*中。htaccess* 文件:

```
Header set Content-Security-Policy "default-src 'self';" 
```

Enter fullscreen mode Exit fullscreen mode

这样，您将阻止从外部来源加载脚本。例如，要为您的特定项目更改变量，使用下面的代码行来允许像 Google Analytics 这样的第三方脚本:

```
header set Content-Security-Policy "script-src 'self' www.google-analytics.com;" 
```

Enter fullscreen mode Exit fullscreen mode

在实现这个头时应该小心，因为它很容易阻塞所需的资源。我建议在单独的浏览器选项卡中使用这个标题执行各种测试，以检查终端上的任何错误。

> **注意** : *如果您之前在服务器中包含了 x-content-security-policy 报头，并且已经过时，您必须删除它，因为如果您同时使用这两个报头，您可能会遇到问题。*

第四个可以用来提高安全性的标题是 *X-content-type-options* ，当预期的 MIME 类型与页面上声明的类型不匹配时，它可以防止您的网站加载不需要的样式和脚本。要添加这种保护，请在您的*中使用下面一行代码。htaccess* 文件:

```
Header set X-Content-Type-Options "nosniff" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## **14。防止 XML-RPC 攻击**

一些应用程序和软件使用**xmlrpc.php**文件与 WordPress 进行通信；其中，WordPress 应用程序或 Outlook 和 Thunderbird 等电子邮件客户端允许“通过电子邮件发布文章”功能。像 Jetpack 或 JSON API 这样的插件也使用 XMLRPC 文件来实现它们的一些功能。

您可以通过修改*中的规则来完全拒绝对 xmlrpc.php 文件的访问。htaccess* 文件，或者在确定不需要的情况下直接删除。拒绝通过。 *htaccess* ，将下面几行代码添加到文件中:

```
# Deny access to XMLRPC.
<Files xmlrpc.php>
Order Deny,Allow
Deny from all
</Files> 
```

Enter fullscreen mode Exit fullscreen mode

也有可能使用插件拒绝对 XMLRPC 的访问，如第 14 点中提到的 [Disable XML-RPC](https://it.wordpress.org/plugins/disable-xml-rpc/) 或 [iThemes Security](https://it.wordpress.org/plugins/better-wp-security/) 。

对于那些绝对需要这个 API 特性的人来说，最好的解决方案是只从你需要访问的 IP 上启用它，拒绝其他人。在这个特定的例子中，将下面几行代码添加到文件*中。htaccess* ，用你想要访问的 IP 地址替换它:

```
<Files xmlrpc.php>
order deny, allow
deny from all
allow from X.X.X.X
</Files> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## **15。禁用 JSON Rest API**

如果你的插件都没有使用 REST API，你可以很容易地禁用它。只需将下面几行代码添加到你的活动主题的*functions.php*文件中，或者使用一个插件:从 WordPress 版本开始，REST APIs 被包含在中央核心中，允许任何开发者与网站进行交互。这使得 WordPress 能够接触到更多不熟悉该平台的开发者，但同时也为可能的攻击，尤其是 DDoS 攻击打开了方便之门。

以下代码阻止从外部源加载脚本。

```
add_filter('json_enabled', '__return_false'); 
```

Enter fullscreen mode Exit fullscreen mode

如果您不想修改代码，您可以使用第 12 点中提到的 iThemes 安全插件，或者防火墙插件，它们将保持 REST APIs 活动，但只允许拥有独占权限的用户访问。

* * *

发现这篇文章有错别字？[做个 PR](https://github.com/lucagrandicelli/wordpress-security-handbook-2019) 。

* * *

> **嘿！我还提供关于[开发列表](https://dev.to/listings)的 Wordpress 指导。
> 随便问！***