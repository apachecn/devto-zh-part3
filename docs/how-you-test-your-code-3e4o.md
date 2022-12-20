# 你如何测试你的代码？

> 原文：<https://dev.to/samsha1/how-you-test-your-code-3e4o>

[![unit test](img/3e75ba945ee4068f2b8de35c305c5eeb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4_quP5Oj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p1zwh1fe58ubatubcqhq.jpg)

测试代码是理解你的应用程序和代码行为的最重要的部分之一。每个开发人员都知道 bug 有多痛苦，尤其是在生产阶段，因为这需要数小时的艰苦工作。测试每个场景就像相信你自己一样好。您编写并执行您的代码，但不知道在发生异常或违反验证时它会如何表现，这可能会导致在登台或生产中出现问题。因此，运行测试用例在 SDLC 中起着至关重要的作用。一旦你的测试覆盖率达到一个好的水平，你就赋予了丰富的生命，闭着眼睛相信你的代码。

今天，我们将编写一个简单的测试方法，并使用 Xdebug 和 PHPUnit 等工具运行测试代码。PHPUnit 是一个面向程序员的测试框架。这是为 PHP Web 应用程序编写单元测试的优秀测试框架。在 PHPUnit 的帮助下，我们可以指导测试驱动的改进。

让我们安装 PHPUnit:

```
wget https://phar.phpunit.de/phpunit-7.5.phar
chmod +x phpunit-7.5.phar
sudo mv phpunit-7.5.phar /usr/bin/phpunit 
```

还有其他几种安装 phpunit 方法。**在这里找到更多** : [phpunit](https://phpunit.readthedocs.io/en/8.0/installation.html) 。

现在，为了获得我们的测试覆盖完整报告，我们需要安装 Xdebug:

```
sudo apt install php-pear
pecl install xdebug
sudo apt-get install php-xdebug 
```

在这里找到更多关于 **Xdebug** 的信息: [xdebug](https://xdebug.org/docs/install) 。

不再拖延，让我们深入到简单的测试场景中，测试数组是否为空:

创建一个文件夹 **UnitTest** ，我们将在其中包含所有可测试的文件。在这个文件夹中，创建一个子文件夹 **tests** 。在这个子文件夹中创建一个新文件`phpunit.xml`，并添加以下代码。