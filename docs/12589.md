# 让你的测试断言更具声明性

> 原文：<https://dev.to/shaikhul/hamcrest---make-your-test-assertion-more-declarative-4mme>

Hamcrest 框架最初是在 Java 世界中引入的，后来在其他语言(例如 PHP、Python、Ruby ),根据语言范围有一些例外。它提供了广泛的[匹配器](https://github.com/hamcrest/hamcrest-php#available-matchers)对象，使得测试断言更有意义，更容易理解。

我将使用 Hamcrest 的 [PHP](https://github.com/hamcrest/hamcrest-php) 实现。

这里是一个开始的例子。

#### 安装

Hamcrest-php 可以使用[composer](https://packagist.org/?query=hamcrest-php)安装

```
// first thing first, install hamcrest-php and include it, your vendor location may vary.

require __DIR__ . '/vendor/autoload.php'; 
```

Enter fullscreen mode Exit fullscreen mode

#### 快速用例- `assertThat`

下面是一个简单的`assertThat`匹配器的例子。

```
$result = true;
$expected = true;

assertThat($result, equalTo($expected)); 
```

Enter fullscreen mode Exit fullscreen mode

#### 给出一个好听的可选描述

你可以给这个断言一个很好的描述。

```
assertThat("Result should be true", $result, equalTo($expected)); 
```

Enter fullscreen mode Exit fullscreen mode

#### 添加装饰师/糖- `is`

Hamcrest 还提供了`is` decorator/sugar，让它变得超级声明性！

```
assertThat("Result should be true", $result, is(equalTo($expected))); 
```

Enter fullscreen mode Exit fullscreen mode

#### 断言否定- `not`

有时我们需要测试消极性，hamcrest 用`not` matcher 掩盖这一点。

```
$expected = true;
assertThat("Result should not be false", $result, is(not(equalTo($expected)))); 
```

Enter fullscreen mode Exit fullscreen mode

当断言失败时，它会显示这样的错误消息

```
Hamcrest\AssertionError with message 'Result should be true
Expected: <false>
     but: was <true>' 
```

Enter fullscreen mode Exit fullscreen mode

请随意查看其他支持语言的其他[匹配器](https://github.com/hamcrest/hamcrest-php#available-matchers)或 hamcrest！