# 用 PhpStorm 在 Twig 中自动完成 Craft CMS 3 APIs

> 原文：<https://dev.to/gaijinity/auto-complete-craft-cms-3-apis-in-twig-with-phpstorm-5go9>

# 用 PhpStorm 自动完成 Twig 中的 Craft CMS 3 APIs

### Sym fony plu g in 与 lit tle mag ic 相结合，将为您提供所有 Craft CMS APIs 在您的 Twig tem plates 中的完整自动完成，并带有 PhpStorm！

安德鲁·韦尔奇

[![Phpstorm-auto-complete-craft-cms-apis-twig](img/37b756b4237d8d42c581d79d192fb591.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r1ywTNkY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_992x558_crop_center-center_82_line/2611/phpstorm-auto-complete-craft-cms-apis-twig.jpg)

Php Storm 是一个开发基于 web 的项目和后端项目的爱好者。

我已经在[中提到了我对 Php Storm 的使用，所以你想要制作一个 Craft 3 Plu g in？](https://dev.to/gaijinity/so-you-wanna-make-a-craft-3-plugin-76m-temp-slug-4321262) arti cle，当你从模板、ES6 JavaScript 甚至 Twig 上写东西时，它同样是很棒的。

你可以在 Php Storm 中做的一件绝对令人惊奇的事情就是在你的 Twig 模板中获得整个 API 的完全自动编译！要制作 mag ic hap pen，请在中安装[Sym fony plug g，然后转到**首选项** → **语言&框架工作**→**PHP**→**Sym fony**并检查**启用该项目的 plug in**。](https://plugins.jetbrains.com/plugin/7219-symfony-plugin)

<aside>If you think you’ve seen this before, you prob­a­bly haven’t. Car­ry on, intre­pid reader!</aside>

然后你需要做的就是把这个检查提示放在你的 Twig 模板的顶部:

```
 {# @var craft \craft\web\twig\variables\CraftVariable #} 
```

…就这样，你在你的小模板中得到和你在 PHP 代码中相同的自动编译。由于整个 Craft 应用程序现在都可以在我们的 Twig 模板中获得，这使得编写使用 Craft 应用程序 API 的 Twig 代码变得更好。

因此，虽然这很好，但是将该注释添加到每个单个模板可能会有点麻烦。如果有一种方法，我们可以在我们的项目中添加一个文件，然后它就可以正常工作，这不是很好吗？

<aside>Just by hav­ing Php­Storm index a file, we can get auto-com­plete of everything!</aside>

事实证明，我们可以！不久前，Robin 发布了一个问题，并以 Sym fony plu g in devel op ers 的一个回答结束，这个回答很棒。这是我修改过的文件版本:

```
 <?php
/**
 * FauxTwigExtension for Craft CMS 3.x
 *
 * This is intended to be used with the Symfony Plugin for PhpStorm:
 * https://plugins.jetbrains.com/plugin/7219-symfony-plugin
 *
 * It will provide full auto-complete for craft.app. and and many other useful things
 * in your Twig templates.
 *
 * Documented in the article:
 * https://nystudio107.com/blog/auto-complete-craft-cms-3-apis-in-twig-with-phpstorm
 *
 * Place the file somewhere in your project or include it via PhpStorm Settings -> Include Path.
 * You never call it, it's never included anywhere via PHP directly nor does it affect other
 * classes or Twig in any way. But PhpStorm will index it, and think all those variables
 * are in every single template and thus allows you to use Intellisense auto completion.
 *
 * Thanks to Robin Schambach; for context, see:
 * https://github.com/Haehnchen/idea-php-symfony2-plugin/issues/1103
 *
 * @link https://nystudio107.com
 * @copyright Copyright (c) 2019 nystudio107
 * @license MIT
 * @license https://opensource.org/licenses/MIT MIT Licensed
 */

namespace nystudio107\craft;

/**
 * Class FauxCraftVariable extends the actual Craft Variable, but with added properties
 * that reflect things that are added to the Craft Variable dynamically by
 * plugins or modules.
 *
 * @property \craft\web\twig\variables\Cp $cp
 * @property \craft\web\twig\variables\Io $io
 * @property \craft\web\twig\variables\Routes $routes
 * @property \craft\web\twig\variables\CategoryGroups $categoryGroups
 * @property \craft\web\twig\variables\Config $config
 * @property \craft\web\twig\variables\Deprecator $deprecator
 * @property \craft\web\twig\variables\ElementIndexes $elementIndexes
 * @property \craft\web\twig\variables\EntryRevisions $entryRevisions
 * @property \craft\web\twig\variables\Feeds $feeds
 * @property \craft\web\twig\variables\Fields $fields
 * @property \craft\web\twig\variables\Globals $globals
 * @property \craft\web\twig\variables\I18N $i18n
 * @property \craft\web\twig\variables\Request $request
 * @property \craft\web\twig\variables\Sections $sections
 * @property \craft\web\twig\variables\SystemSettings $systemSettings
 * @property \craft\web\twig\variables\UserSession $session
 * @property \putyourlightson\blitz\variables\BlitzVariable $blitz
 * @property \nystudio107\twigpack\variables\ManifestVariable $twigpack
 * @mixin \craft\commerce\web\twig\CraftVariableBehavior
 *
 * @author nystudio107
 * @package nystudio107\craft
 * @since 1.0.2
 */
class FauxCraftVariable extends \craft\web\twig\variables\CraftVariable
{
}

/**
 * Class FauxTwigExtension provides a faux Twig extension for PhpStorm to index
 * so that we get Intellisense auto-complete in our Twig templates.
 *
 * @author nystudio107
 * @package nystudio107\craft
 * @since 1.0.2
 */
class FauxTwigExtension extends \Twig\Extension\AbstractExtension implements \Twig\Extension\GlobalsInterface
{
    public function getGlobals(): array
    {
        return [
            // Craft Variable
            'craft' => new FauxCraftVariable(),
            // Craft Elements
            'asset' => new \craft\elements\Asset(),
            'category' => new \craft\elements\Category(),
            'entry' => new \craft\elements\Entry(),
            'tag' => new \craft\elements\Tag(),
            // Craft "Constants"
            'SORT_ASC' => 4,
            'SORT_DESC' => 3,
            'SORT_REGULAR' => 0,
            'SORT_NUMERIC' => 1,
            'SORT_STRING' => 2,
            'SORT_LOCALE_STRING' => 5,
            'SORT_NATURAL' => 6,
            'SORT_FLAG_CASE' => 8,
            'POS_HEAD' => 1,
            'POS_BEGIN' => 2,
            'POS_END' => 3,
            'POS_READY' => 4,
            'POS_LOAD' => 5,
            // Misc. Craft globals
            'currentUser' => new \craft\elements\User(),
            'currentSite' => new \craft\models\Site(),
            'now' => new \DateTime(),
            // Commerce Elements
            'lineItem' => new \craft\commerce\models\LineItem(),
            'order' => new \craft\commerce\elements\Order(),
            'product' => new \craft\commerce\elements\Product(),
            // Third party globals
            'seomatic' => new \nystudio107\seomatic\variables\SeomaticVariable(),
        ];
    }
} 
```

把文件放在你的项目中 Php Storm 会索引它的地方，然后你就可以走了！我把我放在`src/php/FauxTwigExtension.php`中，但是它可以是 Php Storm 索引的任何地方。

文件永远不会被任何东西包含，类永远不会被实例化，它不会以任何方式影响你的项目或分支。

Php Storm 索引只是一个虚假的扩展，因此 Intel lisense 将为我们自动完成。

**专业提示:**确保你没有被 Php 风暴忽略**偏好** → **直接指向**。这就是 Craft 为您的客户字段放置行为生成器的位置。

这是它给我们的启示。

**VARI 能干的飞船**

[![Phpstorm-auto-complete-craft-variable](img/a0b43d34dc3c25e25448f664ffae2382.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mGyVlLgW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x490_crop_center-center_100_line/phpstorm-auto-complete-craft-variable.png)

**条目**

[![Phpstorm-auto-complete-entry](img/7e5145600540a76b836b78e149ae7709.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xcNxTCcx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x490_crop_center-center_100_line/phpstorm-auto-complete-entry.png)

**猫 E GO RY**

[![Phpstorm-auto-complete-category](img/0ff14a8183db40abe8c826bce9e6a22e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1g8TV8z0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x490_crop_center-center_100_line/phpstorm-auto-complete-category.png)

**COM MERCE 产品**

[![Phpstorm-auto-complete-product](img/595009a29dc2f734d987e8eff0252017.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4CikLxYO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x490_crop_center-center_100_line/phpstorm-auto-complete-product.png)

**SEO MAT IC**

[![Phpstorm-auto-complete-seomatic](img/9d4ccda8f79ef36fe7f9ff0d99930dac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qX2zSde8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x468_crop_center-center_100_line/phpstorm-auto-complete-seomatic.png)

这是一个很好的自动完成的现成列表。但是真正酷的事情是你可以添加任何尤尔想要的到`FauxTwigExtension.php`文件中，让 Php 风暴索引它，然后你就可以走了！

很可爱。我要感谢罗宾督促我写这篇文章，还要感谢奥利弗·斯塔克的帮助。

希望有帮助！

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计