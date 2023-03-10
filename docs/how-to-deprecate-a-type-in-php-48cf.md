# 如何在 php 中弃用类型

> 原文：<https://dev.to/greg0ire/how-to-deprecate-a-type-in-php-48cf>

所以你想把这个类或者接口重命名为别的东西，因为你需要把那个类型移动到另一个包中，或者你有新的编码标准规则需要应用到它的名字中。一种解决方案是使用继承并将所有代码转移到新类型:

```
<?php

// LegacyFoo.php

/**
 * @deprecated please use ShinyNewFoo instead!
 */
interface LegacyFoo extends ShinyNewFoo
{
} 
```

这种方法非常好，直到您的用户编写期望旧类型的代码，并从您的代码中获得新类型:

```
<?php

class Bar // registered and invoked by your library
{
    public function __invoke(LegacyFoo $foo) // crash
    {
    }
} 
```

但是不要害怕，还有另一种方法，那就是为你的类型创建一个别名。

```
<?php

// LegacyFoo.php

class_alias(ShinyNewFoo::class, LegacyFoo::class); 
```

这创建了从接口`LegacyFoo`到接口`ShinyNewFoo`的别名。你没看错，虽然它是`class_alias`并且我使用了`class`常量，但两者都适用于接口。但是这还不够，因为没有什么能保证`LegacyFoo`会在某个时候自动加载。在参数类型声明中使用该类型不会触发自动加载，因为当作为参数传递的对象被实例化时，该类型肯定应该自动加载。嗯，这种优化对类型别名不起作用，这意味着我们必须在`ShinyNewFoo.php`的底部手动触发自动加载。

```
<?php

// ShinyNewFoo.php

class_exists(LegacyFoo::class); 
```

以为这就完了？别急，还有更多。现在一切都正常了，让我们把它投入生产，看它燃烧起来！调用很少被使用，以至于 Composer 在生成其自动加载器类映射时并不寻找它们。[自动加载类映射](https://getcomposer.org/doc/articles/autoloader-optimization.md)是一种不用先检查文件系统就知道要加载哪些文件的方法，这样更快。为了欺骗 Composer 检测遗留类型，我们可以将它声明为一段死代码:

```
<?php

// LegacyFoo.php

class_alias(ShinyNewFoo::class, LegacyFoo::class);

if (false) {
    class LegacyFoo extends ShinyNewFoo
    {
    }
} 
```

这也将欺骗大多数 ide 提供自动完成。

最后，如果我们想在使用遗留类型时触发一个弃用错误，该怎么办？我们不能这样做，因为它会自动加载每次我们使用新的类型。我们所能做的就是实施一个最佳解决方案:

```
<?php

if (!class_exists(ShinyNewFoo::class, false)) {
    @trigger_error(
        'LegacyFoo is deprecated!',
        E_USER_DEPRECATED
    );
} 
```

首先检查`ShinyNewFoo`是否存在，*不触发自动加载*。如果没有，那么`LegacyFoo`在某处被引用，我们可以安全地触发弃用。

完成了吗？以下问题似乎已经在 php 的最新版本中得到解决，如果您的库要求 php >= 7.4.0，您可以忽略它。

否则…不，还没完，你这个可爱的夏天的孩子！它更深入。

这种情况更少发生，但是让我们考虑一个您公开的接口，该接口在其一个签名中使用了不推荐使用的类型，并被切换到新的类型:

```
<?php

interface Bar
{
    public function baz(ShinyNewFoo $foo);
} 
```

你的消费者的实现应该怎么样？

让我们也考虑做类似事情的类，但是你没有标记为 final…(或者那可能是`abstract`，相同的问题)。

```
<?php

abstract class Foobar
{
    abstract public function foobar(ShinyNewFoo $foo);
} 
```

扩展你的消费者类别会发生什么？

当然，他们会崩溃和燃烧！因为类型声明不触发自动加载，别名不存在，所以 PHP 不能知道两个类型声明的意思相同。

```
<?php

final class ExtendingFoobar extends Foobar implements Bar
{
    public function baz(LegacyFoo $foo) // 💥
    {
    }

    public function foobar(LegacyFoo $foo) // 💥
    {
    }
} 
```

除非…您猜到了，否则我们需要向`class_exists`(或`interface_exists`)调用添加另一个调用来触发自动加载。为了不被弃用，我们将在新类型上使用它，它将依次静默加载旧类型并使用类别名。

```
<?php

interface Bar
{
    public function baz(ShinyNewFoo $foo);
}
class_exists(\ShinyNewFoo::class); 
```

总而言之，每个可扩展接口，每个在签名中使用旧类型的接口都应该确保自动加载新类型。

完成了。下次见。哇，那太难了，我不能说那感觉很满足。我希望在 php 中有一种本地方法来完成所有这些。

如果你想自己解决这个问题，尝试一下，这里有一个回购协议，可能会成为你的一个好的起点:[https://github.com/greg0ire/type_deprecation_experiment](https://github.com/greg0ire/type_deprecation_experiment)