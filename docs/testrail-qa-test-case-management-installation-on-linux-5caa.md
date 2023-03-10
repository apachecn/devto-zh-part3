# TestRail:在 Linux 上安装 QA 测试用例管理

> 原文：<https://dev.to/setevoy/testrail-qa-test-case-management-installation-on-linux-5caa>

*[![](img/1808aa4fb39cfb9835d5f9e7b34f6a21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t95deDEw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2019/03/testrail-logo.png)TestRail——面向 QA 和开发团队的测试用例管理软件。*

嗯——这些都是描述所需要的)

下面——它在 Debian Linux 上安装了 NGINX，Let's Encrypt，PHP-FPM，MariaDB 和 Exim。

由于 TestRail 只是一个简单的 PHP/MySQL 应用程序——所以这篇文章可能更有意思，因为它是一个快速 LEMP SSL 设置过程描述的例子，而不是 TestRail 本身。

项目主页-[www.gurock.com/testrail](https://www.gurock.com/testrail)

安装官方文档—[此处> > >](http://docs.gurock.com/testrail-admin/start) 。

安装将在 AWS EC2 上进行。

### LEMP 和 SSL

登录到 EC2:

```
ssh admin@testrail.example.com -i bttrm-testrail-eu-west-1.pem 
```

更新软件包列表和已安装的软件包:

```
root@ip-172-31-25-137:/home/admin# apt update && apt upgrade 
```

#### 让我们加密

安装加密客户端:

```
root@ip-172-31-25-137:/home/admin# apt install -y git unzip
root@ip-172-31-25-137:/home/admin# git clone https://github.com/letsencrypt/letsencrypt /opt/letsencrypt 
```

使用独立验证器获取证书:

```
root@ip-172-31-25-137:/home/admin# /opt/letsencrypt/letsencrypt-auto certonly -d testrail.example.com
...
1: Spin up a temporary webserver (standalone)
2: Place files in webroot directory (webroot)
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 1
...
- Congratulations! Your certificate and chain have been saved at:
/etc/letsencrypt/live/testrail.example.comd/fullchain.pem
Your key file has been saved at:
/etc/letsencrypt/live/testrail.example.com/privkey.pem
Your cert will expire on 2019-06-19.
... 
```

#### NGINX

安装 NGINX:

```
root@ip-172-31-25-137:/home/admin# apt -y install nginx 
```

稍后将对其进行配置。

#### PHP

添加 PHP 7.2 库:

```
root@ip-172-31-25-137:/home/admin# wget -q https://packages.sury.org/php/apt.gpg -O- | sudo apt-key add -
OK
root@ip-172-31-25-137:/home/admin# echo "deb https://packages.sury.org/php/ stretch main" | sudo tee /etc/apt/sources.list.d/php.list
deb https://packages.sury.org/php/ stretch main 
```

更新可用软件包列表:

```
root@ip-172-31-25-137:/home/admin# apt update 
```

安装 PHP 和 TestRail 所必需的模块:

```
root@ip-172-31-25-137:/home/admin# apt -y install php7.2 php7.2-fpm php7.2-mysql php7.2-curl php7.2-json php7.2-mbstring php7.2-xml php7.2-zip 
```

查找 PHP 配置:

```
root@ip-172-31-25-137:/home/admin# find /etc/ -name php.ini
/etc/php/7.2/cli/php.ini
/etc/php/7.2/fpm/php.ini 
```

编辑`/etc/php/7.2/fpm/php.ini`和`/etc/php/7.2/cli/php.ini`并添加库加载:

```
...
extension=mysql.so
extension=curl.so 
```

##### ionCube 安装

下载其存档:

```
root@ip-172-31-25-137:/home/admin# cd /tmp/
root@ip-172-31-25-137:/tmp# wget https://downloads.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz 
```

拆开包装，移至`/opt` :

```
root@ip-172-31-25-137:/tmp# tar xfpz ioncube_loaders_lin_x86-64.tar.gz
root@ip-172-31-25-137:/tmp# mv ioncube /opt/ 
```

如上例所示，将`zend_extension`添加到两个 ini 文件中:

```
...
extension=mysql.so
extension=curl.so
zend_extension=/opt/ioncube/ioncube_loader_lin_7.2.so 
```

#### NGINX 虚拟主机配置

生成测向键:

```
root@ip-172-31-25-137:/tmp# openssl dhparam -out /etc/nginx/dhparams.pem 2048 
```

添加虚拟主机配置`/etc/nginx/conf.d/testrail.example.com` :

```
server {
    listen 80;
    server_name testrail.example.com;
    # Lets Encrypt Webroot
    location ~ /.well-known {
        root /var/www/html;
        allow all;
    }
    location / {
        # office1
        allow 194.***.***.24/29;
        # office2
        allow 91.***.***.78/32;
        # arseny home
        allow 188.***.***.48/32;
        deny  all;
        return 301 https://testrail.example.com;
    }
}
server {
    listen       443 ssl;
    server_name  testrail.example.com;
    root /var/www/testrail.example.com;
    access_log  /var/log/nginx/testrail.example.com-access.log;
    error_log /var/log/nginx/testrail.example.com-error.log warn;
    # office1
    allow 194.***.***.24/29;
    # office2
    allow 91.***.***.78/32;
    # arseny home
    allow 188.***.***.48/32;
    deny  all;
    ssl_certificate /etc/letsencrypt/live/testrail.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/testrail.example.com/privkey.pem;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    ssl_dhparam /etc/nginx/dhparams.pem;
    ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:ECDHE-RSA-AES128-GCM-SHA256:AES256+EECDH:DHE-RSA-AES128-GCM-SHA256:AES256+EDH:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";
    ssl_session_timeout 1d;
    ssl_stapling on;
    ssl_stapling_verify on;
    location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar|otf|ttf)\$ {
        try_files \$uri =404;
    }
    location / {
        try_files $uri $uri/ /index.php?$args;
    }
    location ~* ^.+\.(jpg|jpeg|gif|png|ico|css|js|woff)$ {
        access_log off;
        log_not_found off;
        expires max;
    }
    location ~ \.php(?:/|$) {
        try_files $uri =404;
        fastcgi_pass unix:/run/php/testrail.example.com.sock;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_intercept_errors on;
        fastcgi_index index.php;
    }
} 
```

检查语法并重新加载配置文件:

```
root@ip-172-31-25-137:/tmp# nginx -t && service nginx reload 
```

#### PHP-FPM 配置

创建一个池的配置文件`/etc/php/7.2/fpm/pool.d/testrail.example.com.conf` :

```
[testrail.example.com]
user = www-data
group = www-data
listen = /run/php/testrail.example.com.sock
listen.owner = www-data
listen.group = www-data
pm = dynamic
pm.max_children = 5
pm.start_servers = 2
pm.min_spare_servers = 1
pm.max_spare_servers = 3
;php_admin_value[sendmail_path] = /usr/sbin/sendmail -t -i -f www@my.domain.com
;php_flag[display_errors] = off
;php_admin_value[error_log] = /var/log/fpm-php.www.log
;php_admin_flag[log_errors] = on
;php_admin_value[memory_limit] = 32M 
```

重新启动 FPM 并检查新池是否已启动:

```
root@ip-172-31-25-137:/tmp# service php7.2-fpm restart
root@ip-172-31-25-137:/tmp# ps aux | grep fpm
root     15008  0.5  2.7 400684 28368 ?        Ss   11:00   0:00 php-fpm: master process (/etc/php/7.2/fpm/php-fpm.conf)
www-data 15010  0.0  0.6 400676  6904 ?        S    11:00   0:00 php-fpm: pool testrail.example.com
www-data 15011  0.0  0.6 400676  6904 ?        S    11:00   0:00 php-fpm: pool testrail.example.com
www-data 15012  0.0  0.6 400676  6904 ?        S    11:00   0:00 php-fpm: pool www
www-data 15013  0.0  0.6 400676  6904 ?        S    11:00   0:00 php-fpm: pool www 
```

##### PHP 测试

创建工作目录:

```
root@ip-172-31-25-137:/tmp# mkdir -p /var/www/testrail.example.com 
```

创建`/var/www/testrail.example.com/info.php`文件:

```
<?php
phpinfo()
?> 
```

在浏览器中签入:

[![](img/fee105fa9e594ad5c274379bc8f70740.png "TestRail: QA Test Case Management система - установка на Linux")](https://rtfm.co.ua/wp-content/uploads/2019/03/Screenshot_20190321_131113.png)

### [MySQL](#mysql)

安装它:

```
root@ip-172-31-25-137:/tmp# apt -y install mariadb-server 
```

运行初始配置:

```
root@ip-172-31-25-137:/tmp# mysql_secure_installation 
```

创建数据库和用户:

```
MariaDB [(none)]> CREATE DATABASE testrail DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> GRANT ALL ON testrail.* TO 'testrail'@'localhost' identified by 'uo***8a';
Query OK, 0 rows affected (0.00 sec) 
```

### TestRail 安装

在这里> > > 得到你的试炼[。](https://secure.gurock.com/customers/testrail/trial/)

您将收到一封电子邮件，其中包含下载链接和试用密钥。

下载档案:

```
root@ip-172-31-25-137:/tmp# wget https://secure.gurock.com/downloads/testrail/testrail-latest-ion70.zip 
```

解包并移动到项目的目录:

```
root@ip-172-31-25-137:/tmp# unzip testrail-latest-ion70.zip
root@ip-172-31-25-137:/tmp# cp -r testrail/* /var/www/testrail.example.com/
root@ip-172-31-25-137:/tmp# chown -R www-data:www-data /var/www/testrail.example.com/ 
```

在浏览器中打开您的 URL，并在那里继续安装:

[![](img/1c33df9ac9c16ef179d8f86e21bc09fb.png "TestRail: QA Test Case Management система - установка на Linux")](https://rtfm.co.ua/wp-content/uploads/2019/03/Screenshot_20190321_133214.png)

配置数据库连接详细信息:

[![](img/8ecca4733cb6d7e82141c71e5def6acf.png "TestRail: QA Test Case Management система - установка на Linux")](https://rtfm.co.ua/wp-content/uploads/2019/03/Screenshot_20190321_134833.png)

创建日志目录:

```
root@ip-172-31-25-137:/tmp# mkdir /var/www/testrail.example.com/logs
root@ip-172-31-25-137:/tmp# chown -R www-data:www-data /var/www/testrail.example.com/ 
```

设置项目的详细信息:

[![](img/ef01fc82456da20535c160b330c9b6f4.png "TestRail: QA Test Case Management система - установка на Linux")](https://rtfm.co.ua/wp-content/uploads/2019/03/Screenshot_20190321_134913.png)

稍后将设置电子邮件，现在跳过它:

[![](img/280b964964f5a740920a99bacd23d0e8.png "TestRail: QA Test Case Management система - установка на Linux")](https://rtfm.co.ua/wp-content/uploads/2019/03/Screenshot_20190321_135019.png)

添加管理员用户，试用密钥通过电子邮件发送:

[![](img/54da004cc930ac32ebadd4036d877092.png "TestRail: QA Test Case Management система - установка на Linux")](https://rtfm.co.ua/wp-content/uploads/2019/03/Screenshot_20190321_135048.png)

就绪:

[![](img/4e9e6099dab2888294e933a208c5c419.png "TestRail: QA Test Case Management система - установка на Linux")](https://rtfm.co.ua/wp-content/uploads/2019/03/Screenshot_20190321_135141.png)

登录:

[![](img/bfbdac42333172c0a6865edc302cb7fd.png "TestRail: QA Test Case Management система - установка на Linux")](https://rtfm.co.ua/wp-content/uploads/2019/03/Screenshot_20190321_135226.png)

#### 克朗乔布斯

现在，添加一个 cronjob 来运行 TestRail 的任务。

检查`task.php`是否工作:

```
root@ip-172-31-25-137:/home/admin# php /var/www/testrail.example.com/task.php
root@ip-172-31-25-137:/home/admin# echo $? 
```

如果这里没有错误，可以添加到 crontab:

```
* * * * * www-data /usr/bin/php /var/www/testrail.example.com/task.php 
```

#### Exim 和邮件设置

发送电子邮件–将使用本地 Exim。

安装它:

```
root@ip-172-31-25-137:/home/admin# apt -y install exim4-base 
```

[配置](https://rtfm.co.ua/exim-mailing-to-remote-domains-not-supported/) it:

```
root@ip-172-31-25-137:/home/admin# dpkg-reconfigure exim4-config 
```

检查邮件发送是否正常:

```
root@ip-172-31-25-137:/home/admin# echo "Exim test" | mailx -s "Exim test" admin@example.com 
```

进入 TestRail 管理页面-*管理>站点设置>电子邮件*:

[![](img/c8a7e57b5b673212853d3763cbee584f.png "TestRail: QA Test Case Management система - установка на Linux")](https://rtfm.co.ua/wp-content/uploads/2019/03/Screenshot_20190322_143224.png)

完成了。

### 类似的帖子

*   <small>02/15/2016</small>[Debian:установкаLEMP–NGINX+PHP-FPM+MariaDB](https://rtfm.co.ua/debian-ustanovka-lamp-nginx-php-fpm-mariadb/)<small>(0)</small>
*   <small>02/10/2018</small>[ansi ble:миграцияrtfm 2.10——让我们加密，NGINX SSL，主机名и exim](https://rtfm.co.ua/ansible-migraciya-rtfm-2-10-lets-encrypt-nginx-ssl-hostname-i-exim/) <small>(0)</small>
*   <small>02/26/2019</small>[OpenVPN:SSL 和主机名配置](https://dev.to/setevoy/openvpn-ssl-and-hostname-configuration-2nb9) <small>(0)</small>
*   <small>2016 年 12 月 4 日</small>[【TLS/SSL:ngix】【kg9】【SSL】让我们加密 debian 8](https://rtfm.co.ua/nginx-nastrojka-lets-encrypt-na-debian-8/)<small></small>
<small></small>

<small></small>