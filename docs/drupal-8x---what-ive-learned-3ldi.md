# Drupal 8.x -我学到了什么

> 原文：<https://dev.to/michaeldscherr/drupal-8x---what-ive-learned-3ldi>

如果有人对我下面描述的任何问题有解决方案，请随时留下评论。

我现在已经用`Drupal 8.6`开发了两个生产站点。这篇文章将会介绍哪里出错了，我的团队是如何修复它们的，然后是一些我们从反复试验中学到的最佳实践。

事后看来，第一个网站完全是一场灾难。在开发一个产品网站的过程中，没有什么比通过反复试验来学习 Drupal(或其他任何东西)更糟糕的了。对`rendered entities`、`view modes`等一无所知。我不断与模板斗争。我们滥用了`[theme].module`钩子，而不是使用`custom modules`来处理功能。

第二个网站要好得多，尽管我认为还有很多东西需要学习。我们最大的问题是在使用`ajax`功能时如何有效地使用`views`。我们决定不确定`taxonomies`有实际的页面(也就是有可访问的`alias`)，这导致在试图过滤某些`views`的页面时出现了很多问题。

本地化仍然是一个大问题。如果您曾经使用 Drupal 进行过开发，您就会知道正确配置和输出本地化内容是一件极其令人沮丧的事情。

## 最佳实践(根据我的经验)

### 语言配置

**相信我说的话:**

提前算好自己的`localizations`。

开发前您应该知道:

*   您将使用的所有`languages` + `language codes`
*   所有内容的`original language`是什么

**场景**:

假设你被告知网站将使用加拿大英语(`en-CA`)和加拿大法语(`fr-CA`)。然后在部署前几周，一旦所有的内容 pop 大部分完成，他们希望将`language code`从`en-CA => en`更改为。

**问题来了**:

一旦添加了`language code`,似乎就没有办法更改了。此外，所有以前的`language code`作为`original language`的内容也不能更改。即使您删除了`orignal language`节点`translation`，您仍然不能分配另一个`translation`作为`original language`。

好吧，那么你添加了新的`language code`，内容用新的`language`再次弹出所有节点…然后遇到另一个问题。除了具有可重复数量的值的`original language`之外的`translation`中的所有内容都不能超过`original language's`条目的数量。

我们是如何解决这个问题的？我们没有。我们考虑过通过`SQL`语句更新数据库，但是如果你有更好的方法，请分享你的想法。

### 安装 Drupal 核心 w/ Composer

帮自己一个忙，使用 [Composer 安装 Drupal 核心](https://www.drupal.org/docs/develop/using-composer/using-composer-to-manage-drupal-site-dependencies)。升级`core`现在就像
一样简单

```
composer outdated drupal/*

composer update drupal/core --with-dependencies
drush updatedb
drush cr 
```

更多信息可在他们的网站上获得。

### 用 Composer 安装模块

与`core`处理相同，改为运行:

```
# example
# composer require drupal/[module_name]

composer require drupal/better_exposed_filters 
```

#### 奖励:牛逼模块

*   [段落](https://www.drupal.org/project/paragraphs)——就像[的 ACF for Wordpress](https://www.advancedcustomfields.com/)
*   [曝光更好的滤镜](https://www.drupal.org/project/better_exposed_filters)——在`views`的高级部分增加更多选项
*   [Twig XDebug](https://www.drupal.org/project/twig_xdebug) -在你的 Twig 文件中添加调试点(需要在服务器上安装并配置[XDebug](https://xdebug.org/)；可能会写另一篇关于这个的博文)
*   [路径自动](https://www.drupal.org/project/pathauto) -自动生成/更新`node aliases`
*   [视图参考字段](https://www.drupal.org/project/viewsreference) -与`paragraphs`模块一起使用；允许在页面的任何地方注入一个`view`
*   [Devel](https://www.drupal.org/project/devel) -用于调试
*   [Yoast SEO](https://www.drupal.org/project/yoast_seo) -用于 SEO；元标签的内容弹出
*   [Drupal Media](https://www.drupal.org/project/media)——在核心中从`8.4`开始，只需安装即可；为处理媒体增加了更好的 UX 体验

### 使用`Drupal Console` & `Drush`可执行文件

#### 安装说明(使用 Composer)

*   [Drupal 控制台文档](https://docs.drupalconsole.com/en/getting/composer.html)
*   [德鲁士文件](http://docs.drush.org/en/master/install/)

要通过`composer`安装，运行:

```
# drupal console
composer require drupal/console:~1.0 \
    --prefer-dist \
    --optimize-autoloader

# drush
composer require drush/drush 
```

常用用法:

```
# usage
./vendor/bin/drupal [command]
./vendor/bin/drush [command]

# cache rebuild
./vendor/bin/drush cr

# show watchdog log
./vendor/bin/drupal watchdog:show

# install module
./vendor/bin/drupal module:install [module_name]

# generate module
./vendor/bin/drupal generate:module 
```

### 为本地开发设置 Drupal

[遵循本教程](https://www.drupal.org/node/2598914)让您的本地开发变得更加容易。

### 使用视图模式

`View Modes`允许你定义多种方式(又名`templates`)来显示同一类型的内容。两种本地视图模式是`full`和`teaser`。仅凭这两个`view modes`，你就可以完成你所需要的`95%`。

想象一下这个场景:您有一个内容类型`Case Study`。案例研究可以显示为单个页面(`full`)、视图网格内(`teaser`)，也可以显示为另一个页面上的特色案例研究(`featured`)。

您可以单独定制每个`view mode`的`display`。例如，假设`teaser`输出了`title`和`description`，但是隐藏了您设置的`image`。但是`image` *应该在`featured`和`full` `view modes`上显示*。

这允许我们利用 3 个不同的模板并定制输出:

```
node--case-study.html.twig
node--case-study--teaser.html.twig
node--case-study--featured.html.twig 
```

### 使用渲染的实体

让我帮你省下几个小时的挫败感。尽可能使用`rendered entities`。按照我的理解，一个`rendered entity`是一个实体通过它选中的`view mode`的输出。

想象一下上面的案例研究`featured`的例子。您向`Basic Page`内容类型添加一个新字段，作为案例研究的参考。在`Basic Page`的`manage display`中，可以将病历参照的`format`改为`Rendered Entity`，并选择`featured`查看模式。

这大大减少了代码重复。您将实体的呈现与引用它们的节点分开。

**Pro 提示**:您也可以使用`rendered entity`和一个特定的`view mode`作为`view`的格式。此外，在不能使用`content`格式的情况下，您可以添加一个名为`Content: Rendered Entity`的字段，您可以这样引用它:

```
{{ fields.rendered_entity.content }} 
```

### 使用媒体模块

是的，它仍然是实验性的，但是我们还没有遇到任何问题。它使得上传、选择和删除媒体更加容易。

## 结论

Drupal 非常强大。它允许您添加复杂的关系和字段来构建任何类型的内容。然而，有一些更高级别的功能对于理解和实现是至关重要的。利用`view modes`、`rendered entities`和其他最佳实践将帮助您成为更好的 Drupal 开发人员。

这篇文章比我预想的要长。还有更多最佳实践和技巧没有出现在这篇文章中。欢迎任何有新见解或解决方案的评论。