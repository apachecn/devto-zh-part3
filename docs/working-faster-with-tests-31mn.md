# 更快地处理测试

> 原文：<https://dev.to/xoubaman/working-faster-with-tests-31mn>

正如我们在本系列的[上一篇文章](https://dev.to/xoubaman/what-we-should-expect-from-tests-5d9n)中看到的，我们希望我们的测试能够为我们提供关于应用程序正确行为的快速反馈。

在这篇文章中，我们将触及测试过程中通常不被提及的一个方面:如何更快地进行测试。我们将讨论三件你可以做的事情来使你的测试流程更加有效。

### 快写出来！

[![The Pragmatic Programmer](img/91f4c6518f7318eb2c6ef9c9c6f13ac4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rMhefrGP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dlyhzlngbk3ovgm4sokz.jpg)

《务实的程序员》这本书虽然已经有 20 年的历史了，但仍然充满了好的建议，其中很多仍然适用于现代软件开发。

其中一个秘诀是:

> 好好使用单个编辑器

现代的 ide 有办法自动化几乎所有的事情，并且投资一些时间来了解你在大部分编码时间里使用的工具的具体细节绝对是值得的。说到测试，你越早写好测试，你就能越早从测试中得到反馈。你能做些什么来写得更快呢？

作为一个 IntelliJ 用户和 PHP 程序员，我使用 PHPStorm 作为我的 IDE。下面我描述了一些能显著提高编写测试速度的功能。无论您使用什么工具，都可能有类似的东西(除非您使用完整的 Windows 记事本，在这种情况下，您可以跳过这一节)。

IntelliJ 的 ide 有一个[模板系统](https://www.jetbrains.com/help/phpstorm/using-file-and-code-templates.html),可以为你自动生成几乎所有的代码。当谈到测试时，我们可以，例如，用一个设置方法创建它们，设置一个新的 SUT(被测系统)实例作为一个属性，并且可能扩展一个具体项目的基本测试类。只需分析您在创建测试时总是手动做的事情，并将其合并到测试模板中。

然后我们有[键盘快捷键](https://www.jetbrains.com/help/phpstorm/navigating-between-test-and-test-subject.html)，允许我们在一个类和它的测试之间导航。如果这个类没有测试，它将显示一个创建测试的选项(这将利用前面提到的模板)。

下面是一个模板系统和键盘快捷键的工作示例(嗯，静态操作)。首先是模板:

```
<?php
#if (${NAMESPACE})
namespace ${NAMESPACE};
#end

#if (${TESTED_NAME} && ${NAMESPACE} && !${TESTED_NAMESPACE})
use ${TESTED_NAME};
#elseif (${TESTED_NAME} && ${TESTED_NAMESPACE} && ${NAMESPACE} != ${TESTED_NAMESPACE})
use ${TESTED_NAMESPACE}\\${TESTED_NAME};
#end
use PHPUnit\Framework\TestCase;

class ${NAME} extends TestCase
{
    /** @var ${TESTED_NAME} **/
    private $sut;

    protected function setUp()
    {
        parent::setUp();
        $this->sut = new ${TESTED_NAME};
    }

    public function testSomething(): void
    {
        //TODO
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们在类的主体中按下跳转到 test 的快捷键时，IDE 会显示一个选项来选择一个可用的或者创建一个新的:

[![Using the 'jump to test' keyboard shortcut](img/ee592baa08c6524449dcab182b244126.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H27O5jsB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/39envc69cma1dmbpo0l5.png)

选择“创建新测试”将使用模板并自动显示以下内容:

```
<?php

namespace Example;

use PHPUnit\Framework\TestCase;

final class MyClassTest extends TestCase
{
    /** @var MyClass * */
    private $sut;

    protected function setUp()
    {
        parent::setUp();
        $this->sut = new MyClass;
    }

    public function testSomething(): void
    {
        //TODO
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，在测试类中，再次使用*跳转到测试*快捷方式，我们将回到测试类中。

养成使用这些快捷方式的习惯，并根据您的需要调整模板，您将在编写测试时获得可观的速度。

### 跑快点！

[![If they walk aligned they will look like a TestCase](img/584773b8aca8441b8e03ecbe68e9c02f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3Um3eGO0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2c96gc1qnvvb09z4prc7.jpg)

从你想到*我想现在运行测试*开始，你投入的时间越少，越好。如果我们以某种方式在一些机械动作中自动化运行测试的过程，我们将在我们的编码工作流程中无缝地完成它，而不会失去焦点。

如果您使用 IntelliJ IDEs，有必要了解一下[键盘快捷键](https://www.jetbrains.com/help/phpstorm/running-and-debugging-shortcuts.html),以便:

*   在当前作用域下运行测试——如果光标在测试方法内，则运行单个测试；如果光标在类中，但在方法外，则运行整个测试用例。

*   重新运行您的最后一次运行，这在进行 TDD 时尤为重要。

此外，如果你不熟悉它们，[运行配置](https://www.jetbrains.com/help/phpstorm/run-debug-configuration.html)也是一个重要的时间节省器。您可以仅为单元测试、仅为集成测试或者为具体的套件或文件夹定义它们，然后使用键盘快捷键来自动运行它们。

如果您不是 IntelliJ 用户，可以考虑花一些合理的时间编写脚本来减少运行测试套件的时间。

在 PHP 世界中，您可以选择配置 Composer(标准包管理工具)脚本来运行任意内容。例如，这是我的一个项目的 composer.json 文件的脚本部分:

```
 "scripts":  {  "test-unit":  "phpunit --configuration phpunit.xml.dist --testsuite unit",  "test-integration":  "phpunit --configuration phpunit.xml.dist --testsuite integration",  "test-functional":  "phpunit --configuration phpunit.xml.dist --testsuite functional",  "test-all":  "phpunit --configuration phpunit.xml.dist",  "all-checks":  [  "parallel-lint . --exclude vendor --exclude var",  "phpcs -p --standard=PSR2 --runtime-set ignore_errors_on_exit 1 --runtime-set ignore_warnings_on_exit 1 src tests",  "phpunit --configuration phpunit.xml.dist",  "vendor/bin/phpstan analyse"  ]  }, 
```

Enter fullscreen mode Exit fullscreen mode

因此，运行终端`composer test-unit`将运行我所有的单元测试。其他套房也一样。还有一个方便的`composer all-checks`，它将运行 linters、tests 和 PHPStan，所有这一切合二为一。

### 经常跑他们！

[![What can go wrong?](img/78576c34af634e8ad1924bf610b3f2c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_HeUzXZN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uwttz0kss909n0m4g3k3.jpg)

因此，现在您可以轻而易举地实现您的测试，并以闪电般的速度运行它们。不使用这种超级能力将是一种浪费，所以试着在你的编码流程中自然地引入运行测试的行为。这是获得这些如此令人满意的快速反馈回路所必须的。

如果你做 TDD(你应该做)，这是过程的核心部分。如果没有，那就训练自己。更重要的是，考虑到我们有忘记东西的倾向，在我们做的每一次提交中强制运行我们的测试可能是一个好主意。

在 IntelliJ 世界中，您可以配置脚本在提交之前运行。这意味着通过 IDE 的 GUI 使用 git。

另一个选择是配置您的项目 git 挂钩在提交之前运行测试，如果它们不是绿色的，就取消它。

或者甚至是别名 shell 命令，所以您不需要提交更多的命令，而是总是测试并提交。

像 [GrumpPHP](https://github.com/phpro/grumphp) 这样的工具，显然在 PHP 生态系统中，有助于这些自动化。

# 不要停止测试！

所有之前的建议都集中在测试上，但是可以肯定的是，你的蜘蛛能力已经被激活了，而且你注意到这些提示适用于整个编码过程。

我们程序员做的一件事就是自动化问题的解决方案。为我们每天重复做的任务投入时间这样做似乎是一个好的举措。

# 总结

我们已经看到了三种加快编写和使用测试的方法:

*   写得更快。
*   让他们跑得更快。
*   经常运行它们。

这三种方式可以通过我们日常使用的技术实现自动化。所以，试试吧！

# 进一步阅读

*   这篇[文章](https://dev.to/nickjj/optimize-your-programming-decisions-for-the-95-not-the-5-2n42)讲的是优化你大部分时间做的事情，而不是你不做的事情。
*   像往常一样，过度工程化并不是一个好主意。听 XKCD，想想[的努力和](https://xkcd.com/1205/)的价值。