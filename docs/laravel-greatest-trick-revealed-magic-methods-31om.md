# 拉勒维尔最大的魔术揭秘:魔术方法

> 原文：<https://dev.to/nvio/laravel-greatest-trick-revealed-magic-methods-31om>

[![Photo by Mervyn Chan on Unsplash](img/ee75d40beeb94502ef1b9744a330366e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D-aJRe8W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d7oeukl09h4vemh75dl0.png) 
*由默文·陈在 Unsplash 上拍摄*

Laravel 将 PHP 提升到了一个全新水平，为您的下一个项目提供了出色的开发者体验。于是，有人将其定性为“神奇”。

今天，我将向你揭示一个拉勒维尔的把戏，**魔术方法**。

# 什么是魔法？

重要的是要理解魔术方法并不是 Laravel 独有的，而是在您拥有的任何 PHP 应用程序中都可以使用。Laravel 恰好有一些关于魔法方法的最有趣的用例。

魔术方法是在 PHP 中声明的任何类中都可用的方法，它提供了在类中实现附加功能的方法。

这里有一个很好的定义:

> 程序员永远不会直接调用魔法方法——实际上，PHP 会在“幕后”调用该方法。这就是为什么它们被称为“神奇”的方法——因为它们从不被直接调用，它们允许程序员做一些非常强大的事情。

总共有 15 种魔法方法:

```
class MyClass
{
    public function __construct() {}

    public function __destruct() {}

    public function __call() {}

    public function __callStatic() {}

    public function __get() {}

    public function __set() {}

    public function __isset() {}

    public function __unset() {}

    public function __sleep() {}

    public function __wakeup() {}

    public function __toString() {}

    public function __invoke() {}

    public function __set_state() {}

    public function __clone() {}

    public function __debuginfo() {}
} 
```

如果你用 PHP 做过一些面向对象的编程，你肯定会认出`__construct`方法，这也是一个神奇的方法。原来你一直都在用魔法方法！

您还会注意到，按照惯例，所有的魔法方法都带有前缀`__`。

今天，我们不会深入讨论所有的代码，而只会讨论 Laravel 代码库中使用的有趣的代码。如果其他人对您感兴趣，请随时查看下面的文档👇

[PHP:神奇方法-手动](https://www.php.net/manual/fr/language.oop5.magic.php)

# 拉勒维尔如何使用魔法方法

## `__get()`

Laravel 的模特真的很特别。它们不将属性数据作为类的直接属性存储，而是存储在属性`protected $attributes`中，这是模型保存的所有数据的关联数组。

让我们来看看简单的 PHP 类和 Laravel 模型访问属性的区别。

```
<?php

/**
 * A normal user class in PHP (without Laravel) will be just a class with the said attributes
 */
class NormalUser
{
    public $firstName = 'Alice';
}

$normalUser = new NormalUser;

$normalUser->firstName; // Will return 'Alice' 
```

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

/**
 * A user class in Laravel
 */
class LaravelUser extends Model
{
    /**
     * Note that we store all the attributes data in one and single array
     */
    protected $attributes = [
        'firstName' => 'Alice',
    ];
}

$laravelUser = new LaravelUser;

$laravelUser->firstName; // Will return 'Alice' as well 
```

我们可以看到上面的 PHP 和 Laravel 类的行为完全相同。然而，在 Laravel 中，属性并不像普通 PHP 那样存储，而是所有属性都集中在一个名为`$attributes`的属性中。我们仍然能够访问正确的数据，但是怎么访问呢？

这都是因为有了`__get`魔法方法才有可能。让我们试着在一个例子中实现我们自己的简化版本。

```
<?php

class NormalUser
{
    /**
     * We declare the attributes that same way as in Laravel
     */
    protected $attributes = [
        'firstName' => 'Alice',
    ];

    /**
     * The function __get receive one parameter
     * which will be the name of the attribute you want to access
     * in this case $key = "firstName"
     */
    public function __get(string $key)
    {
        return $this->attributes[$key];
    }
}

$normalUser = new NormalUser;

$normalUser->firstName; // Will return 'Alice' 
```

我们做到了！🎉

我们需要注意的是，只有在类中找不到具有匹配名称的属性时，才会调用神奇的方法`__get`。这是当 PHP 在类中找不到 asked 属性时调用的一种后备方法。所以在下面的例子中，魔法方法`__get`根本不会被调用。

```
<?php

class NormalUser
{
    public $firstName = 'Bob';

    protected $attributes = [
        'firstName' => 'Alice',
    ];

    public function __get($key)
    {
        return $this->attributes[$key];
    }
}

$normalUser = new NormalUser;

/**
 * Will return 'Bob' as the attribute exists in the class
 * so the magic method __get doesn't get call in this case
 */
$normalUser->firstName; 
```

幕后还有更多的事情在发生。如果你想深入了解 Laravel 的模型到底是如何使用`__get`的，你可以查看下面的源代码。

[laravel/framework](https://github.com/laravel/framework/blob/0207d73d7daae2f5902a76136780324b0cdfbb1c/src/Illuminate/Database/Eloquent/Model.php#L1519)

## `__set()`

当试图设置的属性没有在类中声明时，使用神奇的方法`__set`。让我们再次看看普通 PHP 类和 Laravel 中的模型之间的区别。

```
<?php

class NormalUser
{
    public $firstName = 'Alice';
}

$normalUser = new NormalUser;

$normalUser->firstName = 'Bob';

$normalUser->firstName; // Will return 'Bob' 
```

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class LaravelUser extends Model
{
    protected $attributes = [
        'firstName' => 'Alice',
    ];
}

$laravelUser = new LaravelUser;

$laravelUser->firstName = 'Bob';

$laravelUser->firstName; // Will return 'Bob' as well 
```

正如我们所看到的，在这个例子中，我们仍然试图将值`Bob`影响到一个属性，这个属性实际上并不存在于类中，而是存在于属性`$attributes`中。让我们试着用神奇的方法`__set`实现那个确切的行为。

```
<?php

class NormalUser
{
    public $attributes = [
        'firstName' => 'Alice',
    ];

    /**
     * The magic method __set receives the $name you want to affect the value on
     * and the value
     */
    public function __set($key, $value)
    {
        $this->attributes[$key] = $value;
    }
}

$normalUser = new NormalUser;

$normalUser->firstName = 'Bob';

/**
 * As we don't have the __get magic method define in this example for simplicity sake,
 * we will access the $attributes directly
 */
$normalUser->attributes['firstName']; // Will return 'Bob' 
```

开始了。我们成功地在 Laravel 中实现了`__get`和`__set`魔术方法的基本用法！只需要几行代码！

请记住，这些神奇的方法尽可能简单，不要进入太多的细节，因为它不仅仅是那些用例，如果你对它的工作原理感到好奇，我邀请你自己深入代码进行一些探索！(如果您有任何问题，也可以随时在 Twitter 上联系我)

同样，如果你想了解更多，这里有源代码的链接

[laravel/framework](https://github.com/laravel/framework/blob/0207d73d7daae2f5902a76136780324b0cdfbb1c/src/Illuminate/Database/Eloquent/Model.php#L1531)

让我们进入最后一个也是最有趣的案例！🙌

## [T3`__call()`T4】T1】](#-raw-call-endraw-amp-raw-callstatic-endraw-)

当调用了一个方法，但在特定的类中没有找到时，执行`__call`。在 Laravel 中，这个神奇的方法使 PHP 中的宏成为可能。

我不会深入宏的所有细节，但是如果你感兴趣，这里有一篇很好的文章，解释了如何在你的 Laravel 应用程序中使用它们👇

[Laravel 宏的魔力](https://tighten.co/blog/the-magic-of-laravel-macros)

让我们试着看看如何用普通 PHP 重现一个简单的宏示例。

```
<?php

class NormalUser
{
    public $firstName = 'Alice';

    public $lastName = 'Bob';
}

$normalUser = new NormalUser;

$normalUser->fullName(); // This will throw an error as no method "fullName" has been declared. 
```

现在使用`__call`，我们可以定义一个包含闭包函数的数组，这样我们就可以在应用程序中以编程方式添加这些函数。

```
<?php

class NormalUser
{
    public $firstName = 'Alice';

    public $lastName = 'Bob';

    /**
     * We initialise our macros as an empty array that we will fill
     */
    public static $macros = [];

    /**
     * We define this method to add new macro as we go
     * the first argument will be the name of the macro we want to define
     * the second will be a Closure function that will be executed when calling the macro
     */
    public static function addMacro($name, $macro) {
        static::$macros[$name] = $macro;
    }

    /**
     * "__call" receives two parameters,
     * $name which will be the name of the function called in our case "fullName"
     * $arguments which will be all the arguments passed in the function in our case it'll just be an empty array as we can't pass any arguments in our function
     */
    public function __call(string $name, array $arguments) {
        /**
         * We retrieve the macro with the right name
         */
        $macro = static::$macros[$name];
        /**
         * Then we execute the macro with the arguments
         * Note: we need to bind the Closure with "$this" before calling it to allow the macro method to act in the same context
         */
        return call_user_func_array($macro->bindTo($this, static::class), $arguments);
    }
}

$normalUser = new NormalUser;

$normalUser->fullName(); // This will still break as we didn't define the macro "fullName" yet and the method "fullName" doesn't exist either

/**
 * We add the macro function "fullName"
 */
NormalUser::addMacro('fullName', function () {
    return $this->firstName.' '.$this->lastName;
});

$normalUser->fullName(); // Now, returns "Alice Bob" 
```

宏比这个稍微复杂一点，但是我们通过使用`__call`魔法方法创建了一个简单的宏工作版本。

除了静态方法外，`__callStatic`的工作方式与`__call`完全相同。

同样，如果你想自己挖掘更多，这里有`Macroable` trait 源代码

[laravel/framework](https://github.com/laravel/framework/blob/e6c8aa0e39d8f91068ad1c299546536e9f25ef63/src/Illuminate/Support/Traits/Macroable.php)

## 最终取

女士们先生们，这就对了，虽然当你第一次开始使用 Laravel 时，它确实让人感觉到*神奇*,但是通过查看源代码本身，你就能理解这种“神奇”是如何在幕后运作的。

就像现实生活中的魔法一样，没有解释就没有事情发生，在代码中更是如此。总有一行代码在某个地方完成工作，你只需要找到它。

我鼓励你更多地挖掘 Laravel，让魔法消失，不要忘记在 Twitter 上与我分享你的金块！

*这篇博文最初发表在[我的个人博客](https://stevenyung.me/laravel-greatest-trick-revealed-magic-methods/)上，请拥抱一下🤗*

*特别感谢:[塞缪尔](https://twitter.com/aukraiser)、[让·巴普蒂斯特](https://github.com/veronj)和[杰西卡](https://twitter.com/Jessica_thlmns)*