# ★自动格式化 PHP、JavaScript 和 CSS 文件的工具

> 原文：<https://dev.to/freekmurze/tools-to-automatically-format-php-javascript-and-css-files-25be>

当与其他人一起从事一个项目时，选择一个编码标准是很重要的。PHP 世界中像 [PSR-2](https://www.php-fig.org/psr/psr-2/) 这样的编码标准规定了某些字符应该放在哪里的规则，比如一个`if`语句的大括号，或者逗号。就编码标准达成一致使得代码对于所有需要接触项目的开发人员来说更具可读性。

违反这些格式规则很容易出错，修复所有小的格式错误也有点麻烦。幸运的是，有许多工具可以自动格式化这些代码。在这篇博文中，我将向您展示如何在 Laravel 项目中自动格式化 PHP、JavaScript 和 CSS 文件。不使用 Laravel？没问题！其中大部分可以应用于任何 PHP 项目。

## 自动格式化 PHP 文件

有很好的付费服务来执行编码标准，比如 [StyleCI](https://styleci.io/) ，但是你也可以在你自己的项目中这样做。一个很好的工具是 [PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer) 。让我们来看看如何使用它。

您可以在全局范围内安装该工具，但是我建议将它安装到您的项目中，这样从事该项目的任何人都可以使用它。

你可以用
安装 PHP-CS-Fixer

```
composer require --dev friendsofphp/php-cs-fixer 
```

接下来，您可以通过在项目的根目录下创建一个`.php_cs`配置文件来配置它。这是我在 Laravel 项目中使用的配置。

```
<?php

$finder = Symfony\Component\Finder\Finder::create()
    ->notPath('vendor')
    ->notPath('bootstrap')
    ->notPath('storage')
    ->in( __DIR__ )
    ->name('*.php')
    ->notName('*.blade.php');

return PhpCsFixer\Config::create()
    ->setRules([
        '@PSR2' => true,
        'array_syntax' => ['syntax' => 'short'],
        'ordered_imports' => ['sortAlgorithm' => 'alpha'],
        'no_unused_imports' => true,
    ])
    ->setFinder($finder); 
```

在这个配置的第一个语句中，我们使用`Finder`对象来选择一些文件。我们将排除`vendor`,因为否则当我们升级依赖项时，composer 会抱怨这些文件中的变化。在 Laravel 应用程序中，`bootstrap`目录包含一些自动生成的缓存。我们不打算修改这些。`storage`目录也将保持不变。在所有其他目录中，我们将选择以`.php`结尾的文件(除了视图模板的`.blade.php`文件)。

第二条语句包含我们要对所选文件做什么。我们的基础将是前面提到的 [PSR-2 编码标准](https://www.php-fig.org/psr/psr-2/)。我们还将添加一些额外的规则:

*   `array()`的任何用法都将被转换为`[]`
*   所有的进口将按字母顺序排列
*   所有未使用的导入都将被删除。

PHP-CS-Fixer 附带了许多附加规则。前往 GitHub 上的[包的用法部分，查看所有可用的规则。](https://github.com/FriendsOfPHP/PHP-CS-Fixer#usage)

当 PHP-CS-Fixer 运行时，它会创建一个临时的`.php_cs.cache`文件。您应该在您的`.gitignore`中添加一个条目来忽略这个文件。

你可以像这样运行 PHP-CS-Fixer:

```
vendor/bin/php-cs-fixer fix 
```

我从来不用上面的命令运行`php-cs-fixer`，而是使用一个名为。`format`。当然，你想怎么叫都行。使用 composer 脚本有一个额外的好处，那就是，如果您每次都要使用另一个工具来自动格式化您的代码，您可以更改 Composer 脚本，并且仍然运行嵌入在 muscle 内存中的相同命令。

将此添加到您的`composer.json`文件:

```
"scripts":  {  "format":  [  "vendor/bin/php-cs-fixer fix"  ],  } 
```

您现在可以使用:
运行修复程序

```
composer format 
```

现在，您所有的项目文件都根据`PSR-2`和您指定的附加规则进行了格式化。

## 自动格式化 JavaScript、Vue 和 CSS 文件

让我们也设置一个自动格式化 JavaScript 文件的工具。一个很好的选择是[更漂亮的](https://prettier.io/)。你可以通过运行
来安装`yarn`

```
yarn add prettier --dev --exact 
```

NPM 的用户可以运行这个:

```
npm install --save-dev --save-exact prettier 
```

可以通过在你的根目录下添加一个`.prettierrc`文件来配置更漂亮的。文档包含所有可用选项的列表。这是我在大多数项目中使用的配置。

```
{  "printWidth":  100,  "singleQuote":  true,  "tabWidth":  4,  "trailingComma":  "es5"  } 
```

在一个典型的项目中，你的资产的缩小版本将被存储在你的`public`目录中的某个地方。您可以通过在您的项目根目录中添加一个包含以下内容的`.prettierignore`文件来让 Prettier 知道它不应该扫描该目录:

```
public/
vendor/ 
```

在 Laravel 项目中，所有 CSS 和 JavaScript 通常都存储在`resources/`目录中。要自动格式化这些文件，您可以运行此命令。

```
prettier --write 'resources/**/*.{css,js,vue}' 
```

您可能不希望每次需要自动格式化文件时都键入该命令。让我们通过在`package.json`文件中添加一个脚本来使运行更漂亮更容易一点。

```
"scripts":  {  "format":  "prettier --write 'resources/ **/*.{css,js,vue}' 'nova-components/** /*.{css,js,vue}'",  }, 
```

有了这个，你可以跑得更漂亮了:

```
yarn format 
```

或者用:

```
npm run format 
```

## 在关闭

PHP-CS-Fixer 和[beauty](https://github.com/prettier/prettier)可以自动格式化你的代码。如果你想看一个使用这两种工具的 Laravel 项目的例子，去[GitHub](https://github.com/spatie/murze.be)上的 murze.be repo，它包含了运行这个博客的代码。

最近更漂亮的获得了格式化 PHP 代码的能力。我没有亲自试过，但是[克里斯](https://assertchris.io/)喜欢，一定不错！

> 我已经开始使用更漂亮的(和 php 解析器/插件)来格式化 PHP 代码；因为太一致了。我用 php-cs-fixer 已经很久了，但是因为有很多东西不能重新格式化，所以我改了。
> 
> —assert Chris(@ assert Chris)[2019 年 1 月 21 日](https://twitter.com/assertchris/status/1087298033858023426?ref_src=twsrc%5Etfw)

快乐格式化！