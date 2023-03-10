# 用 PHP 7.2 修复 Exec-PHP (WordPress 插件)

> 原文：<https://dev.to/nabbisen/fixing-exec-php-wordpress-plugin-with-php-72-15e5>

## 本帖讲述的是

这篇文章是关于修正在最新的 [WordPress](https://wordpress.com/) 和 [PHP](http://php.net/) 7.2 中运行的" [Exec-PHP](https://wordpress.org/plugins/exec-php/) "插件的错误。

## 发生了什么事

前几天，我在为我的客户托管 WordPress 站点的服务器上将 PHP 版本从 5.6 升级到 7.2。

其中一个网站使用了 Exec-PHP，使我们能够直接在帖子或页面中执行 PHP 代码。

不幸的是，它过时了。上一次更新是在 9 年前，而且是基于 7.0 版本的 PHP。由于安全问题，这个插件已经被官方弃用，并且从去年年底已经从 WordPress [插件](https://wordpress.org/plugins/)页面上消失了。

不管怎样，我的顾客已经用过了，服务还在。我需要找到实现服务器迁移和服务连续性的方法。

## 它为什么会出错

是因为 PHP 7.0 的一个向后不兼容的变化，那就是:
[新对象不能通过引用](http://php.net/manual/en/migration70.incompatible.php#migration70.incompatible.other.new-by-ref)赋值。
这是被禁止的:

```
class C {}
$c =& new C; 
```

## 如何修复

#### 使用替代插件

如果有所发现，这将是一个几乎理想的方式。

#### 直接修改插件代码

像这样从' =& '中删除' & ':

```
- $c =& new C; + $c = new C; 
```

所有的目标都在`wordpress/wp-content/plugins/exec-php`:

*   `exec-php.php`
    *   第 22 行
*   `include/admin.php`
    *   第 53、56、57、63、64、79 行
*   `includes/ajax.php`
    *   第 64 行
*   `includes/cache.php`
    *   第 22、39 行
*   `includes/manager.php`
    *   第 36、37、38、39 行

例如，将第 22 行的`exec-php.php`修改为:

```
- $GLOBALS['g_execphp_manager'] =& new ExecPhp_Manager(); + $GLOBALS['g_execphp_manager'] = new ExecPhp_Manager(); 
```

## 经过所有的修改和测试

WordPress 站点和修改后的 Exec-PHP 一起工作得很好。到目前为止，我的客户对新服务器很满意: )