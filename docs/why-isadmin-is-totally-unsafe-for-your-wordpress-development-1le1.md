# 为什么 is_admin()对于你的 Wordpress 开发是完全不安全的

> 原文：<https://dev.to/lucagrandicelli/why-isadmin-is-totally-unsafe-for-your-wordpress-development-1le1>

在开发一个 Wordpress 插件/主题时， [`is_admin()`](https://codex.wordpress.org/Function_Reference/is_admin) 可能是检查当前代码是否在 admin 部分下运行使用最多的函数之一。不幸的是，大多数开发人员倾向于认为这足以确保代码由拥有特权和正确访问权限的用户运行。当然，这都是错的。

## 地狱之门

3 月 21 日，wordfire 安全团队[披露了 Social war 中的两个漏洞](https://www.wordfence.com/blog/2019/03/recent-social-warfare-vulnerability-allowed-remote-code-execution/)，Social war 是 WordPress 生态系统中一个非常受欢迎的插件。一个是存储的跨站点脚本攻击(XSS)，另一个是远程代码执行(RCE)，两者都由 CVE-2019-9978 跟踪。

这两个漏洞有一个共同点:误用 WordPress 中的 [`is_admin()`](https://codex.wordpress.org/Function_Reference/is_admin) 功能。我们来看看为什么:

```
<?php
/**
 * Migrates options from $_GET['swp_url'] to the current site.
 *
 * @since 3.4.2
 */
if ( true == SWP_Utility::debug('load_options') ) {
    if (!is_admin()) { // THE DOOR TO HELL
        wp_die('You do not have authorization to view this page.');
    }
    $options = file_get_contents($_GET['swp_url'] . '?swp_debug=get_user_options'); 
```

Enter fullscreen mode Exit fullscreen mode

*BLA BLA 代码*

```
<?php
$options = str_replace('<pre>', '', $options);
$cutoff = strpos($options, '</pre>');
$options = substr($options, 0, $cutoff);
$array = 'return ' . $options . ';';
try {
    $fetched_options = eval( $array ); // WELCOME TO HELL
} 
```

Enter fullscreen mode Exit fullscreen mode

以上是《社会战争》插件的片段。报告的代码旨在将远程文本文档解析为供插件使用的 key->value 选项数组。然而，插件并没有使用 JSON 这样的典型数据存储格式，而是生成了选项作为实际的 PHP 代码块，由`eval()`处理成一个数组。

最终，Wordfence 的人很好地解释了这一点:

> 当恶意注入与插件通常使用的格式相匹配时，如在当前运行的攻击活动中，XSS 有效载荷可以被注入到一个或多个这些设置中。然而，远程文件中的`<pre>`标签的内容被任意传递给`eval()`，这意味着其中的代码作为 PHP 直接执行。

所以，现在够了。让我们回到基础。

## 是 _admin()的目的

如[中提到的:](https://codex.wordpress.org/Function_Reference/is_admin)

> is_admin()不用于安全检查。每当当前 URL 是 WordPress 管理端的页面时，它将返回 true。它不检查用户是否登录，也不检查用户是否有权访问所请求的页面。这是一个方便插件和主题用于各种目的的函数，但是它不适合验证安全请求。

这是否意味着我不应该使用 [`is_admin()`](https://codex.wordpress.org/Function_Reference/is_admin) ？没有。至少，不仅仅是。

当编写只在 Wordpress 后端管理区需要的代码时，这个功能是至关重要的，但是它应该总是要做的第一步。之后，你写的每样东西都必须被封装在一个安全检查层之下，这个安全检查层主要考虑两点:用户会话和用户能力。然后，我们可以确定一个简单的渐进式安全路径，如下所示:

1.  用户是否正在查看管理部分？
2.  如果是，用户是否真正登录(有效会话)？
3.  如果是，当前登录的用户是否有能力查看此部分？

所以，在继续之前，让我告诉你一些事情。

## 用户会话

Wordpress 是一个非常重视安全性的高级系统。当在它的基础设施内开发时，我们有所有必要的工具来编写良好和安全的代码。

[`is_user_logged_in()`](https://developer.wordpress.org/reference/functions/is_user_logged_in/) 是你在管理区下开发时总是需要用到的第一个函数。它只是做它的名字:检查用户是否登录。虽然这看起来是一个简单的任务，但是当与 [`is_admin()`](https://codex.wordpress.org/Function_Reference/is_admin) 一起使用时，会基于会话、[临时令牌](https://codex.wordpress.org/WordPress_Nonces)等等授予第一层安全性。

## Wordpress 中的用户能力

让我们再次引用一下 [Wordpress Codex](https://codex.wordpress.org/Roles_and_Capabilities) :

> WordPress 使用了角色的概念，旨在让网站所有者能够控制用户在网站中可以做什么和不可以做什么。通过为每个用户分配特定的角色，网站所有者可以管理用户对诸如撰写和编辑帖子、创建页面、定义链接、创建类别、审核评论、管理插件、管理主题和管理其他用户等任务的访问。

事实上，并非所有登录用户都可以使用管理区域的每个部分。与此同时，你可能不仅仅希望管理员能够访问这一部分，所以 Wordpress 提供了一个方便的工具:**神奇的** [`user_can( $user, $capability )`](https://codex.wordpress.org/Function_Reference/user_can)

该函数将用户 ID 作为第一个参数，将特定的“功能”作为第二个参数。如果当前登录的用户有这个，函数返回 true。

```
<?php

if ( ! is_admin() ) {
    wp_die( 'This code is for admin area only' );
}

if ( ! is_user_logged_in() ) {
    wp_die( 'You shall not pass!' );
}

if ( ! user_can( $id_user, 'my_capability' ) {
    wp_die( 'Yes, you too... shall not pass.' );
}

// Do your stuff 
```

Enter fullscreen mode Exit fullscreen mode

Wordpress 有一个很长的[用户角色和功能](https://codex.wordpress.org/Roles_and_Capabilities)列表，所以你只需要做出正确的选择。

这三个功能一起使用是编写安全代码的良好开端，至少可以防止大多数可能影响 Wordpress 插件或主题的常见漏洞。

**编码药丸** : *偏执狂是安全最好的朋友*

* * *

发现错别字？[制作 PR](https://github.com/lucagrandicelli/blog/tree/master/wordpress/02-why-is_admin()-is-totally-unsafe-for-you-wordpress-development)

*封面图片由* [阿莱娜·艾娜米](https://www.artstation.com/aenamiart)拍摄