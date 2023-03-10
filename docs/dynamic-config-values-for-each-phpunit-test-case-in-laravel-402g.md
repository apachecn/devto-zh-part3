# Laravel 中每个 PHPUnit 测试用例的动态配置值

> 原文：<https://dev.to/dbilovd/dynamic-config-values-for-each-phpunit-test-case-in-laravel-402g>

*这篇文章最初发表在我的[博客](http://dbilovd-blog.herokuapp.com/dynamic-config-phpunit-laravel)T3 上*

### 问题:

我目前正在开发一个 USSD 应用程序，它支持在运行时通过更改配置值在不同的网关提供者之间进行切换。

测试每个提供者测试用例既简单又直接。我将更新我的 phpunit.xml 文件中的值，然后离开。然而，当我想运行我的整个测试套件时，我遇到了问题。

### 解:

事实证明，解决方案很简单:门面。Laravel 允许在编写测试时模仿任何外观。然而，文档不鼓励嘲笑配置外观。相反，应该使用 Config::set 方法。

因此，我按照下面的代码做了一些事情。

```
<?php
// define your namespace and dependencies here

use Illuminate\Support\Facades\Config;

class GatewayProviderOneTest extends TestCase
{
    public function setUp ()
    {
        parent::setUp();
        Config::set("defaultGatewayProvider", "firstProvider");
    }

    // ... your test methods come here
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我所需要的。现在，GatewayProviderOneTest 将针对设置为 defaultGatewayProvider:first provider 的值运行其断言。

此外，每次在您的 tearDown 方法中，您都可以将其恢复为默认值。然而，我不需要这样做。