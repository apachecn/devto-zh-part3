# 用 OOP 简化 WordPress 的 functions.php

> 原文：<https://dev.to/tylerlwsmith/simplifying-wordpresss-functionsphp-with-oop-2mj8>

我喜欢 WordPress，我在大多数客户的网站上使用它。在它的内置特性、丰富的插件生态系统和无尽的学习资源/文档之间，我可以花更多的时间为每个项目构建独特的特性，而不用花太多时间重复劳动。

也就是说，WordPress 并不完美。像 PHP 本身一样，WordPress API 经常让人感觉笨拙和不一致。我花了很多时间谷歌我经常使用的函数和动作钩子，因为我不记得它们的名字了。WordPress 的这一部分不太理想。

当我学习 Laravel 时，我发现当使用一个简单优雅的面向对象 API 时,*可以感觉到*有多好。那种感觉就像毒品:一旦我尝到了味道，我就会上瘾。我想在我的 WordPress 开发中加入一点可爱的面向对象编程(简称 OOP)。

输入`functions.php`。

我保持我自己的基于的[下划线开始主题，我在我的大多数项目中使用它，它的`functions.php`感觉很笨拙。我的函数文件相当典型，接近股票](https://underscores.me/)[强调 functions.php](https://github.com/Automattic/_s/blob/master/functions.php)。以下是它所做工作的概要:

1.  `after_setup_theme`挂钩内:
    *   **使用`add_theme_support()`为`title-tag`、`custom-logo`、`post-thumbnails`、`customize-selective-refresh-widgets`和一系列`html5`组件添加**主题支持。
    *   **使用`register_nav_menus()`注册**导航菜单。
    *   **使用`add_image_size()`添加**图像尺寸。
2.  `wp_enqueue_scripts`挂钩内:
    *   **用各自的`wp_enqueue_style()`和`wp_enqueue_script()`函数将**样式和脚本排队。
3.  包括相关文件。

还是那句话，这是一个相当典型的`functions.php`，并没有什么不妥。但是，我对它的设置有一些问题:

1.  记忆不是我最大的强项，记住哪些函数以单词**开头，add** ， **register** 和 **wp_enqueue** 对我来说是不可能的。
2.  动作钩子无声地失败，我也不知道有多少次我输入了`after_theme_setup`而不是`after_setup_theme`。
3.  我在每个项目中为完全相同的东西添加了主题支持，我真的不希望相同的模板弄乱我的代码。

让我们后退一步，考虑一下`functions.php`代码是什么*实际上*在这里做什么。

当你思考它的时候，所有发生的事情都是在主题本身上执行某种动作。如果我们有一个**主题对象**，我们可以用一个简单的、面向对象的 API 对其执行这些操作，会怎么样？

## 一个简单的，面向对象的 API

我不想记住哪些函数是以 **add** 、 **register** 或者 **wp_enqueue** 开头的。事实上，所有这些本质上都做了同样的事情:它们给主题添加了一些东西。所以我将用单词**加上**来表示所有这些。我想**添加**主题支持，**添加**导航菜单，**添加**图像大小，**添加**脚本。

我很懒。跟我打。

我希望我的`functions.php`看起来或多或少像这样:

```
<?php // functions.php

require get_template_directory() . '/classes/theme-class.php';

$theme = new MyTheme;

$theme->addNavMenus([
    'menu-1' => 'Primary',
]);

$theme->addSupport('post-thumbnails');

$theme->addImageSize('full-width', 1600);

$theme->addStyle('theme-styles',  get_stylesheet_uri())
      ->addScript('theme-script', get_template_directory_uri() . '/js/custom.js'); 
```

Enter fullscreen mode Exit fullscreen mode

不再需要记忆钩子名和函数前缀。欢呼吧！

同样非常清楚的是，所有这些方法都是在主题本身上执行一个动作。

让我们来建造这个。

我们将从在一个新文件中定义一个主题类并构建一个`addNavMenus()`方法开始。本质上，我们只是围绕现有的 WordPress 钩子和函数构建包装器，所以这不会太复杂。

```
<?php // theme-class.php

class MyTheme
{
    public function addNavMenus($locations = array())
    {
        add_action('after_setup_theme',function() use ($locations){
            register_nav_menus($locations);
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来解开这里发生的事情。

我们定义了我们的类`MyTheme`，为`addNavMenus()`创建了一个公共方法，并赋予它与`register_nav_menus()` WordPress 函数相同的参数。

在方法内部，我们向`after_setup_theme`钩子添加一个动作，并创建一个闭包(PHP 风格的匿名函数),在这里我们调用 WordPress `register_nav_menu()`函数。使用 PHP 的`use`关键字将`$locations`变量传递给闭包，否则该变量将超出闭包的范围。

侧注:PHP 5.3 支持闭包，我 90%的时间都是用闭包来和 WordPress 挂钩交互，以避免弄乱全局名称空间。然而，尽管 WordPress 采用了 React.js 等现代技术，但 WordPress 官方仍保持了对 PHP 5.2 的向后兼容性，该版本已于 2011 年 1 月寿终正寝🤷‍♂️

## 减少挂钩故障

在`addNavMenus()`方法中，我们已经解决了上面定义的第一个问题:我们已经简化了 API(不再需要记住像 **register** 这样的前缀)。我们仍然有第二个问题:拼写错误的钩子会无声地失败。当我构建我的主题类的方法时，在某个时候我可能会在某个地方写`after_theme_setup`而不是`after_setup_theme`而不会注意到。

让我们通过创建一个触发`after_setup_theme`动作钩子的私有方法来解决这个问题，然后在`addNavMenus()`方法而不是`add_action()`函数中调用这个方法。

```
<?php // theme-class.php

class MyTheme
{
    private function actionAfterSetup($function)
    {
        add_action('after_setup_theme', function() use ($function) {
            $function();
        });
    }

    public function addNavMenus($locations = array())
    {
        $this->actionAfterSetup(function() use ($locations){
            register_nav_menus($locations);
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以这有点酷:我们将`addNavMenus()`中的闭包传递给我们的`actionAfterSetup()`方法，然后通过`use`关键字将其传递给闭包，然后从闭包中的变量名调用代码。奇妙的巫术！

...如果这种描述没有任何意义，只要研究一下代码，就不会太糟糕。

我给这个方法加了前缀“action ”,告诉我这是一个动作钩子，我把它设为私有，因为它只应该在类中使用。

这解决了我们的第二个问题:如果我们错误地键入了`actionAfterSetup()`方法，它将不再无声无息地失败。现在我们只需要钩子的名字在一个地方是正确的。

再补充一些方法吧！

## 抽象项目间共享的代码

我为几乎每个项目的相同特性添加了主题支持:`title-tag`、`custom-logo`、`post-thumbnails`、`customize-selective-refresh-widgets`和一系列`html5`组件。

让我们为`add_theme_support()`函数添加一个包装器。我们将简单地称之为`addSupport()`,因为在主题类中包含“主题”这个词感觉是多余的。一旦实现，我们将看到如何抽象一些重复的代码。

驱动`add_theme_support()`函数的代码有点不可靠:它计算传递给它的参数数量来决定它应该做什么。因此，我们将在包装器中设置一个条件，以查看是否设置了第二个参数，如果第二个参数有值，则只传入第二个参数。

```
<?php // theme-class.php

class MyTheme
{
    /** Previous code truncated for clarity */

    public function addSupport($feature, $options = null)
    {
        $this->actionAfterSetup(function() use ($feature, $options) {
            if ($options){
                add_theme_support($feature, $options);
            } else {
                add_theme_support($feature);
            }
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，我希望能够将这些方法链接在一起，所以我将让`addSupport()`和所有其他公共方法返回`$this`。

```
<?php // theme-class.php

class MyTheme
{
    /** Previous code truncated for clarity */

    public function addSupport($feature, $options = null)
    {
        $this->actionAfterSetup(function() use ($feature, $options) {
            if ($options){
                add_theme_support($feature, $options);
            } else {
                add_theme_support($feature);
            }
        });
        return $this;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经实现了一种添加主题支持的方法，让我们通过为主题创建一组可用的默认设置来抽象出每个项目中需要的设置。我们可以通过使用类构造函数来做到这一点。

```
<?php // theme-class.php

class MyTheme
{
    /** Previous code truncated for clarity */

    public function __construct()
    {
        $this->addSupport('title-tag')
             ->addSupport('custom-logo')
             ->addSupport('post-thumbnails')
             ->addSupport('customize-selective-refresh-widgets')
             ->addSupport('html5', [
                 'search-form',
                 'comment-form',
                 'comment-list',
                 'gallery',
                 'caption'
             ]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为这些现在在类构造函数中，一旦一个主题对象被实例化，这些代码将立即执行，这意味着我们不再需要用 boiler plate 来混淆函数文件，boiler plate 用在我们用这个主题构建的每个站点中。

相反，函数文件只定义了主题中不同于每个项目的部分。

## 更进一步

这只是一个开始，但还有很多工作要做！您可以创建包装器来添加脚本和样式表。你可以通过主题类构造器自动添加`style.css`和`main.js`到你的主题中，进一步简化`functions.php`。您可以创建方法来移除主题支持和移除样式/脚本。这种方法非常灵活，最终会减少代码。

如果你想知道我的主题课是怎么来的，看看我的 GitHub 要点吧。

我希望这是有帮助的，并向您展示了 WordPress 的一些漂亮的面向对象选项。如果你有兴趣学习更多关于 WordPress 的面向对象编程，我强烈推荐你去看看 Kevin Fodness 的[面向对象主题开发 WordPress 讲座](https://wordpress.tv/2015/01/14/kevin-fodness-object-oriented-theme-development/)。对于那些已经在 WordPress 中使用 OOP 的人，我想听听你们对 camelCase 和 snake_case 在方法名、PHP 名称空间等方面的看法。干杯！