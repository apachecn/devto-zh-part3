# 现在用 MySQL 托管在 ZEIT 上安装 WordPress 5

> 原文：<https://dev.to/scalegrid/installing-wordpress-5-on-zeit-now-with-mysql-hosting-5b67>

[![Installing WordPress 5 on ZEIT Now with MySQL Hosting - ScaleGrid](img/d395d5316ae65f5010fb111da146d9dd.png)](https://scalegrid.io/blog/installing-wordpress-5-on-zeit-now-with-mysql-hosting/)

ZEIT Now WordPress builder 已被官方弃用，ZEIT 无法保证 Now PHP 社区构建器的性能/可靠性。

想通过 ZEIT 在 [Now 平台上部署](https://zeit.co/now) [WordPress 5.0](https://wordpress.org/support/wordpress-version/version-5-0/) ？我们在 ZEIT 的 Now 全球无服务器部署平台上的朋友们使用 ScaleGrid 的廉价 MySQL 托管实例为 [WordPress5-on-Now](https://wordpress5.now.sh/?p=18) 编写了一个很棒的教程。由于对这个安装有如此强烈的兴趣，我们决定写下在 ScaleGrid 端配置 MySQL 数据库的步骤，让你现在就可以更快地启动和运行 Wordpress。

那么，为什么 WordPress 现在需要用 ZEIT 托管 [MySQL 呢？现在专注于成为无服务器托管的最佳平台，但你需要找到一种方法来永久存储你的数据。这就是 ScaleGrid 的用武之地。你可以在 Azure](https://scalegrid.io/blog/installing-wordpress-5-on-zeit-now-with-mysql-hosting/) 上设置完全托管的 [MySQL，只需](https://scalegrid.io/mysql/azure.html)[8 美元/月](https://scalegrid.io/pricing.html?db=mysql&cloud=cloud_azure&replica=deployment_standalone&instance=micro#section_pricing_byoc)用于管理，或者[18 美元/月](https://scalegrid.io/pricing.html?db=mysql&cloud=cloud_azure&replica=deployment_standalone&instance=micro#section_pricing_dedicated)用于托管专用服务器。

最好的部分是你需要做的就是设置它，然后你的 MySQL 数据库自动运行，这样你就可以把你的开发精力集中在产品上，而不是那些耗时的数据库操作上。我们开始吧！

## 现在就在 ZEIT 上为 WordPress 设置 MySQL 数据库

### 在 ScaleGrid 中创建 MySQL 数据库

1.  在 ScaleGrid 控制台上注册[30 天免费试用](https://console.scalegrid.io/users/register)。
2.  创建您的第一个 MySQL 集群。确保在与您现在部署的区域相匹配的区域中创建它。我们支持两种不同的 MySQL DBaaS 计划([比较 MySQL 计划](https://help.scalegrid.io/docs/mysql-hosting-features-by-plan)):
    *   [专用 MySQL 集群](https://help.scalegrid.io/docs/mysql-new-cluster-dedicated-hosting) -通过您的 ScaleGrid 帐户托管。
    *   [BYOC MySQL 集群](https://help.scalegrid.io/docs/mysql-new-cluster-bring-your-own-cloud) -通过你自己的 Azure 账户托管。
3.  设置好集群后，转到 MySQL 集群详细信息页面，选择“数据库”选项卡，然后单击绿色的“新建数据库”按钮。
4.  在弹出的“创建新数据库”窗口中，只需输入数据库的名称，然后单击“创建”。[![Create a MySQL Database in ScaleGrid For Your ZEIT Now Deployment on WordPress](img/56928a4a7cadf0a3cf161f18d5c6e6e2.png)](https://scalegrid.io/blog/wp-content/uploads/2019/02/Create-a-MySQL-Database-in-ScaleGrid-DBaaS.jpg)
5.  您的数据库现在将被创建！您可以在 MySQL 集群的“数据库”选项卡下随时访问它。记下您的数据库名称，以便您可以在我们稍后的 ZEIT Now 步骤中使用它。

### 为您的 MySQL 数据库创建一个新用户

1.  转到您的 MySQL Cluster 详细信息页面，选择“用户”选项卡，然后单击“新用户”按钮。
2.  在“新用户”窗口中，从“选择用户有权访问的数据库”下拉列表中选择您刚刚创建的数据库。
3.  为新用户输入“名称”和“密码”。
4.  在“角色”下拉列表中，确保选择“读写”作为角色，以便新用户对此数据库具有完全写权限。[![Create a MySQL Database New User in ScaleGrid DBaaS for ZEIT Now on WordPress](img/cd63bd28f66fe16d761e7daeadf716fc.png)](https://scalegrid.io/blog/wp-content/uploads/2019/02/Create-a-MySQL-Database-New-User-in-ScaleGrid-DBaaS.jpg)
5.  单击“创建”,您的新用户将被创建！记下你的新数据库用户名和密码，这样你就可以在 WordPress 上设置 ZEIT Now 时使用。

### 获取您的 MySQL 部署的主机名

1.  转到 MySQL Cluster 详细信息页面，选择“概述”选项卡。
2.  找到页面底部的[命令行语法](https://help.scalegrid.io/docs/mysql-command-line-syntax "MySQL Command Line Syntax")部分，查看可用于通过 mysql 客户端连接 MySQL 部署的命令。【T2![MySQL Command Line Syntax - ScaleGrid DBaaS](img/e1c328a9463e9a87e25667f5bb5798b8.png)
3.  '-h '选项后面的服务器名称是您的 MySQL 部署的主机名，在这种特殊情况下，它是' SG-help-1-master . dev servers . scale grid . io '。
4.  记下您的 MySQL 主机名，以便在我们使用 ZEIT Now 和 WordPress 的后续步骤中使用。

### 下载您的 ca.pem 文件

1.  转到 MySQL Cluster 详细信息页面，选择“概述”选项卡。
2.  如果您的 MySQL 部署启用了 SSL，那么您将会看到“SSL 证书”部分
3.  单击“获取 SSL CA 证书”链接查看 CA 证书的内容，并将这些内容复制到 ca.pem 文件中。[![MySQL Cluster ca.pem File SSL Certificate - ScaleGrid DBaaS](img/be5da7c0508f3dc7fc5713b3c9ff6618.png)](https://scalegrid.io/blog/wp-content/uploads/2019/02/MySQL-Cluster-ca.pem-File-SSL-Certificate-ScaleGrid-DBaaS.png)

* * *

现在你已经拥有了在 ZEIT 上用 MySQL 实例设置你的 WordPress 的一切！以下教程中概述的步骤是由 ZEIT Now 开发的，你可以在这里阅读原始说明，网址是 [WordPress5-on-Now](https://wordpress5.now.sh/?p=18) 或点击以下链接。

* * *

## 现在在 ZEIT 上设置 WordPress 5

现在安装。`npm i -g now`如果时间紧迫。在 ScaleGrid 等云 SQL 托管提供商中设置数据库。确保您选择的位置与现在部署的区域相匹配。创建一个`wp-config.php`文件和一个`now.json`文件。你的`now.json`文件将设置`@now/wordpress`建造者和几条路线:

```
{
  "version": 2,
  "builds": [
    { "src": "wp-config.php", "use": "@now/wordpress" }
  ],
  "routes": [
    { "src": "/wp-admin/?", "dest": "index.php" },
    { "src": ".*\\.php$", "dest": "index.php" }
  ],
  "env": {
    "DB_NAME": "@wordpress_db_name",
    "DB_USER": "@wordpress_db_user",
    "DB_PASSWORD": "@wordpress_db_password",
    "DB_HOST": "@wordpress_db_host"
  }
}
```

注意，它指的是像`@wordpress_db_name`这样的几个秘密。你可以用`now secret add`创建这些。我的`wp-config.php`看起来如下。注意，出于安全原因，我们使用 MySQL 而不是 TLS。

```
<?php
/**
 * The base configuration for WordPress
 *
 * The wp-config.php creation script uses this file during the
 * installation. You don't have to use the web site, you can
 * copy this file to "wp-config.php" and fill in the values.
 *
 * This file contains the following configurations:
 *
 * * MySQL settings
 * * Secret keys
 * * Database table prefix
 * * ABSPATH
 *
 * @link https://codex.wordpress.org/Editing_wp-config.php
 *
 * @package WordPress
 */

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', $_ENV['DB_NAME']);

/** MySQL database username */
define('DB_USER', $_ENV['DB_USER']);

/** MySQL database password */
define('DB_PASSWORD', $_ENV['DB_PASSWORD']);

/** MySQL hostname */
define('DB_HOST', $_ENV['DB_HOST']);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');

/**#@+
 * Authentication Unique Keys and Salts.
 *
 * Change these to different unique phrases!
 * You can generate these using the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}
 * You can change these at any point in time to invalidate all existing cookies. This will force all users to have to log in again.
 *
 * @since 2.6.0
 */
define('AUTH_KEY',         'WPSALT');
define('SECURE_AUTH_KEY',  'WPSALT');
define('LOGGED_IN_KEY',    'WPSALT');
define('NONCE_KEY',        'WPSALT');
define('AUTH_SALT',        'WPSALT');
define('SECURE_AUTH_SALT', 'WPSALT');
define('LOGGED_IN_SALT',   'WPSALT');
define('NONCE_SALT',       'WPSALT');

/**#@-*/

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each
 * a unique prefix. Only numbers, letters, and underscores please!
 */
$table_prefix  = 'wp_';

/**
 * For developers: WordPress debugging mode.
 *
 * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 *
 * For information on other constants that can be used for debugging,
 * visit the Codex.
 *
 * @link https://codex.wordpress.org/Debugging_in_WordPress
 */
define('WP_DEBUG', false);

define( 'WP_SITEURL', 'https://' . $_SERVER['HTTP_HOST'] );
define( 'WP_HOME', 'https://' . $_SERVER['HTTP_HOST'] );
define( 'WP_CONTENT_URL', 'https://' . $_SERVER['HTTP_HOST'] . '/wp-content' );

/* That's all, stop editing! Happy blogging. */

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
        define('ABSPATH', dirname(__FILE__) . '/');

define( 'MYSQL_SSL_CA', ABSPATH . 'ca.pem' );
define( 'MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL );

/** Sets up WordPress vars and included files. */
require_once(ABSPATH . 'wp-settings.php');
```

确保将您的``WPSALT`` 更改为随机字符串或作为`$_ENV`传递的密码。最后我们创建了一个从 ScaleGrid 下载的`ca.pem`文件。这只有在 TLS 上使用 MySQL 时才有必要。就是这样！然后运行`now`进行部署或者`git push`如果你现在配置了[+GitHub](https://zeit.co/github)。

* * *

如果您想将 ZEIT 与 ScaleGrid 的 [MySQL](https://scalegrid.io/mysql.html "MySQL Hosting") 、 [MongoDB](https://scalegrid.io/mongodb.html "MongoDB Hosting") 、 [Redis](https://scalegrid.io/redis.html "Redis Hosting") 或 [PostgreSQL](https://scalegrid.io/postgresql.html "PostgreSQL Hosting") 托管和管理计划结合使用，请在下面给我们留下评论，我们很乐意将它们写下来！此外，请在我们的评论中或 Twitter 上分享任何问题，地址为 [@scalegridio](https://dev.to/scalegridio) ，我们将跟进提供帮助。