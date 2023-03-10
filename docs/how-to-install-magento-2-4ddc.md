# 如何安装 Magento 2？

> 原文：<https://dev.to/bhavikrocktech/how-to-install-magento-2-4ddc>

Magento 2 是上一版本 Magneto 1 的最新升级版，该版本正式发布，采用了现代技术和简化的定制以及修改的目录，很快成为 200，000 多家在线零售商最喜爱的电子商务平台。

与其他平台相比，Magento 1 因加载速度慢而臭名昭著。升级到 Magento 2 是由于它的快速和临时缓存设置。Magento 2 有一个简单直观的界面，允许你使用内置的 WYSIWYG 编辑器或直接编写 HTML 源代码为每篇文章创建独特的风格。Magento 质量的关键在于它的标准结构、强大的设计和底层技术。为了让 Magento 2 在你的在线商店上运行，这里有完整的 Magento 2 安装指南。

[如何安装 MAGENTO 2？](https://www.mageants.com/blog/how-to-install-magento-2.html)

第一步:下载 Magento 2 包
第二步:验证先决条件
第三步:从不同的归档分类(zip、tar.bz2、tar.bz2)中选择安装方法，有和没有示例数据。
第四步:上传到你的服务器
第五步:运行检查
这只是基本的安装方式。现在我们来详细看看如何在不同的 OS 中安装 Magento 2。在此之前，以下是 Magento 2 安装必须满足的通用系统要求。

系统需求

操作系统:Windows、Linux 发行版，如 RedHat Enterprise Linux (RHEL)、CentOS、Ubuntu、Debian 等等。如果 RAM 小于 2GB，我们倾向于建议您生成一个交换文件；否则，您的升级可能会失败。

Composer:最新的稳定版本，开发 Magento 扩展所必需的..

Web 服务器:Apache 2.2 或更高版本

PHP: 5.4.11，5.5.x，7 . 0 . 13–7.0 . x，7.1.x(注:Magento 2 不支持 PHP 5.6。)

必需的 PHP 扩展:PDO/MySQL、mbstring、mcrypt、mhash、simplexml、curl、gd2、ImageMagick 6.3.7(或更高版本)或两者都是 soap

数据库:MySQL 5.6，5.7

邮件传输代理(MTA)或 SMTP 服务器

可选但推荐:
php_xdebug2.2.0 或更高版本(仅限开发环境；会对性能产生不利影响)
PHPUnit(作为命令行工具)4.1 或更高版本
SSL:HTTPS 需要有效的安全证书。(注意:不支持自签名 SSL 证书。)

[如何在 WINDOWS 10 上安装 MAGENTO 2？](https://www.mageants.com/blog/how-to-install-magento-2.html)

第一步:从[https://sourceforge.net/projects/xampp/](https://sourceforge.net/projects/xampp/)T2 在你的电脑上安装 XAMPP 服务器第二步:配置 Xampp。运行您的 XAMPP web 环境并启动 apache 和 MySQL 服务
步骤 3:通过点击 Apache 的“config”按钮并选择 php.ini 配置文件来启用指定的 php 扩展。
步骤 4:在配置文件中，通过使用“ctrl+f”找到扩展行来启用扩展“intl”和“xsl”。删除该行前的分号以取消注释，最后保存并关闭文件。现在，停止并启动 Apache 服务。
第五步:在你的 Windows 机器上下载 zip 格式的 Magento 2。你也可以在 Git GUI 软件中从 https://github . com/magento/magento 2
步骤 6:使用默认设置，通过 Composer-Setup.exe 文件下载并安装 Composer。指定 php.exe 文件的路径。提供路径并点击 next，完成
步骤 7:通过解压 XAMPP 安装目录下 htdocs 文件夹中的 zip 文件，使用 Composer 安装 Magento2 依赖项。转到 Magento2 文件夹，选择“在此打开命令窗口”。这将在该位置打开命令提示符。在命令提示符下运行命令“composer install”。这将安装 Magento2 所需的依赖项。
第八步:创建一个 MySQL 数据库。打开您最喜欢的网络浏览器。输入 URL "[http://localhost/phpmyadmin "](http://localhost/phpmyadmin%E2%80%9D)并按回车键打开 phpmyadmin 页面。
步骤 9:进入菜单>新建，创建一个名为 magento2 的数据库。
第十步:安装 Magento2。打开 web 浏览器，输入 URL "[http://localhost/magento 2 "](http://localhost/magento2%E2%80%9D)并点击 enter。您将看到一个 Magento 安装程序屏幕。单击“readiness check”以验证所有依赖项是否都存在。单击下一步配置数据库。根据货币、语言和时区，配置您的网络店面并单击“自定义您的商店”。创建一个“管理员帐户”来设置 Magento 管理员并执行所有需要的操作。在欢迎页面，点击同意并安装 Magento。

[如何在 UBUNTU 上安装 MAGENTO 2？](https://www.mageants.com/blog/how-to-install-magento-2.html)

步骤 1:通过运行以下命令安装 Apache2 HTTP 服务器；
sudo apt-get 更新
sudo apt-get -y 安装 apache2
步骤 2:使用以下命令启用 apache 重写和头模块
sudo a2enmod 重写
sudo a2enmod 头
sudo 服务 apache2 重启
步骤 3:使用 MySQL 客户端安装 MySQL 作为服务器- apt-get 安装 mysql-server mysql-client
步骤 4:现在根据要求更改 apache 配置文件- vim /etc/apache/etc/init.d/apache2 start
步骤 7:现在，为 Magento 2 设置准备好 MySQL DB。请按照步骤正确配置- mysql -u root -p
第八步:使用名称“my_store”(或者你喜欢的任何名称)创建一个 DB。mysql >创建数据库 my _ store
第九步:创建用户名“my _ store _ USER”MySQL>创建以‘USER _ pass’标识的用户 my _ store _ user @ localhost
第十步:接下来，设置“my_store”的权限。因为“我的商店”必须拥有法师数据库的所有权限:mysql >授予我的商店所有权限。*到 my _ store _ user @ localhost
第十一步:从 MageAnts 下载 Magento 2
第十二步:解压 Mage 2.2.0.zip 文件，并将内容复制到以下路径:
/var/www/html
解压 Magento-2 . 2 . 0 . zip
CP-Rrfv Magento/*/var/www/html/
第十三步:接下来，更改权限如下；chown-R www-data:www-data/var/www/html/
步骤 14:根据需求编辑文件权限- chmod -R 755 /var/www/html
步骤 15:最后使用以下命令重启 Apache&MySQL DB
/etc/init . d/Apache 2 重启
/etc/init.d/mysql 重启
步骤 16:在 Ubuntu 上安装 Magento 2
步骤 17[http://127 . 0 . 0 . 1/index . PHP/install](http://127.0.0.1/index.php/install)
第十八步:点击下一步继续设置。
步骤 19:进一步提供数据库凭证(用户名、通行证等。).第 20 步:屏幕上会出现两个选项。现在，点击后端。它将重定向到登录页面。然后使用您的管理员帐户详细信息登录，就大功告成了。

结论

[从 Magento 1 迁移到 Magento 2.0](https://www.mageants.com/magento-1-to-magento-2-migration.html) 。将在扩展、主题、定制开发和数据方面使您受益。此外，随着 MageAnts 的适当扩展添加到您的 Magento 2 bases 在线商店，销售和客户体验将像天空中的火箭一样飙升。