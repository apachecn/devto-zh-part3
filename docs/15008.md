# 用 OOP 简化 WordPress 的 functions.php:第 2 部分

> 原文：<https://dev.to/tylerlwsmith/simplifying-wordpresss-functionsphp-with-oop-part-2-5933>

经过一年对面向对象编程实践的反思，我不再支持我在这篇文章中采用的方法。下面代码中使用的继承增加了不必要的混淆，从长远来看会导致代码更难维护。出于历史原因，我将不再发表这篇文章。

**如果你没有看过原帖([用 OOP 简化 WordPress 的 functions.php](https://dev.to/tylerlwsmith/simplifying-wordpresss-functionsphp-with-oop-2mj8))，我仍然高度赞同那些做法，并经常使用它们。**

* * *

另一个月，我写了一篇题为[用 OOP 简化 WordPress 的 functions.php](https://dev.to/tylerlwsmith/simplifying-wordpresss-functionsphp-with-oop-2mj8)的文章，希望简化 WordPress 为主题增加功能的臃肿的总括。我对这个话题感兴趣是在我为一个 WordPress 站点添加功能的时候，当时我做开发还不到一年。阿姆可能会把网站的功能文件比作“妈妈的意大利面条”

*如果你还没有读过上一篇文章，我将推荐你[在这里阅读](https://dev.to/tylerlwsmith/simplifying-wordpresss-functionsphp-with-oop-2mj8)，因为这篇文章建立在它的代码和概念之上。*

我简化函数文件有几个目标:

1.  通过创建一个更简单、更简洁的与主题交互的 API 来清理站点的函数文件。
2.  防止最常见的挂钩失效。
3.  把锅炉板 API 抽象到某个我永远不用再看的地方。

当我考虑在一个基本的主题设置中解决这些问题时，我认为将主题视为一个对象并构建一个简单的方法 API 来对该对象执行操作是有意义的。在上一篇文章中，我们实现了这一点，使我们的函数文件看起来像这样:

```
<?php // functions.php

require get_template_directory() . '/classes/theme-class.php';

$theme = new MyTheme;

$theme->addNavMenus([
    'menu-1' => 'Primary',
]);

$theme->addImageSize('full-width', 1600);

$theme->addStyle('theme-styles',  get_stylesheet_uri())
      ->addScript('theme-script', get_template_directory_uri() . '/js/custom.js'); 
```

Enter fullscreen mode Exit fullscreen mode

我很高兴我在这里着陆，但是我的大部分主题都有很多动作和过滤挂钩。

当我开始构建 WordPress 主题时，我在函数文件的底部有很多钩子，像这样占据空间:

```
 <?php // functions.php

/** Below theme setup */

function mytheme_excerpt_length ( $length ){
    return 20
}
add_filter( 'excerpt_length', 'mytheme_excerpt_length' );

function mytheme_excerpt_more ( $more ) {
    return '&hellip;';
}
add_filter('excerpt_more', 'mytheme_excerpt_more' );

function mytheme_add_excerpt_class ( $excerpt ){
    return '<div class="mytheme-excerpt">' . $excerpt . '</div>';
}
add_filter('the_excerpt', 'mytheme_add_excerpt_class' );

/** More filters and actions forever */ 
```

Enter fullscreen mode Exit fullscreen mode

像这样的代码可以轻松地运行一百多行。

我不喜欢这样的代码有两个原因:

1.  它弄乱了函数文件。
2.  你需要给每个函数加上独特的前缀，否则会有名称冲突的风险。

所以这样不好。

为了改善这一点，我开始将我的钩子放入另一个文件中，我使用 PHP 的`include`命令将它包含在`functions.php`中，并且我将把我所有的钩子放在闭包(PHP 的匿名函数版本)中以避免强制前缀。

包含的文件通常看起来像这样:

```
<?php // inc/hooks.php

add_filter( 'excerpt_length', function ( $length ) {
    return 20;
});

add_filter('excerpt_more', function ( $more ) {
    return '&hellip;';
});

add_filter('the_excerpt', function ( $excerpt ){
    return '<div class="wp-excerpt">' . $excerpt . '</div>';
}); 
```

Enter fullscreen mode Exit fullscreen mode

我认为这是一个进步，但也不是没有问题。

这些是简单的自我描述挂钩，但是如果我有一个在我的内容底部添加社交分享按钮的挂钩会怎么样呢？如果没有一个描述性的名字，我就需要钻研代码来理解它在做什么。我可以注释代码，但是最好使用不完全依赖注释的模式。

此外，没有办法解开一个封闭。虽然这对于一个自定义主题来说比一个插件要少，但是这是值得记住的事情。

关于上一篇文章的补充说明:从技术上讲，在我们的基本主题类中，也没有办法解开闭包。然而，如果你看一下我的完整类的[要点](https://gist.github.com/tylerlwsmith/dc1f5fb04126189ec42980952cd124d7)，我们有一个 API 来移除我们通过‘remove’方法添加的任何东西。

那么我们该怎么办呢？

## 面向对象编程中的思维

仔细想想，这些滤镜是在主题本身上执行动作。我们已经创建了一个带有 API 的主题类，用于我们需要在几乎每个 WordPress 主题上设置的东西。然而，除了那个类中已经有的方法，我倾向于在我构建的每个站点上需要非常不同的钩子。

那么，*为什么不扩展*主题类呢？为胜利欢呼！

```
<?php // classes/my-theme-class.php
require get_template_directory() . '/classes/theme-class.php';

MyThemeClass extends MyTheme
{

    protected $excerptLength = 20;

    public function __construct()
    {
        add_filter( 'body_class', [ $this, 'filterExcerptLength' ] );
        add_filter( 'the_content', [ $this, 'filterExcerptMore' ] );
        add_filter( 'nav_menu_css_class' , [ $this, 'filterAddExcerptClass' ]);

        parent::__construct();
    }

    public function filterExcerptLength($length){
        return $this->excerptLength;
    }

    public function filterExcerptMore($more) {
        return '&hellip;';
    }

    public function filterAddExcerptClass($excerpt){
        return '<div class="mytheme-excerpt">' . $excerpt . '</div>';
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我已经创建了对应于每个钩子的方法，并在每个钩子前面加上前缀“filter”(我不喜欢在主题名称前面加上前缀(`mytheme_excerpt_length`)，因为它们感觉很粗糙，但是“filter”或“action”前缀描述了方法正在做什么)。我在类构造函数中为每个方法添加了过滤器，这意味着一旦类被初始化，它们就会被钩住。类上的 Filters 方法作为数组添加，类作为第一个数组项，方法作为第二个数组项。最后，调用父类构造函数。

这让我感觉好多了，原因如下:

1.  一旦主题对象被初始化，我们就直接对它执行操作。因为过滤器修改了主题，这感觉是合乎逻辑的。
2.  我们有每个方法的描述性名称。
3.  因为它们在一个主题类中，所以每个方法都是封装的，不需要主题名称前缀。

在这个实现中仍然有一些我不喜欢的东西。钩子仍然可以无声地失败。然而在 WordPress 核心和插件中有如此多的钩子，这可能是不可避免的。

我认为这是一个更优雅的实现。全局名称空间使用带前缀的函数，一切都在主题类中，感觉就像是它的正确位置。

有了这个，你的函数文件可以是这样的:

```
<?php // functions.php

require get_template_directory() . '/classes/my-theme-class.php';

$theme = new MyThemeClass;

$theme->addNavMenus([
    'menu-1' => 'Primary',
]);

$theme->addImageSize('full-width', 1600);

$theme->addStyle('theme-styles',  get_stylesheet_uri())
      ->addScript('theme-script', get_template_directory_uri() . '/js/custom.js'); 
```

Enter fullscreen mode Exit fullscreen mode

所有来自 MyThemeClass 的钩子都是在对象初始化时添加的，并且您仍然可以使用父类 API 来添加菜单、样式表等等。

我们可以更进一步。由于这些是 MyThemeClass 的一部分，我们可以将所有这些添加到 MyThemeClass 的构造函数:

```
<?php // classes/my-theme-class.php
require get_template_directory() . '/classes/theme-class.php';

MyThemeClass extends MyTheme
{

    protected $excerptLength = 20;

    public function __construct()
    {
        $this->addNavMenus([
            'menu-1' => 'Primary',
        ]);

        $this->addImageSize('full-width', 1600);

        $this->addStyle('theme-styles',  get_stylesheet_uri())
              ->addScript('theme-script', get_template_directory_uri() . '/js/custom.js');

        add_filter( 'body_class', [ $this, 'filterExcerptLength' ] );
        add_filter( 'the_content', [ $this, 'filterExcerptMore' ] );
        add_filter( 'nav_menu_css_class' , [ $this, 'filterAddExcerptClass' ]);

        parent::__construct();
    }

    public function filterExcerptLength($length){
        return $this->excerptLength;
    }

    public function filterExcerptMore($more) {
        return '&hellip;';
    }

    public function filterAddExcerptClass($excerpt){
        return '<div class="mytheme-excerpt">' . $excerpt . '</div>';
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个实现中，构造函数提供了影响主题的所有动作和过滤器的鸟瞰图。

将所有这些都转移到子主题类构造函数中，您的`functions.php`可以看起来像这样:

```
<?php // functions.php
require get_template_directory() . '/classes/my-theme-class.php';
$theme = new MyThemeClass; 
```

Enter fullscreen mode Exit fullscreen mode

我已经开始重构我的一个项目中的代码，以实现这种功能，我正在将这些代码移植到我个人的 starter 主题中，我希望将来我能够更快、更干净地编写项目代码。

如果你有兴趣学习更多关于 WordPress 的面向对象编程，我强烈推荐你去看看 Kevin Fodness 的[面向对象主题开发 WordPress 讲座](https://wordpress.tv/2015/01/14/kevin-fodness-object-oriented-theme-development/)。当开始转向面向对象的 WordPress 时，我抓住了他的很多想法。对于那些已经在 WordPress 中使用 OOP 的人，我想听听你们对 camelCase 和 snake_case 在方法名、PHP 名称空间等方面的看法。干杯！