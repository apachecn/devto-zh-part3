# PHP 枚举

> 原文：<https://dev.to/brendt/php-enums-2831>

枚举类型，简称“enum”，是对命名值进行分类的数据类型。枚举可以用来代替硬编码的字符串，例如，以结构化和类型化的方式来表示博客文章的状态。

PHP 没有本地枚举类型。它提供了一个非常基本的 [SPL 实现](http://php.net/manual/en/class.splenum.php)，但是这真的不能说明问题。

有一个由 Matthieu Napoli 编写的流行包叫做 T2 myc labs/PHP-enum T3。这是我和其他许多人在无数项目中使用的一个包。真的很牛逼。

今天我想探讨一下我们在解决像用户世界中的枚举这样的问题时遇到的一些困难。我将谈谈我个人对 enums 的看法，我们将思考核心支持。

继续阅读 https://stitcher.io/blog/php-enums