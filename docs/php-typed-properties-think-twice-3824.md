# PHP 类型属性:三思而后行。

> 原文：<https://dev.to/mindplay/php-typed-properties-think-twice-3824>

#### PHP 的[类型化属性 RFC](https://wiki.php.net/rfc/typed-properties) 已经合并到 master 中，并将在 PHP 7.4 中可用。😍

但是，在您扑向这个特性并开始移植所有现有的模型类来使用这个特性之前，您一定要阅读这个。

今天你可能会有一些冗长而隆重的事情，比如:

```
class Product
{
    /**
     * @var int
     */
    private $price;

    /**
     * @var string
     */
    private $description;

    public function getPrice(): int
    {
        return $this->price;
    }

    public function setPrice(int $price)
    {
        $this->price = $price;
    }

    public function getDescription(): string
    {
        return $this->description;
    }

    public function setDescription(string $description)
    {
        $this->description = $description;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能希望把它移植到一些短小精悍的东西上，就像这样:

```
class Product
{
    public int $price;

    public string $description;
} 
```

Enter fullscreen mode Exit fullscreen mode

从表面上看，这些功能实际上是等同的。

但是等一下。

采用这个新特性有两个主要的权衡。

#### *如果要抽象一个接口背后呢？*

你可以

一旦你开始使用公共的、类型提示的属性，你就不能再引入抽象，所以你必须返回到 getters 和 setters。

换句话说，如果您选择公共的、类型提示的属性，您就选择了放弃任何当前或未来的抽象！

#### *如果要添加验证怎么办？*

比如说，描述的最高价格或最大字符串长度？

你可以

一旦选择了类型提示属性，除了类型提示属性支持的简单类型检查之外，就不能再添加任何新的约束。

还要注意的是，做出这两个非常典型的变化中的任何一个都将是向前发展的*突破*的变化，所以肯定要考虑这一点。

可耻。

我想，重构回类型提示的属性？

尽管如此，您仍然可以获得内部类型检查的好处，对吗？

```
interface ProductInterface
{
    public function getPrice(): int;
    public function getDescription(): string;
}

class Product implements ProductInterface
{
    private int $price;

    private string $description;

    public function getPrice(): int
    {
        return $this->price;
    }

    public function setPrice(int $price)
    {
        $this->price = $price;
    }

    public function getDescription(): string
    {
        return $this->description;
    }

    public function setDescription(string $description)
    {
        $this->description = $description;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯。

当然，但是你已经从 setter-methods 的类型提示中获得了类型安全。

现在你有*两个*类型检查。并不能使事情“两倍于类型安全”,不是吗？

在静态分析和 IDE 支持方面，没有真正的区别。与 php-doc 块相比，在这里使用类型提示属性真正获得的唯一好处是语法略短。(也许还有一层针对内部错误的额外保护。)

您是否计划添加一些带有文档块的内联文档？那么无论如何你都需要 doc-blocks——并且 doc-blocks 中的类型提示不是可选的，所以你将额外重复你的所有类型。

此时，您可以再次删除这些属性类型提示。你所拥有的已经很好了。

#### *现在...*

我发帖不仅仅是为了破坏你的兴奋和心情。

只是想让您意识到，如果为了简洁和方便而选择类型提示的属性，您会做出严重的权衡。

类型提示属性肯定会在类中方便的内部类型检查中发挥作用，而不仅仅是具有公共 getters 和 setters 的模型，所以这是值得庆幸的。只是这不会立即给你带来你在其他语言中可能体验过的那种方便和简洁。

这是前进道路上重要的一步！

但是还需要两个特性来实现您所希望的简洁和方便。

#### 界面中的属性

为了解决第一个问题，我们需要在接口中添加对类型提示属性的支持。

大概是:

```
interface ProductInterface
{
    public int $price;
    public string $description;
}

class Product implements ProductInterface
{
    public int $price;
    public string $description;
} 
```

Enter fullscreen mode Exit fullscreen mode

你喜欢类型提示属性的其他语言？他们有这个。

这听起来可能很简单，但它提出了许多有趣和困难的问题，我甚至不会在这里深入讨论。

当然，这只是解决了第一个问题——记住，除了类型提示属性支持的简单类型检查之外，您还牺牲了添加新约束的能力。

#### 访问者

为了解决第二个问题，我们需要在类和接口中添加对访问器的支持。

在这一点上，我们正在冒险进入幻想世界，但请耐心听我说。

给`$description`添加一个长度约束，我们可能会得到如下结果:

```
interface ProductInterface
{
    public int $price;

    public string $description;
}

class Product implements ProductInterface
{
    public int $price;

    private string $_description;

    public string $description {
        get {
            return $this->_description;
        }
        set {
            if (strlen($description) > 100) {
                throw new RangeException();
            }

            $this->_description = $description;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，其他语言的类型提示属性给了你如此多的感觉？是的，他们有这个功能。

无论如何，这个例子的重点是展示如何混合和匹配访问器和公共属性，以及(最重要的)能够在公共属性和访问器之间重构而不破坏接口的事实。

它实际上是一个以前已经提出的功能。在 [2009](https://wiki.php.net/rfc/propertygetsetsyntax) 没有进行投票，在 [2012](https://wiki.php.net/rfc/propertygetsetsyntax-v1.2) 被否决，在 [2013](https://wiki.php.net/rfc/propertygetsetsyntax-alternative-typehinting-syntax) 的一个变种也被否决。

所以这绝对不是“仅仅如此”——这是另一个需要非常仔细和深思熟虑的设计的特性，而且这可能是未来很长的一段路。

#### 结束。

我希望这篇文章能帮助你理解为什么这个新特性是朝着正确方向迈出的令人兴奋的一步，同时，你应该在充分意识到你所做的权衡的情况下使用它。

Cheers 😎✌