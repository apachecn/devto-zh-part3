# Wordpress 安全手册 2019 -第 3 部分:强化你的 Wordpress 安装

> 原文：<https://dev.to/lucagrandicelli/the-wordpress-security-handbook-2019-part-3-harden-your-wordpress-installation-d14>

## **16。明智地选择主题和插件**

插件和主题是强大的外部资源，可以帮助你增加 WordPress 网站的功能。在 WordPress 的官方知识库和其他网站上有数十万个可用的。不幸的是，并不是所有的都被检查，因此构成了严重的安全问题。通常，我们在安装插件之前不会对功能和代码进行深入检查，未经验证的插件可能会导致安全漏洞和冲突。

> 仅从 [WordPress 知识库](https://it.wordpress.org/plugins/)和可信来源下载插件和主题。

所以，在选择你的下一个插件或主题之前，我想给你一些建议:

*   看一下评论，下载数，评论；
*   检查最后一次更新的日期，以确定开发是否仍然有效；
*   寻找作者并检查存储库中的每一个其他主题；
*   检查软件与当前安装的兼容性问题。

有时候，在安装一堆插件之前进行一次完全备份是安全的。

* * *

## **17。删除任何关于你的 WP 安装的信息**

另一件重要的事情是，当涉及到 WP 安全时，要隐藏尽可能多的信息。例如，你当前安装的版本是可以帮助黑客理解潜在安全漏洞的信息之一，所以通过隐藏它，攻击者将不能利用任何与 WordPress 特定版本相关的已知漏洞。

你可以通过添加以下代码到你的主题的*functions.php*文件或者你的插件的工具中来删除 HTML 头和静态文件中的信息:

```
/* 
Hide scripts and styles version
*/
function remove_wp_version_strings( $src ) {
    global $wp_version;
    parse_str( parse_url( $src, PHP_URL_QUERY ), $query );
    if ( !empty( $query['ver'] ) && $query['ver'] === $wp_version ) {
        $src = remove_query_arg( 'ver', $src );
    }
    return $src;
} 
add_filter( 'script_loader_src', 'remove_wp_version_strings' );
add_filter( 'style_loader_src', 'remove_wp_version_strings' );

/*
Hide tag generator from headings
*/
function remove_wp_generator() {
    return '';
}
add_filter( 'the_generator', 'remove_wp_generator' ); 
```

Enter fullscreen mode Exit fullscreen mode

你也可以通过添加下面一行代码到**来隐藏当前 WordPress 版本的信息。主 WP 文件夹内的 htaccess** 文件:

```
# Block any WP info
<files readme.html>
Order allow,deny
Deny from all
</Files>
<files license.txt>
Order allow,deny
Deny from all
</files> 
```

Enter fullscreen mode Exit fullscreen mode

注意:尽管一些 wordpress 安全指南建议删除所有这些文件，我的建议是完全阻止对它们的访问，因为新的 WordPress 安装或更新会产生新的文件。

* * *

## **18。隐藏 PHP 通知和警告**

除了限制向攻击者提供信息的其他策略，隐藏错误报告是一个好主意，它可以向攻击者提供有价值的信息，如网站的 PHP 和 WordPress 版本、文件夹结构或服务器信息。

在开发环境中，错误报告有助于验证您的工作并发现潜在的错误；然而，在一个活跃的网站上，你应该禁用这些日志来隐藏诸如路径、名称、版本等信息。

要在 WordPress 中禁用错误报告，只需将下面几行代码添加到*wp-config.php*文件中:

```
error_reporting( 0 );
ini_set( 'display_errors', 0 ); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## **19。隐藏任何 PHP /Apache 信息**

隐藏信息的最后一个技巧是配置服务器发送的头:它们通常包含关于安装的软件和运行的 PHP 版本的信息。

根据安装情况，您必须通过在**中添加以下代码行来隐藏或限制网络服务器上共享的信息。主文件夹中的 htaccess** 文件:

```
ServerSignature Off 
```

Enter fullscreen mode Exit fullscreen mode

有两种方法可以隐藏一些服务器在 HTTP 头中发送的关于网站 PHP 版本的信息。首先，将以下代码添加到**中。htaccess** 文件:

```
Header unset X-Powered-By 
```

Enter fullscreen mode Exit fullscreen mode

或者在 *php.ini* 文件中使用下面的指令:

```
expose_php = Off 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:通常可以使用服务器管理面板将这行代码添加到当前活动的 *php.ini* 文件中，但是步骤
可能会根据您的托管服务而有所不同。

* * *

## **20。让你的 Wordpress 保持最新**

为了保护你的网站免受已知的安全漏洞，你应该总是使用 WordPress 的最新版本，保持已安装插件的更新，并更新你的主题。

通常，保持你的网站手动更新是更可取的，即使它需要更多的注意力和时间，因为它允许你检查每个更新中包含的功能和它们的目的。关于更新的顺序，我一直建议先更新 Wordpress 核心，再更新插件和主题，不用担心顺序问题。

然而，如果你想让 WordPress 核心自动更新，只需将下面一行代码添加到你的*wp-config.php*文件中:

```
define( 'WP_AUTO_UPDATE_CORE', true ); 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:如果你关闭了 WordPress **cron** 服务，自动更新将不会被执行。

每次更新后，您都会收到一封电子邮件，地址是平台管理员帐户使用的电子邮件地址。

WordPress 核心更新只是等式的一部分:根据 wpscan.org 的一份报告，在 WordPress 的安装中发现的 52%的漏洞是由于插件，11%是由于主题，37%是由于 WordPress 的核心。

如果你想自动更新插件，在你的活动主题的*functions.php*文件中添加下面一行
代码:

```
add_filter( 'auto_update_plugin', '__return_true' ); 
```

Enter fullscreen mode Exit fullscreen mode

在添加这段代码之前，请删除网站上所有未使用的插件。简单的停用不足以消除潜在的漏洞:消除它们！

添加以下代码行将自动更新主题:

```
add_filter( 'auto_update_theme', '__return_true' ); 
```

Enter fullscreen mode Exit fullscreen mode

最后，记住保证你的 WordPress 站点的安全是没问题的，但是你不应该忘记你的计算机也应该受到病毒和恶意软件的保护。确保您使用可靠安全的防病毒软件，使您的操作系统保持最新。

## **21。选择好的托管公司**

最后一个安全建议是选择一个安全的服务器来托管您的 web 项目，尽管就重要性而言它应该是第一个。

你的主机提供商应该为你提供一个安全的平台，并保持你的基础设施的安全性。当心那些使用过时软件的托管服务，不保护访问，雇佣对 WordPress 知之甚少的技术支持。

**选择一个好的主机服务提供商对于安全、性能和内部和平至关重要:)**

* * *

发现这篇文章有错别字？[做个 PR](https://github.com/lucagrandicelli/wordpress-security-handbook-2019) 。

> **嘿！我还提供关于[开发列表](https://dev.to/listings)的 Wordpress 指导。
> 随便问！**