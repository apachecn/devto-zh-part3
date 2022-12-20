# 为 PHP 代码嗅探器编写自定义嗅探

> 原文：<https://dev.to/matthewbdaly/writing-a-custom-sniff-for-php-codesniffer-4b82>

我最近开始意识到 PHP 中的所有类在默认情况下都应该是 final 的，并且已经开始这样做了。然而，当你开始做这样的事情时，很容易错过一些没有更新的文件，或者忘记更新，所以我想要一种方法来检测没有被设置为抽象或最终的 PHP 类，如果可能的话，自动将它们设置为最终的。我之前提到过，我广泛使用 PHP 代码嗅探器，它有能力找到并解决与编码风格的偏差，所以昨晚我开始研究为此创建一个编码标准的可能性。理解如何做到这一点需要一点努力，所以我想我将使用这个嗅探作为一个简单的例子。

第一部分是列出目录结构。PHP 代码嗅探器有一个非常特殊的布局:

*   标准的文件夹必须具有标准的名称，并且位于 Composer 设置的源文件夹中(在本例中为`src/AbstractOrFinalClassesOnly`)。
*   该文件夹必须包含一个定义标准名称和描述的`ruleset.xml`文件，以及任何其他必需的内容。
*   任何定义的嗅探必须在一个`Sniffs`文件夹中。

在这种情况下，`ruleset.xml`文件相当简单，因为这是一个非常简单的标准:

```
<?xml version="1.0"?>
<ruleset name="AbstractOrFinalClassesOnly">
    <description>Checks all classes are marked as either abstract or final.</description>
</ruleset> 
```

Enter fullscreen mode Exit fullscreen mode

嗅探旨在执行以下操作:

*   检查所有类是否设置了`final`关键字或`abstract`关键字
*   当运行修复程序时，使所有没有关键字`abstract`的类成为最终类

首先，我们的类必须实现接口`PHP_CodeSniffer\Sniffs\Sniff`，这需要以下方法:

```
 public function register(): array;

    public function process(File $file, $position): void; 
```

Enter fullscreen mode Exit fullscreen mode

注意这里的`File`是`PHP_CodeSniffer\Files\File`的一个实例。第一种方法注册嗅探应该操作的代码。这里我们只对类感兴趣，所以我们返回一个包含`T_CLASS`的数组。这在 PHP 使用的解析器标记的[列表中定义，表示类和对象:](https://secure.php.net/manual/en/tokens.php) 

```
 public function register(): array
    {
        return [T_CLASS];
    } 
```

Enter fullscreen mode Exit fullscreen mode

对于`process()`方法，我们接收两个参数，文件本身和位置。我们需要保存我们检查的令牌的记录，所以我们在私有属性中这样做:

```
 private $tokens = [
        T_ABSTRACT,
        T_FINAL,
    ]; 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们需要找到错误:

```
 if (!$file->findPrevious($this->tokens, $position)) {
            $file->addFixableError(
                'All classes should be declared using either the "abstract" or "final" keyword',
                $position - 1,
                self::class
            );
        } 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`$file`在`class`之前获取令牌，并将`$tokens`属性作为可接受值的列表进行传递。如果前面的令牌既不是`abstract`也不是`final`，我们添加一个可修复的错误。第一个参数是字符串错误消息，第二个是位置，第三个是失败的嗅探的类。

这将抓住问题，但不会真正解决问题。为此，我们需要从 file 对象中获取 fixer，并调用它的`addContent()`方法来添加`final`关键字。我们修改`process()`来提取修复程序，将其作为属性添加，然后在遇到可修复的错误时调用`fix()`方法:

```
 public function process(File $file, $position): void
    {
        $this->fixer = $file->fixer;
        $this->position = $position;

        if (!$file->findPrevious($this->tokens, $position)) {
            $file->addFixableError(
                'All classes should be declared using either the "abstract" or "final" keyword',
                $position - 1,
                self::class
            );
            $this->fix();
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

然后我们定义`fix()`方法:

```
 private function fix(): void
    {
        $this->fixer->addContent($this->position - 1, 'final ');
    } 
```

Enter fullscreen mode Exit fullscreen mode

下面是完成的类:

```
<?php declare(strict_types=1);

namespace Matthewbdaly\AbstractOrFinalClassesOnly\Sniffs;

use PHP_CodeSniffer\Sniffs\Sniff;
use PHP_CodeSniffer\Files\File;

/**
 * Sniff for catching classes not marked as abstract or final
 */
final class AbstractOrFinalSniff implements Sniff
{
    private $tokens = [
        T_ABSTRACT,
        T_FINAL,
    ];

    private $fixer;

    private $position;

    public function register(): array
    {
        return [T_CLASS];
    }

    public function process(File $file, $position): void
    {
        $this->fixer = $file->fixer;
        $this->position = $position;

        if (!$file->findPrevious($this->tokens, $position)) {
            $file->addFixableError(
                'All classes should be declared using either the "abstract" or "final" keyword',
                $position - 1,
                self::class
            );
            $this->fix();
        }
    }

    private function fix(): void
    {
        $this->fixer->addContent($this->position - 1, 'final ');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我已经通过 Github 发布了结果标准[。](https://github.com/matthewbdaly/abstract-or-final-sniff)

这是一个有点粗糙和现成的，我可能会重构它时，我有时间。此外，它没有很好地显示我想要的行为，因为理想情况下，它应该只在实现接口的类中寻找`abstract`和`final`关键字。然而，事实证明，创建这种嗅探是相当容易的，除了我不得不去寻找各种不太清楚的教程。希望这个例子更简单，更容易理解。