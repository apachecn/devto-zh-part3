# 用 Mac 上的家酿升级到 PHP 7.3

> 原文：<https://dev.to/brendt/upgrade-to-php-7-3-with-homebrew-on-mac-2887>

## 用自制软件升级

首先确保 brew 是最新的:

```
brew update 
```

接下来，升级 PHP:

```
brew upgrade php 
```

通过运行`php -v` :
检查当前版本

```
php -v

# PHP 7.3.3 (cli) (built: Mar 8 2019 16:42:07) ( NTS )
# Copyright (c) 1997-2018 The PHP Group
# Zend Engine v3.3.3, Copyright (c) 1998-2018 Zend Technologies
# with Zend OPcache v7.3.3, Copyright (c) 1999-2018, by Zend Technologies 
```

继续阅读 https://stitcher.io/blog/php-73-upgrade-mac