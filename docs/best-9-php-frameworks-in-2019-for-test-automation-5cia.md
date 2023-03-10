# 2019 年测试自动化最佳 9 个 PHP 框架

> 原文：<https://dev.to/lambdatest/best-9-php-frameworks-in-2019-for-test-automation-5cia>

PHP 是用于服务器端 web 开发的最流行的脚本语言之一。它被多个组织使用，尤其是像 WordPress 这样的内容管理网站。如果你正在考虑使用 PHP 开发一个 web 应用程序，你还需要一个 2019 年最好的 PHP 框架来测试你的应用程序。您可以手动执行视觉和可用性测试，但是对于功能、验收和单元测试、跨浏览器测试，自动化的 PHP 框架将有助于大大加快测试周期。在这篇文章中，我们将比较 2019 年测试自动化的最佳 9 个 PHP 框架，这些框架减轻了测试人员的工作，并确保更快地部署您的应用程序。

## 1。 [PHPUnit](https://phpunit.de/)

[![PHPunit](img/39b17aff50be496153c2b52b13dc3249.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vDxuhHuH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/04/PHPunit.png)

PHPUnit 被认为是 2019 年测试自动化方面最好的 PHP 框架之一，旨在对使用 PHP 语言开发的应用程序进行单元测试。就像 JUnit 一样，它是 xUnit 的一个实例，工作方式几乎类似。塞巴斯蒂安·博格曼创建这个框架的目的是帮助开发人员发现他们编码中的错误。

### 安装的先决条件

除了对 PHP 的正确理解和开发经验，你还需要

*   你系统中安装的 PHP 最新版本。
*   JSON 和 DOM 扩展，默认情况下是启用的。
*   您还需要 spl、反射和 pcre 扩展。有一些默认启用的扩展。
*   代码覆盖率需要 Xdebug 和 tokenizer。如果您想要生成 XML 报告，xmlwriter 是您需要的另一个扩展。

### 为什么要选择 PHPUnit 作为 PHP 框架？

就像其他用于单元测试的测试自动化框架一样，PHPUnit 帮助您开发性能良好且易于维护的代码。

*   它还有助于您在代码进入下一个测试阶段之前识别可能出现的缺陷。
*   由于测试仅由开发人员执行，因此在开发阶段早期就能发现问题。
*   单元测试有助于检测问题并修复应用程序某个片段的代码，从而使其他片段保持完整，没有任何损坏的可能。
*   调试过程变得更加简单。只有当某个单元测试失败时才需要调试。

### PHPUnit 的缺点

PHPUnit 唯一的缺点是，为了测试多个函数，开发人员需要添加封面注释。如果您更改了方法或函数的名称，而没有更新 **@covers 注释**，那么将跳过对该特定方法或函数的测试。

### PHPUnit 是 2019 年测试自动化最好的 PHP 框架吗？

在早期，测试自动化框架并没有被广泛使用，PHPUnit 作为该领域的唯一参与者，显然是 PHP 中自动化单元测试的最佳和唯一选择。但是在引入共同认知之后，观点发生了变化。Codeception 以相对更快的方式进行单元测试，并且使用更少的资源。

想为 PHPUnit 运行第一个自动化脚本吗？下面是在 PHPUnit 上使用 Selenium 运行[自动化脚本的分步指南。](https://www.lambdatest.com/support/docs/phpunit-with-selenium-running-phpunit-automation-scripts-on-lambdatest-selenium-grid/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting)

## 2。[共知](https://codeception.com/)

[![codeception](img/bac1ae1ff49a5dc0a459fffed9c69736.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z4E_Hv5n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/04/codeception.png)

Codeception 是一个自动化测试框架，除了单元测试之外，它还具有多种特性。该框架源自 PHPUnit，能够管理 web 应用程序的功能、单元以及验收测试。所有这 3 个都是 Codeception 的主要套件，对于其他测试，如 API 测试，您也可以添加 API 套件。

### 安装的先决条件

您将需要安装 PHPUnit 的所有要求。除此之外，您还需要在项目的根目录下安装一个 Composer。

### 为什么要选择 Codeception 作为 PHP 框架？

有许多原因会迫使你尝试共知。

*   首先，你会得到用 PHP 语言编写测试用例的最佳方法。语法突出显示还消除了语法错误的可能性。
*   编写测试用例非常简单。
*   您不仅可以进行单元测试，还可以通过添加套件进行验收或功能测试。
*   支持多种框架，无需任何扩展或更改代码。如果您的项目正在迁移，那么使用 Codeception 编写的旧测试用例将会以同样的方式工作。
*   Codeception 的 Db 模块帮助测试人员从数据库中获取您需要的任何东西，并从您的测试模块中清理数据。因此，隔离测试变得更加容易。
*   您可以通过使用 XPath 定位器或 CSS 选择器来运行复杂的测试用例。REST 和 SOAP web 服务也可以简单地使用 Codeception 进行测试。
*   这些测试简单易懂，非技术背景的人也能清楚地理解。

### 共同认知的弊端

仅有的几个缺点是

*   由于框架模拟浏览器，得到假阳性结果的机会增加了。
*   需要一个框架来开始测试。
*   无法测试 AJAX 和 JavaScript。

### code ception 是 2019 年测试自动化最好的 PHP 框架吗？

正如上一节所讨论的，在比较单元测试框架时，Codeception 比 PHPUnit 好得多，因为它提供了执行验收测试和功能测试的选项。然而，如果你只寻找验收测试，那么 Behat 是比 Codeception 更好的选择。

希望从代码接收开始吗？这里有一个指南可以帮助你在 Codeception 上运行你的第一个 [Selenium 脚本。](https://www.lambdatest.com/support/docs/codeception-integration-with-lambdatest/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting)

[![](img/a4763f8eaf9d6fd829acc4b65181fcfb.png)](https://accounts.lambdatest.com/register/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting)

## 3。[拉勒维尔黄昏](https://laravel.com/)

[![laravel dusk](img/73692ed1090c68d210c4db4ba79b5316.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V5lZwVQs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/04/laravel-dusk.png)

Laravel task 是一个基于浏览器的测试自动化工具，用于测试基于 PHP 的 web 应用程序。该工具还可以帮助您自动化重复性的任务。使用这个框架，你可以测试你开发的应用程序，也可以测试任何其他使用 Google Chrome 发布的网站。

### 安装的先决条件

与其他测试自动化框架相比，Laravel Dusk 的安装和使用非常简单。

*   首先，在你正在开发的项目中，添加一个 composer 依赖`laravel/dusk`
*   安装完黄昏包后，运行命令`dusk: install`。
*   默认情况下，你只能在 Chrome 上使用 Dusk。为了在其他浏览器上测试您的应用程序，您需要启动一个 Selenium 服务器。

### 为什么要选择 Laravel Dusk 作为 PHP 框架？

对于使用 PHP 开发的应用程序来说，使用 Laravel Dusk 进行测试是非常有利的。

*   您可以开发最佳的身份验证和授权脚本。
*   HTML 之类的表示视图和 SQL 查询之类的业务逻辑可以很容易地分开。
*   Laravel 的迁移特性允许您创建标准格式的数据库表。
*   对于开发者来说，Laravel 可以和 Angular JS 一起用于开发大型的单页 web 应用。
*   该工具还可以用来创建动态 HTML 元素，因为它也支持 vue js。
*   资产编译也可以使用 elixir 或 gulp 来完成，它们可以缩小 js 和 CSS 文件，从而提高应用程序的性能。

### 薄暮之弊

Laravel 唯一的缺点就是不能在 Chrome 之外的其他浏览器上使用。如今，当客户需要一个跨浏览器兼容的网站时，这当然是一个缺点。

### Laravel Dusk 是 2019 年测试自动化最好的 PHP 框架吗？

Laravel 最重要的优势是，除了测试，它还可以用来开发遵循 MVC 模式的 web 应用程序。但是，如果只从测试的角度来看，Selenium 显然是测试 web 应用程序的最佳选择，因为它能够支持多种语言以及平台和浏览器兼容性。

这里有一个完整的文档来帮助你用 Laravel 和 Selenium 执行[自动化测试。](https://www.lambdatest.com/support/docs/laravel-dusk-with-selenium-running-laravel-dusk-automation-scripts-on-lambdatest-selenium-grid/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting)

## 4。[行为](http://behat.org/en/latest/)

[![behat](img/63dd99b7d5053a05becf4c4e3378ce69.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YTtwArpb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/04/behat.png)

Behat 是一个行为驱动的测试工具，用于使用 PHP 开发的应用程序。该工具的目的是在软件开发生命周期中帮助利益相关者、管理层和开发人员之间的非语言交流。它有助于记录软件在执行测试时的行为示例，以及它如何匹配预期的行为。

### 安装的先决条件

有两种安装 Behat 的方法

*   最简单的方法是下载最新版本的 behat.phar。你只需要把它放在项目文件夹中。运行命令$ php behat。phar -V，它会告诉你 Behat 是否安装正确。
*   推荐的方式是使用 Composer，就像其他测试框架一样。

### 为什么要选择 Behat 作为 PHP 框架？

先把优点列出来。

*   测试用例以人类可读的方式编写，特性描述了各个站点的规格，测试套件提供了由 web 应用程序特性组成的非常清晰的文档。
*   帮助维护具有稳定部署周期的应用程序。你所需要做的就是运行测试，检测并修复错误，然后重复这个过程，直到项目通过所有的测试用例，从而得到一个稳定的应用程序。
*   验收标准易于理解，开发人员可以清楚地了解客户想要什么。没有任何阻碍，他们可以开始开发和添加新的功能。一旦功能成功地通过了测试用例，就可以开始部署了。
*   这有助于你与客户密切合作，直到你们双方就产品的工作方式达成一致。因此，迫使您遵循行为驱动开发的标准协议。

### 行为的弊端

尽管有这些优点，也有一些缺点。

*   Behat 的实现需要相当长的时间。
*   你还必须重新思考和重组整个开发过程。
*   与利益相关者和开发人员的讨论可能会持续很长时间，直到你们就如何开发一个特性达成共识。

### Behat 是 2019 年测试自动化最好的 PHP 框架吗？

虽然行为驱动的测试场景也可以使用 Codeception 生成，但是，它不能满足使用 BDD 环境开发的软件的所有需求。由于 Behat 完全致力于 BDD 测试，如果您正在开发一个基于 PHP 的应用程序，显然除了 PHPspec 之外，没有其他东西可以替代 Behat。

遵循我们的指南，开始使用 Behat 和 Selenium 进行[自动化测试。](https://www.lambdatest.com/support/docs/behat-with-selenium-running-behat-automation-scripts-on-lambdatest-selenium-grid/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting)

## 5。 [PHPSpec](http://www.phpspec.net/en/stable/)

[![PHPSpec](img/3367b08665282e653087584bb85674d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EBKt2o5B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/04/PHPSpec.png)

BDD 是一种开发技术，它允许项目在规范和故事级别开始。PHPSpec 是一个流行的 BDD 工具，它可以帮助你使用 PHP 编写干净的代码。开发工作首先描述对象的行为。一旦被涉众认可，下一个阶段就是开始编码。

### 安装的先决条件

PHPSpec 可以通过两种方法安装

*   作曲方法
*   使用 Composer 配置文件

在开始安装之前，请确保您的系统中至少安装了 PHP 5.6。

### 为什么要选择 PHPSpec 作为 PHP 框架？

在 PHPSpec 的众多优点中，我发现最有用的是

*   当开始执行测试用例时，PHPSpec 根据测试用例中方法的使用来生成类或方法的框架。您面临的任何额外的优势都可以通过编写一个额外的测试用例来覆盖。
*   通过重构，您可以重构整个代码，而不改变其外部行为。
*   您还可以使用 PHPSpec 来测试使用 Laravel 开发的应用程序。
*   PHPSpec 有一个被称为预言的嘲讽框架。预言确保对于每个测试，所需类的模拟实例被传递给构造函数。

### PHPSpec 的缺点

除了优点之外，也有一些缺点。

*   测试私有方法是一种限制。
*   假设你的公共方法返回一个值。它不是对象 API 的一部分。你不能测试那个。
*   不能使用 PHPSpec 执行集成测试。
*   再有用的预言，也不能嘲弄抽象的方法。

### PHPSpec 是 2019 年测试自动化最好的 PHP 框架吗？

PHPSpec 主要用于设计类。无需过多参与基础设施，您就可以设计一个隔离的领域模型。然而，如果您正在寻找一个工具来验证您的应用程序是否满足涉众的需求，Behat 是测试人员和开发人员的首选。Behat 的代码比 PHPSpec 更容易理解，特别是对于没有技术背景的人来说。

[![](img/e4b0b9b347849e2da140c9175e617e65.png)](https://accounts.lambdatest.com/register/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting)

## 6。 [Atoum](http://atoum.org/)

[![Atoum](img/7c7c3d9ee790dc700fe1677d2eadf501.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uQrPYO1W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/04/Atoum.png)

Atoum 是一个自动化框架，用于对使用 PHP 语言开发的应用程序进行单元测试。虽然它的行为有点像 PHPUnit，但它更现代、更简单，是一个独立的框架。与 PHPUnit 不同，使用 Atoum 编写的测试用例可读性强，而且大大简化了。此外，Atoum 的 asserters 使您能够编写更少的代码并测试更多的功能。

### 安装的先决条件

你可以从他们的官方[网站](http://downloads.atoum.org/nightly/atoum.phar)获得 Atoum 的最新版本。安装 Atoum 有几种方法。

*   按作曲家的用法
*   在 GitHub 克隆存储库
*   你可以从他们的官方网站下载 PHAR 档案
*   Atoum 也可以通过与其他框架集成来使用。

### 为什么要选择 Atoum 作为 PHP 框架？

Atoum 有许多其他基于 PHP 的单元测试框架所没有的优势。

*   它具有灵活的结构。它允许你重命名目录、测试用例或套件以及任何你喜欢的东西。
*   有 3 个引擎用于执行测试用例。对于一个单独的测试用例，您可以定义一个除了默认提供的引擎之外的特定引擎。
*   你可以用多种方式编写一个测试用例。要么使用经典方法，要么使用空断言的学术方法，要么使用 Atoum 的断言的更好更聪明的方法。
*   由于 Atoum 提供了一组自然的断言，所以测试用例是可读的，也更容易理解。
*   你不再需要操作真正的目录。在运行测试用例时，Atoum 允许您创建一个由虚假目录组成的虚拟文件系统，从而降低错误处理真实项目文件的风险。
*   类构造函数可以被嘲笑。无论何时调用一个方法，您都可以控制计算值。
*   最好的特性是，Atoum 可以轻松地与 Gitlab、Jenkins、PHPCI、Travis CI 等持续集成工具集成。它还可以与多种 ide 一起使用，如 Atom、Netbeans、Sublime Text 等。

### Atoum 的弊端

说到缺点，我没有发现任何无法解决或制造障碍的问题。

### Atoum 是 2019 年测试自动化最好的 PHP 框架吗？

这个问题的答案肯定是肯定的。它比 PHPUnit 或 Codeception 好得多，也更灵活。尽管它不能像 Codeception 一样执行功能测试或验收测试，但当它只执行单元测试用例时，灵活性、多平台支持和智能断言的使用使 Atoum 成为最好的单元测试框架。

## 7。 [Kahlan](https://github.com/kahlan/kahlan)

[![kahlan](img/a251e138d205ee1c2b0e402a9f009fea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_t5tHfQ_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/04/kahlan.png)

就像 Behat 或 PHPSpec 一样，Kahlan 是另一个行为驱动的测试框架。与众不同的是，通过使用`describe-it`语法，它将 PHP 上的 BDD 测试向前推进了一步。就像 Javascript 或 Ruby 一样，使用这个框架，不需要任何 PECL 扩展，就可以直接对代码进行猴子式修补或存根。

### 安装的先决条件

你需要在你的系统中安装一个稳定的最新版本的 PHP(至少 5.5 版本)。之后，卡兰可以通过两种方式安装

*   从 Git 存储库中。但是为此，您需要安装 Composer 和 Git。
*   作为一个 dev 依赖项，您也可以从 composer 包中安装 Kahlan。

### 为什么要选择 Kahlan 作为 PHP 框架？

在卡兰的众多优点中，我认为有用的是

*   包含 JSpec、RSpec 语法
*   更好的代码覆盖度量
*   您可以检查是否在您的类上调用了特定的方法。
*   存根系统更加灵活。不再需要预言或嘲弄。
*   您可以通过 monkey 补丁轻松替换核心类和函数。
*   Icov 和伊斯坦布尔是内置的报告，允许简单的 HTML 或终端报告。
*   工作服，粗布，气候是一些内置的出口商，让你很容易推动代码。

### 卡兰的缺点

唯一的缺点是，卡兰没有讲故事或开发基于 BDD 的场景的能力。

### Kahlan 是 2019 年测试自动化最好的 PHP 框架吗？

Kahlan 毫无疑问是一个非常有用的 BDD 工具，但是当我们把它和 Behat 比较时，Behat 更有用。一个不是来自技术背景的人，也许是一个利益相关者或者一个业务分析师，可以很容易理解用 Behat 编写的代码意味着什么。但是对于 Kahlan 来说，需要对 PHP 有基本的了解。如果项目是敏捷的，那么 Behat 也是一个更好的选择。

## 8。[橄榄石](http://peridot-php.github.io/)

[![Peridot](img/445593f0b5d93bc37044c035540d7f61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LkfGOu35--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/04/Peridot.png)

Peridot 是一个高度可靠、可扩展的 PHP 框架，支持在行为驱动环境下开发的应用程序的测试。该框架是轻量级的，比 PHPSpec 或其他框架更快。此外，它是事件驱动的这一事实使得它很受欢迎，因为当你使用 Peridot 时，开发插件要容易得多。

### 安装的先决条件

对于安装，您需要在您的系统中安装最新版本的 PHP。之后，橄榄石可以通过 3 个步骤安装

*   手动下载最新的 Phar
*   使用命令提示符手动操作。
*   像其他框架一样使用 composer。

### 为什么要选择橄榄石作为 PHP 框架？

橄榄石有几个优点。

*   就像 Kahlan 一样，它也使用 describe-it 语法来编写自然的、人类可读的测试用例。使用 Peridot 编写的测试用例很容易被非技术背景的人理解。
*   因为它是事件驱动的，所以提供了无痛苦的即席写作体验。
*   您还可以轻松添加自定义报告程序、助手和插件。
*   如果你的测试用例很庞大，Peridot 可以同时运行它们，因为它是轻量级的，比其他框架更快。

### 橄榄石的缺点

我在使用橄榄石时没有发现任何这样的缺点。唯一的缺点是它不适合在敏捷应用中使用，因为它的特性不像 Behat 那样丰富。

### Peridot 是 2019 年测试自动化最好的 PHP 框架吗？

在我们这里讨论的 3 个 BDD 框架中，Behat 显然是最佳选择。如前所述，Behat 的特性在部署和验收测试过程中为团队提供了帮助。尽管 Peridot 更快、更容易理解，但是在部署阶段缺乏对应用程序的支持是一个缺点。

## 9。[硒](https://www.seleniumhq.org/)

[![Selenium](img/ed0f765ac82a087fed32c6fada5ffedb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6xFYQaqR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/04/Selenium.png)

Selenium 是 web 应用程序自动跨浏览器测试的最佳自动化框架。对于使用 PHP 开发的应用程序，您可以将 PHP 绑定用于开发人员基于浏览器的自动化测试用例。该框架非常灵活，最新版本的 Selenium web driver 可以在所有主流浏览器上完美运行。

### 安装的先决条件

Selenium 没有任何依赖关系。然而，如果你想在 PHP 中使用它，你需要在你的系统中安装一个稳定的最新版本的 PHP。您还需要一个 composer 来提供所需的依赖项。

### 为什么要选择 Selenium 作为 PHP 框架？

使用 Selenium 测试 PHP 框架有无数的好处。

*   首先，这个框架是开源的。任何人都可以下载源代码，并根据项目需求进行修改。
*   它支持多种编程语言。即使有一天您的项目从 PHP 迁移到任何其他技术，您仍然可以使用 Selenium 来执行测试用例。
*   Selenium 可以在任何操作系统上使用，它支持所有主流浏览器，如 Chrome、IE、Opera、Safari 或 Firefox。
*   通过使用非常少的硬件资源，您可以使用 Selenium 并行执行多个测试用例。

### 硒的坏处

也有某些缺点。

*   这个框架有点困难，测试用例的创建需要一些时间。学习曲线也有点陡峭，因为框架每天都在发展。
*   由于该软件是开源的，如果你面临任何复杂情况，没有可靠的支持。
*   设置测试环境也很复杂，而且如果您希望报告错误，它不支持与测试管理工具的集成。
*   您只能在本地计算机上可用的浏览器上执行浏览器兼容性测试。

### 用 LambdaTest 硒栅克服硒的局限性

LambdaTest 上线了一个[云上的 Selenium 网格](https://www.lambdatest.com/blog/lambdatest-now-live-with-an-online-selenium-grid-for-automated-cross-browser-testing/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting),让测试人员社区的自动化测试变得更加容易。

*   使用 [LambdaTest Selenium Grid](https://www.lambdatest.com/selenium-automation?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting) ，您可以在 2000 多个真实浏览器和浏览器版本上执行基于 Selenium 的测试。
*   LambdaTest 提供了与众多第三方工具的[集成，以促进更好的错误记录、持续集成和持续部署管道。](https://www.lambdatest.com/integrations?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting)
*   LambdaTest 提供了他们的 [DesiredCapabilities 生成器](https://www.lambdatest.com/capabilities-generator/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting)，它将基于您通过点击操作的选择来帮助生成 **desiredcapabilities 类**。
*   LambdaTest 通过[聊天](https://dev.tojavascript:void(0))提供 24×7 的客户支持。你也可以给 support@lambdatest.com 的[发邮件来解决你的任何问题。](//mailto:support@lambdatest.com)
*   LambdaTest[还为 Selenium](https://www.lambdatest.com/blog/lambdatest-launches-api-for-selenium-automation/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting) 发布了 API，因此您可以从 LambdaTest 平台提取定制的、功能强大的 Selenium 脚本执行报告。
*   并行测试让您可以同时运行多个测试，从而进一步减少测试的执行。

### Selenium 是 2019 年测试自动化最好的 PHP 框架吗？

如果你只想在浏览器上测试一个基于 PHP 的 web 应用程序，Laravel Dusk 会有用得多。但是，如果您考虑的是更大的范围，并且想要一个可以对 web 应用程序执行跨浏览器测试的开源自动化框架，那么没有比 Selenium 更好的选择了。另外，使用 LambdaTest 在云上进行测试，可以尽可能地加快测试周期。

上面提到的框架不仅适用于测试人员，也适用于开发人员。因为，在当前的 DevOps 和敏捷时代，传统的规则不再流行，测试人员只做测试，开发人员只做开发。一个优秀的测试人员或开发人员也必须对其他领域有所了解。如果你是一名开发人员，行业不会期望你是测试专家，但是你应该有一些为你的代码编写单元测试用例的基本知识，或者理解测试人员在解释什么。如果你是一名 PHP 开发人员或测试人员，上面提到的 9 个工具将对你的进步有很大帮助。即使你是一名新生，学习上面列出的流行的单元测试或 BDD 工具也会推动你在这个行业的职业生涯。

[![](img/5ca15e434455da9426a2aad75efd7d47.png)](https://accounts.lambdatest.com/register/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting)

**相关帖子:**

1.  [如何创建跨浏览器兼容的 HTML 进度条？](https://www.lambdatest.com/blog/how-to-create-a-cross-browser-compatible-html-progress-bar/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting)
2.  [我在面试中被问得最多的 19 个 JavaScript 问题](https://www.lambdatest.com/blog/19-javascript-questions-i-have-been-asked-most-in-interviews/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting)
3.  [开发网站的 21 大 JavaScript 和 CSS 库](https://www.lambdatest.com/blog/top-21-javascript-and-css-libraries/?utm_source=dev&utm_medium=Blog&utm_campaign=ar-03-120419eu&utm_term=OrganicPosting)