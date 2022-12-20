# 更新到 EC-CUBE 4.0.0 - >4.0.1@ openbsd

> 原文：<https://dev.to/nabbisen/ec-cube-400---401---openbsd-5g8m>

# Summary

前几天，在[OpenBSD](http://openbsd.org) 6.4 上安装了[EC-CUBE](https://www.ec-cube.net/) 4.0.0。
*在以下报道中写了详细情况:

[![nabbisen](img/6759a36884202f6cf21b258b1e80383e.png)](/nabbisen) [## 在 OpenBSD 6.4 上安装 EC-CUBE 4

### nab bisen 12 月 21 日 183 分钟读数

#eccube #ecommerce #openbsd #installation](/nabbisen/ec-cube-4--openbsd-64--30pd)

之后发布了 EC-CUBE 的 4.0.1，所以这次进行了更新。
步骤遵循了[官方文档](https://doc4.ec-cube.net/)的[“EC-CUBE 主机版本升级”](https://doc4.ec-cube.net/update)。
OpenBSD 特有的有以下两点。

1.  执行 PHP 命令时，用`php-7.2`代替`php`。
2.  虽然在封装中存在多个[`.htaccess`](https://httpd.apache.org/docs/current/ja/howto/htaccess.html)，但是如果在某个地方有更新，则根据需要通过[`httpd.conf`](https://man.openbsd.org/httpd.conf.5)/ [`php.ini`](http://php.net/manual/en/configuration.file.php)(`php-72.ini`)来吸收差值。

对于后者，按照与后述的“注意事项”中的 EC-CUBE 主体的代码定制时的差分更新相同的要领进行了更新。

### environment

*   操作系统:OpenBSD 6.4 amd64
*   网页サーバー: OpenBSD [httpd](https://man.openbsd.org/httpd.8)
*   购物车系统: EC-CUBE 4.0.0 - >4.0.1
*   DB: [马里亚 DB](https://mariadb.com/) 10.0

### Pay attention

如果正在进行 EC-CUBE 定制，则需要小心。
如果按照更新步骤进行文件复盖，则定制内容可能会消失。

[官方文件“注意事项”](http://doc4.ec-cube.net/quickstart_update#%E3%81%94%E6%B3%A8%E6%84%8F):

> 如果自定义 EC-CUBE 主体的代码( app/config/eccube、app/DoctrineMigrations、bin、src 和 html 目录)，则会复盖文件
> [请确认各版本的变更差分](http://doc4.ec-cube.net/quickstart_update#%E5%90%84%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3%E3%81%A7%E3%81%AE%E5%A4%89%E6%9B%B4%E5%B7%AE%E5%88%86)并导入必要的差分。

<center>✿ ✿ ✿</center>

# 更新的步骤

### premise

将更新对象的根目录的名称设为`eccube`。

首先获取现状的备份。
接下来下载最新软件包&解压缩。
以最新软件包的文件为基础，进行更新。
整体上有以下层次关系:

```
.
|
+-- eccube          * アップデート対象
|   +-- app
|   +-- bin
|   +-- ...
+-- eccube-4.0.1    * 最新パッケージ
|   +-- app
|   +-- bin
|   +-- ...
+-- %backup-dir%    * バックアップ
    +-- database-backup.sql
    +-- eccube-4.0.0 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 步骤 1: [获取备份](http://doc4.ec-cube.net/quickstart_update#1-%E3%82%B5%E3%82%A4%E3%83%88%E3%81%AE%E3%83%90%E3%83%83%E3%82%AF%E3%82%A2%E3%83%83%E3%83%97)

##### #1.创建备份目录

```
$  mkdir %backup-dir% 
```

Enter fullscreen mode Exit fullscreen mode

%backup-dir%实际上命名为:

`eccube4.0.0-`date '+%Y%m%d'``

##### #2.数据库备份

可以通过[`mysqldump`获取 MariaDB / MySQL 的备份。](https://dev.to/nabbisen/mariadb-backing-uprestoring-databases-on-the-command-line-351j)

```
$  mysqldump -u %user% -p %database% > %backup-dir%/database-backup.sql 
```

Enter fullscreen mode Exit fullscreen mode

##### #3.源文件的备份

从软件包的根目录复制所有文件。

```
$  cp -rp eccube %backup-dir%/eccube-4.0.0 
```

Enter fullscreen mode Exit fullscreen mode

##### #4.备份完成

至此，备份完成。
为了防止人为错误，作为最终确认，我认为最好先检查一下制作好的东西。

```
$  ls -l %backup-dir% 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 步骤 2 :进行更新

**注释: EC-CUBE 4.0.1 以上版本可以使用[维护模式](http://doc4.ec-cube.net/quickstart_update#2%E3%83%A1%E3%83%B3%E3%83%86%E3%83%8A%E3%83%B3%E3%82%B9%E3%83%A2%E3%83%BC%E3%83%89%E3%82%92%E6%9C%89%E5%8A%B9%E3%81%AB%E3%81%99%E3%82%8B%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3401%E4%BB%A5%E9%99%8D)。 在 4.0.0 中，也可以通过在根目录`eccube`中暂时创建空文件`.maintenance`来进行。*

##### #1\. [从官方网站下载页面](https://www.ec-cube.net/download/)获取最新的 EC-CUBE 包

```
$  wget http://downloads.ec-cube.net/src/eccube-4.0.1.zip
$  unzip eccube-4.0.1.zip 
```

Enter fullscreen mode Exit fullscreen mode

##### #2\. [更新公共文件](http://doc4.ec-cube.net/quickstart_update#3-%E5%85%B1%E9%80%9A%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E5%B7%AE%E3%81%97%E6%9B%BF%E3%81%88)

无论更新版本如何，始终作为更新对象的文件组。

```
$  cp -r eccube-4.0.1/app/config/eccube      eccube/app/config
$  cp -r eccube-4.0.1/app/DoctrineMigrations eccube/app
$  cp -r eccube-4.0.1/bin                    eccube/
$  cp -r eccube-4.0.1/src                    eccube/
$  cp -r eccube-4.0.1/html                   eccube/
$  cp -r eccube-4.0.1/vendor                 eccube/ 
```

Enter fullscreen mode Exit fullscreen mode

##### #3\. [更新个别文件](http://doc4.ec-cube.net/quickstart_update#4-%E5%80%8B%E5%88%A5%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E5%B7%AE%E3%81%97%E6%9B%BF%E3%81%88)

每个更新版本可能都有需要额外更新的文件。
4.0.0 - >更新为 4.0.1 时:

```
$  cp eccube-4.0.1/composer.json             eccube/
$  cp eccube-4.0.1/composer.lock             eccube/
$  cp eccube-4.0.1/.htaccess                 eccube/
$  cp eccube-4.0.1/index.php                 eccube/
$  cp eccube-4.0.1/maintenance.php           eccube/ 
```

Enter fullscreen mode Exit fullscreen mode

**注释:由于使用的是 OpenBSD 的 httpd，因此更新 Apache 的`.htaccess`实际上不会更新。 作为替代措施，以下步骤将捕获版本之间的差异。*

文件复盖后，清除 APP 应用程序的缓存:

```
$  cd eccube
  $  php-7.2 bin/console cache:clear --no-warmup
  $  cd ../ 
```

Enter fullscreen mode Exit fullscreen mode

另外，如果要跨越多个版本，据说需要分阶段进行更新:

> 如 4.0.0 → 4.0.2 那样进行跨多个版本的版本升级时，请按照 4.0.0 → 4.0.1→4.0.1 → 4.0.2 那样进行阶段性的版本升级。

##### #4\. [`composer.json`/ `composer.lock`的更新](http://doc4.ec-cube.net/quickstart_update#5-composerjsoncomposerlock%E3%81%AE%E6%9B%B4%E6%96%B0)

如果满足以下所有条件，则需要执行此步骤(如果不需要，请跳过此步骤) :

*   个别文件替换的替换对象中包含`composer.json`/ `composer.lock`
*   正在安装插件

[更新 Composer](https://getcomposer.org/) 的[`composer.json`/ `composer.lock`](https://getcomposer.org/doc/01-basic-usage.md):

```
$  cd eccube
  $  php-7.2 bin/console eccube:composer:require-already-installed
  $  cd ../ 
```

Enter fullscreen mode Exit fullscreen mode

##### #5\. [模式更新/迁移](http://doc4.ec-cube.net/quickstart_update#6-%E3%82%B9%E3%82%AD%E3%83%BC%E3%83%9E%E6%9B%B4%E6%96%B0%E3%83%9E%E3%82%A4%E3%82%B0%E3%83%AC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3)

这次不需要本项:

> 4.0.0 → 4.0.1 的更新不需要架构更新。

但是，如下所示，按照需要更新时的步骤执行命令并没有造成危害:

```
$  cd eccube
  $  # スキーマ更新
$  php-7.2 bin/console doctrine:schema:update --force --dump-sql
$  # マイグレーション
$  php-7.2 bin/console doctrine:migrations:migrate
  $  cd ../ 
```

Enter fullscreen mode Exit fullscreen mode

##### #6\. [模板文件的更新](http://doc4.ec-cube.net/quickstart_update#7-%E3%83%86%E3%83%B3%E3%83%97%E3%83%AC%E3%83%BC%E3%83%88%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%81%AE%E6%9B%B4%E6%96%B0)

进行文件的复盖或者[差分](http://doc4.ec-cube.net/quickstart_update#%E5%90%84%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3%E3%81%A7%E3%81%AE%E5%A4%89%E6%9B%B4%E5%B7%AE%E5%88%86)的读取。

4.0.0 ->4.0.1 的更新对象如下。

| 页面名称 | 文件名 |
| --- | --- |
| 咨询 | [T2`Contact/index.twig`](http://doc4.ec-cube.net/documents/updatedoc/4.0.1/Contact_index_twig.htm) |
| 会员注册(输入页) | [T2`Entry/index.twig`](http://doc4.ec-cube.net/documents/updatedoc/4.0.1/Entry_index_twig.htm) |
| 我的页面/会员注册内容变更(输入页面) | [T2`Mypage/change.twig`](http://doc4.ec-cube.net/documents/updatedoc/4.0.1/Mypage_change_twig.htm) |
| 添加我的页面/收件人 | [T2`Mypage/delivery_edit.twig`](http://doc4.ec-cube.net/documents/updatedoc/4.0.1/Mypage_delivery_edit_twig.htm) |
| Purchase goods | [T2`Shopping/index.twig`](http://doc4.ec-cube.net/documents/updatedoc/4.0.1/Shopping_index_twig.htm) |
| Non-member purchasing intelligence input | [T2`Shopping/nonmember.twig`](http://doc4.ec-cube.net/documents/updatedoc/4.0.1/Shopping_nonmember_twig.htm) |
| 添加商品购买/送货地址 | [T2`Shopping/shipping_edit.twig`](http://doc4.ec-cube.net/documents/updatedoc/4.0.1/Shopping_shipping_edit_twig.htm) |
| 指定多个商品购买/送货地点(添加送货地点) | [T2`Shopping/shipping_multiple_edit.twig`](http://doc4.ec-cube.net/documents/updatedoc/4.0.1/Shopping_shipping_multiple_edit_twig.htm) |

##### #7\. [启用维护功能(4.0.0 - >4.0.1 特有)](http://doc4.ec-cube.net/quickstart_update#400---401)

更新`eccube/.env`。
解除了以下 3 行的注释:

```
ECCUBE_LOCALE=ja
ECCUBE_ADMIN_ROUTE=admin
ECCUBE_TEMPLATE_CODE=default 
```

Enter fullscreen mode Exit fullscreen mode

##### #8\. `.htaccess`的变更内容的取入( OpenBSD 特有)

[将`.htaccess`的变更内容](https://github.com/EC-CUBE/ec-cube/compare/4.0.0...4.0.1?w=1#diff-8052c42ab3b8aa06a3f5f788a4ddccc2)吸收如下。

在`/etc/httpd.conf`的服务器定义中添加了以下`block`定义:

```
location "/.maintenance"                        { block } 
```

Enter fullscreen mode Exit fullscreen mode

将`/etc/php-72.ini`编辑如下:

```
; Maximum allowed size for uploaded files.
; http://php.net/upload-max-filesize
;upload_max_filesize = 2M upload_max_filesize = 5M 
```

Enter fullscreen mode Exit fullscreen mode

##### #9.更新完成

[重新启动 PHP-FPM](https://php-fpm.org/) / httpd :

```
#  rcctl restart php72_fpm
#  rcctl restart httpd 
```

Enter fullscreen mode Exit fullscreen mode

**注释:如果使用的是维护模式，请将其关闭。 可以通过删除 EC-CUBE 根目录的`.maintenance`文件来进行。 4.0.1 以后，也可以通过管理画面进行操作。*

<center>✿ ✿ ✿</center>

到此为止。
谢谢您的阅读。