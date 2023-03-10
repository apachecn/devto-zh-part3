# PHP 中的接口并不完全有意义

> 原文：<https://dev.to/maxalmonte14/interfaces-in-php-don-t-make-complete-sense-2n2o>

# PHP 中的接口没有完全的意义

接口是面向对象语言的主要特征之一，它们提供多态行为，并且是高度扩展的模式和技术的基础，如[库模式](https://deviq.com/repository-pattern/)和[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection)，但是它们在 PHP 中真的有意义吗？我们来深究一下。

### [T1】简介](#intro)

首先，我想说在面向对象编程语言中，接口是我最喜欢的特性之一，PHP 也不例外，**我喜欢接口**，我并不是想摒弃它的用法，而是恰恰相反！接口很棒，你应该使用它们，如果你没有使用过接口或者不知道它们是如何工作的，[这里有一篇由](https://daylerees.com/php-interfaces-explained/) [Dayle Rees](https://twitter.com/daylerees) 撰写的优秀文章。

### 面向对象，接口，PHP，一些历史

历史很重要！我们都知道一个叫克里斯托弗·哥伦布的人，他把文明带到了一个还不叫美洲的沙漠地带，了解历史是理解现在的重要组成部分。

早在 2004 年，PHP 5.0 就引入了接口，以及其他一些面向对象的特性，这些特性对这些事情并不重要。第一个实现是非常初级的，如果你访问 php.net 的接口页面，你会看到一个提示，在 PHP 5.3.9 之前，实现两个定义同名方法的接口会把你的代码炸成一百片。PHP 5.3.9 发布于 2012 年 1 月 10 日，在接口被引入近 8 年后！

在 PHP 5.x 的一个最体面的版本中，比如说 5.4 及以后的版本，你有一些体面的接口行为，让我们看一个真实世界的例子(我喜欢真实世界的例子！):

```
interface ParserInterface
{
    public function parse($data);
} 
```

太好了，现在我们有了一个接口，可以非常方便地解析数据。来实施吧！

```
class JSONParser implements ParserInterface
{
    public function parse($data)
    {
        $parsed = json_encode($data);

        if ($parsed == false) {
            throw new UnparseableException('Sorry we can\'t parse your data');
        }

        return $parsed;
    }
} 
```

假设我们已经定义了*unparsable exception*类，因为它并不重要，而且发挥想象力很有趣！

[![Sponge Bob SquarePants says Imagination](img/ad102be2325403d0347fc62d6cd0c6f0.png)](https://i.giphy.com/media/QIiqoufLNmWo8/giphy.gif)

### 接口的第一个问题:缺乏类型提示

我知道，我知道，这个问题在 PHP 7 中已经解决了，但是我们还在谈论历史，记得吗？

现在我们有了一个允许我们解析数据的接口，非常简洁！但是我们有一个问题。接口是一个契约，实现它的所有类都应该严格遵守，没有类型提示，我们不知道应该将什么样的数据传递给 *parse* 方法，尽管[过去提倡这种宽松的风格](https://devalmonte.com/blog/2018-10-10/why-we-need-mixed-types-in-php/)(现在仍然这样！)，这种行为大部分时候都是不想要的。

那么，我们如何解决这个问题呢？目前，一个简单的解决方法是添加一些保护条款，瞧！

```
class JSONParser implements ParserInterface
{
    public function parse($data)
    {
        if (!is_array($data)) {
            throw new UnparseableException('Unsupported type as argument. Please provide an array');
        }

        $parsed = json_encode($data);

        if ($parsed == false) {
            throw new UnparseableException('Sorry we can\'t parse your data');
        }

        return $parsed;
    }
} 
```

虽然不好看，但是很管用！遗憾的是，我们还有另一个问题

### 第二个问题:缺少返回类型

*   *“但是这个在 PHP 7.0 中已经解决了，在 PHP 7.2 中也改进了！”*

确实，但是我们仍然在谈论 PHP 5.x，耐心点，我们正在接近它。

这个问题和上一个问题有关。正如我们已经看到的，一个**接口是一个契约，必须被执行**，我们应该记住这个短语，因为它是！假的。所以，我们必须问自己，如果我们甚至不知道一个方法将返回什么，我们真的在执行一个契约吗？剧透警告:**否**。

由于 PHP 5.x 中缺少返回类型声明，我们甚至可以完全删除 return 语句。

```
class JSONParser implements ParserInterface
{
    public function parse($data)
    {
        ...
        // return $parsed; Let's remove this YOLO
    }
} 
```

那么，我们如何解决这个问题呢？没有办法确保实现接口的所有客户端都将返回正确的类型(甚至根本不返回)，我唯一能想到的是在编写代码之前测试我们的类的行为。TTD FTW！

### 用 PHP 7 解决这些问题

幸运的是，我们不必再活在过去，我们可以重写我们的 *ParserInterface* 接口和 *JSONParser* 类，使它们更加面向对象。让我们看看。

```
interface ParserInterface
{
    public function parse(array $data): string;
} 
```

```
class JSONParser implements ParserInterface
{
    public function parse(array $data): string
    {
        $parsed = json_encode($data);

        if ($parsed == false) {
            throw new UnparseableException('Sorry we can\'t parse your data');
        }

        return $parsed;
    }
} 
```

通过这些简单的修改，我们可以摆脱用于验证参数类型的保护子句，这是一个双赢的局面！

但我们仍然有一个问题，这段代码不是非常面向对象，传递一个*数组*给*解析*方法感觉有点乱，如果我们能传递一个类型的对象，比如说*格式*，那就更好了。让我们做得更好。

```
interface ParserInterface
{
    public function parse(Format $data): string;
} 
```

```
abstract class Format
{
    protected $value;

    public function __construct(array $value)
    {
        $this->value = $value;
    }

    public function getValue(): array
    {
        return $this->value;
    }
} 
```

```
final class JSON extends Format
{
    const FORMAT = 'JSON';
} 
```

理论上，我们应该只在 *JSONParser* 中实现 *ParserInterface* ，一切都会好的。不完全是。

```
class JSONParser implements ParserInterface
{
    public function parse(JSON $JSONData): string
    {
        $parsed = json_encode($JSONData->getValue());

        if ($parsed == false) {
            throw new UnparseableException('Sorry we can\'t parse your data');
        }

        return $parsed;
    }
} 
```

如果我们运行这个...

```
Fatal error: Declaration of JSONParser::parse(JSON $JSONData): string must be compatible with ParserInterface::parse(Format $data): string in... 
```

什么！？这怎么可能？为什么会失败？好了，是时候看看 PHP 中当前最重要的接口问题了。

### PHP 中接口的当前问题:缺乏协变性和逆变性

协变和逆变是描述非常简单的概念的花哨术语，如果你使用它们，你的老板会认为你自作聪明，也许会给你加薪，你不会因为尝试而损失什么。

**Contravariance(或简称 variance)** 是在不破坏实现的情况下用父对象替换对象的能力。

**协方差**是相反的，用一个对象的子对象替换它的能力，而不破坏实现。

*   这是什么意思，它与 PHP 中的接口有什么关系？

相当简单！PHP 不支持逆变和/或协变，在接口和类中都不支持。这就是为什么我们在尝试实现 *ParserInterface* 并将类型提示从*格式*(抽象类)更改为 *JSON* (具体类)时会出现致命错误的原因。

出于演示的目的，让我们看看如何使用更面向对象的语言实现相同的逻辑，在本例中是 C#。

```
public interface IParser <in T> where T : Format
{
    string Parse(T data);
} 
```

```
public abstract class Format
{
    protected Dictionary<string, string> value;

    public Format(Dictionary<string, string> value)
    {
        this.value = value;
    }

    public Dictionary<string, string> GetValue()
    {
        return value;
    }
} 
```

```
public class JSON : Format
{
    public JSON(Dictionary<string, string> value) : base(value)
    {
    }

    public string FORMAT = "JSON";
} 
```

```
public class JSONParser : IParser<JSON>
{
    public string Parse(JSON JSONData)
    {
        // Let's suppose we're implementing the logic needed in here...
    }
} 
```

在这里，我们用一个通用参数定义了一个接口，我们可以将任何*格式*的子类传递给它，瞧，一切都将按预期工作。

如果你不完全理解 C#实现中的语法，这没关系，毕竟，这篇文章是关于 PHP 的，只要记住这完全按照我们想要的那样工作，很好！

所以，真正的问题仍然存在:**我们如何在 PHP 中解决这个问题？嗯，我们不能...或者我们可以吗？**

### PHP 7.4 FTW！

我们已经谈论了 PHP 的过去和现在，现在让我们来谈谈未来！

一个新的 RFC 被批准在 PHP 7.4 中引入协方差和逆变支持(哈利路亚！)，而不需要泛型、输入/输出符号，以及我们在其他语言(如 C#)中需要使用的所有奇怪的东西。

你可以在这里看到细节[，如果你足够好奇，在这里](https://wiki.php.net/rfc/covariant-returns-and-contravariant-parameters)可以看到实现[，所以除非发生什么意外，否则我们会在下一个版本中享受这个特性。](https://github.com/php/php-src/pull/3732)

所以，假设我们有 PHP 7.4 运行时，我们最后的实现应该像预期的那样工作！

```
class JSONParser implements ParserInterface
{
    public function parse(JSON $JSONData): string
    {
        $parsed = json_encode($JSONData->getValue());

        if ($parsed == false) {
            throw new UnparseableException('Sorry we can\'t parse your data');
        }

        return $parsed;
    }
} 
```

```
## somewhere_in_your_application.php

$JSON = new JSON(['name' => 'Max', 'languages' => ['PHP', 'C#', 'JavaScript']]);
$parser = new JSONParser();

try {
    $JSONString = $parser->parse($JSON);
} catch (UnparseableException $exception) {
    // Let's suppose we are handling the exception properly
}

// $JSONString is equal to {"name":"Max","languages":["PHP","C#","JavaScript"]}! 
```

平稳！

### 后学分

我用 PHP 编程 5 年多了，(我知道，没那么长，但那是我的历程！)到目前为止，这是我最喜欢的语言新增功能，一旦你在其他已经实现了这个功能的语言中工作过，你就会爱上它。PHP 7.4 总体上会是一个很棒的发布，让我们拭目以待吧！

在结束之前，我必须说，[RFC](https://wiki.php.net/rfc/covariant-returns-and-contravariant-parameters)定义了比我们看到的更多的东西，所以如果你想详细了解 PHP 7.4 中协方差和逆变是如何工作的，你应该明确地查看一下[RFC](https://wiki.php.net/rfc/covariant-returns-and-contravariant-parameters)。

像往常一样，我想提醒读者，这里写的代码只是为了演示的目的，大多数时候我最终使用了非常糟糕的例子；你可以说- *你为什么不直接用 json_encode 来解析你那该死的数组！？嗯，你是对的，但你明白这篇文章的意思了，不是吗？*

我希望这篇文章对你有用。谢谢你呆了这么久！

最初发布于[devalmonte.com](https://devalmonte.com/blog/2019-04-18/interfaces-in-php-dont-make-complete-sense/)