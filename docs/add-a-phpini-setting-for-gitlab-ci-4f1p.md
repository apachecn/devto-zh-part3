# 为 gitlab-ci 添加 php.ini 设置

> 原文：<https://dev.to/c33s/add-a-phpini-setting-for-gitlab-ci-4f1p>

有时您需要在 gitlab-ci 中动态地更改 php.ini 设置。这很容易。

简单运行如下:

```
echo 'phar.readonly="0"' >> /etc/php/7.1/cli/conf.d/ci.ini 
```

我使用了我的 debian stretch docker 文件，它使用 php 的[https://deb.sury.org/](https://deb.sury.org/)库。您的目录结构可能有所不同。

您可以通过运行以下命令找到 php 从哪里加载配置文件:

```
php --ini 
```

这将打印一个类似如下的列表:

```
$ php --ini
Configuration File (php.ini) Path: /etc/php/7.1/cli
Loaded Configuration File:         /etc/php/7.1/cli/php.ini
Scan for additional .ini files in: /etc/php/7.1/cli/conf.d
Additional .ini files parsed:      /etc/php/7.1/cli/conf.d/10-mysqlnd.ini,
/etc/php/7.1/cli/conf.d/10-opcache.ini,
/etc/php/7.1/cli/conf.d/10-pdo.ini,
/etc/php/7.1/cli/conf.d/15-xml.ini,
/etc/php/7.1/cli/conf.d/20-apcu.ini,
/etc/php/7.1/cli/conf.d/20-apcu_bc.ini,
/etc/php/7.1/cli/conf.d/20-bcmath.ini,
/etc/php/7.1/cli/conf.d/20-bz2.ini,
/etc/php/7.1/cli/conf.d/20-calendar.ini,
/etc/php/7.1/cli/conf.d/20-ctype.ini,
... 
```

在 debian 中，`conf.d`目录中的所有文件都包含在内。所以我们只需要把它放在这个目录里。您还可以将配置附加到主文件`php.ini`中。

`gitlab-ci.yml`中的相关部分:

```
image: c33s/php:7.1
...
build:
  stage: build
  script:
    - composer install
    - php --ini
    - echo 'phar.readonly="0"' >> /etc/php/7.1/cli/conf.d/ci.ini
    - wget https://github.com/box-project/box2/releases/download/2.7.5/box-2.7.5.phar
    - php box-2.7.5.phar build
    - php satis.phar --version
... 
```

*   封面图片由 www _ darkworkx _ de[https://pix abay . com/photos/devo PS-business-process-improvement-3155972/](https://pixabay.com/photos/devops-business-process-improvement-3155972/)