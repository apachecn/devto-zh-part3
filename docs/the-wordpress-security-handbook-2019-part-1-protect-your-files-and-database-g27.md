# Wordpress 安全手册 2019 -第 1 部分:保护你的文件和数据库

> 原文：<https://dev.to/lucagrandicelli/the-wordpress-security-handbook-2019-part-1-protect-your-files-and-database-g27>

免责声明 *:本手册是费尔南多·普恩特为网站托管提供商撰写的指南的翻译。*

## **简介**

想知道 WordPress 是否安全？**答案是肯定的**。

WordPress 使用最新的安全技术。此外，它还会不断更新新版本，其中通常包括几个漏洞补丁。最后，WordPress 是由一个大型开发者社区监控和运行的，安全性是他们的核心。

那么，为什么要有安全手册呢？
因为保护您的网站应该是一项持续的活动，这意味着，除其他外，创建针对攻击者的屏障、故障预防、变更监控、反对恶意访问和隐藏敏感信息。

超过 30%的网站使用 WordPress:正因为如此受欢迎，它成为网络罪犯的目标，也是一个不断发展的平台，对第三方的变化和增加开放。

因此，让我们开始建立我们的手册，并记住:*谈到安全，偏执是你最好的朋友*。

* * *

本文并不打算穷尽所有情况和环境，也不是网站安全的权威指南，因此可能会发生变化。安全专家应该仔细审查和分析每种情况。

* * *

## **1。安装 Wordpress** 之前

当创建一个新的 WordPress 安装时，你应该总是选择最新的稳定版本。但是在安装之前，请遵循以下两个简单的安全步骤:

1.  **更改数据库表前缀**
2.  **使用认证密钥**

**如何更改数据库表前缀**

默认情况下，所有的 WordPress 安装都在数据库中使用表前缀 **wp_** 。我建议您将它改为更复杂的字符串，最好是随机的，例如: **3Fd9_**

> 请记住，这一安全步骤是为了防止对您的数据库可能的攻击。

要改变 WordPress 表的前缀，在**wp-config.php**配置文件中编辑下面一行，用你选择的前缀替换默认前缀(我建议你使用一个生成的随机字符串。使用类似 random.org[:
的在线工具](https://www.random.org/strings/)

```
// Original table prefix.
$table_prefix = 'wp_';

// change it to (es.):
$table_prefix = '3Fd9_'; // This is just an example. Use the prefix you like. 
```

Enter fullscreen mode Exit fullscreen mode

此外，由于这个改变，你将能够在同一个数据库中运行不同的 WordPress 安装，只要你不重复前缀。

**注:**

> *如果你已经安装了你的网站，并且在安装过程中没有改变默认前缀，现在还不晚。使用类似[的插件改变表格前缀](https://it.wordpress.org/plugins/change-table-prefix/)来进行改变。也可以手动更改前缀，但如果不熟悉数据库，就不要这样做。*

**如何使用认证密钥**

WordPress 使用秘密密钥，称为*密钥*和*盐*，存储在 wp-config.php**文件**中:它们通过加密浏览器 cookie 中的会话数据来保护打开的会话。

虽然手动生成这些密钥是可能的，但我建议你使用 https://api.wordpress.org/secret-key/1.1/salt/页面上的官方 WordPress 服务，并用你的 wp-config.php 文件中的密钥替换掉。

**注:**

> 就像数据库前缀一样，您可以随时更改密钥，即使是在现有的网站上:最好定期运行此操作，以使活动会话无效，并强制所有用户重新登录。

**但在继续之前，有一个小技巧:**

万一您应该拒绝对管理面板的任何访问，即使是通过登录凭证，您可以通过在**wp-config.php**:
中插入以下代码来配置每微秒的密钥失效

```
define('AUTH_KEY', microtime());
define('SECURE_AUTH_KEY', microtime());
define('LOGGED_IN_KEY', microtime());
define('NONCE_KEY', microtime());
define('AUTH_SALT', microtime());
define('SECURE_AUTH_SALT', microtime());
define('LOGGED_IN_SALT', microtime());
define('NONCE_SALT', microtime()); 
```

Enter fullscreen mode Exit fullscreen mode

请记住定期更新这些密钥作为预防措施或结束活动会话。

* * *

## **2 -安装 Wordpress 后**

一旦新 WordPress 站点的安装完成，你应该删除安装过程中使用的管理员配置文件，创建一个拥有管理员权限的新用户和你需要的所有用户帐户。

避免使用像“admin”或“administrator”这样的弱用户名(在所有 WordPress 安装中都很常见)，记住使用复杂的密码。禁用管理面板上的 pingback 和 trackback 通知(设置>评论)，因为它们可能会对您的网站构成可能的 **DDoS** ( **D** 分布式**D**enial**o**f**S**service)攻击。

通过在**中添加以下代码行来保护您的文件免受攻击和入侵。htaccess** 文件，最好是在你网站根目录下的文件的开头:

```
#Deny folder listing
Options -Indexes

#Block access to sensitive files
<files .htaccess>
Order allow,deny
Deny from all
</files>
<files wp-config.php>
Order allow,deny
Deny from all
</files> 
```

Enter fullscreen mode Exit fullscreen mode

您还应该通过创建一个新文件来阻止对所有不必要文件的访问。在/wp-admin 文件夹中添加下面几行代码:

```
#Block installation file
<files install.php>
Order allow,deny
Deny from all
</files>
<files setup-config.php>
Order allow,deny
Deny from all
</files> 
```

Enter fullscreen mode Exit fullscreen mode

最后，也看看位于你的网站的根文件夹中的 **robots.txt** 文件。检查它没有显示关于你的 WordPress 安装的敏感信息，例如你的管理文件夹 *wp-admin* 。

* * *

## **3。更改文件和文件夹权限**

确保你的 WordPress 安装中的文件和文件夹有适当的权限来防止攻击者控制你的网站。您可以通过 FTP 客户端或您的虚拟主机服务提供的面板管理来更改权限。

以下是公认的安全值:

*   所有的*文件夹*都要设置 **755** 。
*   所有的*档*都要设置 **644** 。
*   *wp-config.php*应设定 **600**
*   *。应设置 htaccess* 的 **604**

* * *

## **4。阻止 PHP 在你的文件夹中执行**

尽管 WordPress 安装默认情况下通过管理面板阻止 PHP 文件上传，你也应该阻止在文件夹中运行 PHP 代码的能力。另外，你应该限制 WordPress 使用的其他文件夹中不必要的 PHP 代码的执行，这些文件夹不应该被直接访问。

创建新的。htaccess 文件放在您的 **/wp-content/uploads** 文件夹、 **/wp-content/plugins** 和 **/wp-content/themes** 中，并添加以下代码行来阻止 PHP 执行:

```
<Files *.php>
deny from all
</Files> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## **5。禁用 Wordpress 中的文件编辑**

此过程旨在为管理面板增加一个安全级别，以防止不必要的入侵并限制授权用户犯下的错误。要禁用 WordPress 管理面板中的文件编辑选项，在配置文件**wp-config.php**中使用下面一行代码:

```
define( 'DISALLOW_FILE_EDIT', true ); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码相当于删除了网站上任何注册用户的“ **edit_themes** ”权限、
”**edit _ plugins**、 **edit_files** 。

如果您不希望用户自行安装主题和插件，您可以为活跃网站添加额外的控制级别。只需在**wp-config.php**配置文件中输入以下代码:

```
define( 'DISALLOW_FILE_MODS', true ); 
```

Enter fullscreen mode Exit fullscreen mode

如果你必须执行 WordPress 安装任务，记得通过将指令改为“false”来禁用命令。对**wp-config.php**文件的所有更改都必须插入到以下代码行的上方:

```
/* That's all, stop editing! Happy publishing. */ 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## **6。使用 CDN 作为 DNS**

虽然我们已经知道了 CDN(T2)对提高网站性能的好处，但是使用 CDN 作为 DNS 服务(在你的网络服务器之前)可以在以下三个方面提高安全性:

*   启用针对恶意行为(如大量连接或跟踪端口)不断更新的主动防火墙；

*   使用提供商的分布式服务器网络防止暴力攻击，这将影响降至最低，并应用阻止规则来检测这种类型的攻击，通常是 DoS 或 DDoS

*   它隐藏了你服务器的真实 IP 地址，由于屏蔽了你网站的真实 IP 地址，防止了对你网站的直接攻击。

* * *

## **7。后援。后援。备份**

虽然我希望你永远不会用到这个建议，但是安全总比后悔好:对你的网站做一个完整的备份。

你很少需要恢复一个完整的备份，但如果你不得不这样做，检查你的主机提供商是否已经有一些工具可以帮助你。

对于敏感数据的备份，有一个特殊的规则叫做"**" 3-2-1 备份规则**:

*   **保留 3 份备份；**
*   **2 种不同格式(最少)；**
*   **其中一个备份应该位于不同的物理位置。**

在紧急情况下，将所有备份放在相同的格式或相同的位置是没有用的。记住，在对你的 WordPress 安装做了大的改动之后，总是要生成一个新的备份。

* * *

发现这篇文章有错别字？[做个 PR](https://github.com/lucagrandicelli/wordpress-security-handbook-2019) 。

* * *

> **嘿！我还提供关于[开发列表](https://dev.to/listings)的 Wordpress 指导。
> 随便问！**