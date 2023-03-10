# 为什么要封装

> 原文：<https://dev.to/rodrigosyscop/why-encapsulate-f1g>

我很确定你可以在互联网上找到足够多的关于面向对象编程范例的封装的材料。因此，我这里的方法是为那些还不习惯封装的人展示一个简单的 PHP 代码片段。

假设您有一个`wallet`对象，您想对它的公共接口施加一些限制。我的意思是，你想要指导客户代码应该如何使用你的`wallet`对象。

在其他属性和方法中，让我们尽可能地保持对象的简单，并专注于封装。为此，我们先从一个`$balance`和一个`$limit`属性开始，除了两个*公共*方法、`withdraw()`和`deposit()`之外，还有一个*私有*方法、`setLimit()`方法。

```
class Wallet
{
    /**
     * Balance of the wallet
     *
     * @var float
     */
    private $balance;

    /**
     * Availabe limit for the wallet
     *
     * @var float
     */
    private $limit;

    /**
     * Creates a Wallet with a initial balance and a certain limit.
     * @param  float  $balance
     * @param  float  $limit
     */
    public function __construct(float $balance, float $limit = 0)
    {
        $this->limit = $this->setLimit($limit);

        if ($balance + $this->limit < 0) {
            throw new Exception("Can't create a wallet with balance under limit.");
        }

        $this->balance = $balance;
    }

    private function setLimit(float $limit)
    {
        return $this->limit = abs($limit);
    }

    /**
     * Increase balance
     * @param  float  $amount
     * @return float
     */
    public function deposit(float $amount)
    {
        return $this->balance += $amount;
    }

    /**
     * Decrease balance
     * @param  float $amount
     * @return float
     */
    public function withdraw(float $amount)
    {
        $amount = abs($amount);

        if ($this->balance + $this->limit - $amount < 0) {
            throw new Exception("Insuficient funds.");
        }

        return $this->balance -= $amount;
    }

    /**
     * A string representation for the wallet's balance
     * @return string
     */
    public function __toString()
    {
        return sprintf("Balance: $ %.2f", $this->balance);
    }
} 
```

首先，我们不希望允许客户端代码创建没有余额或者余额低于一定限度的钱包。因此，我们已经在构造函数方法中定义了这个初始规则。

接下来，我们不能让客户端代码直接操纵一个钱包的余额。此更改必须通过评估器方法之一发生，“存款()”或“提取()”。出于这个原因，我们将`$balance`属性定义为私有。

我们还希望确保投资组合的余额不会低于构建器中设置的限额。我们可以在`withdraw()`方法中定义这个业务规则。

## 使用钱包类

定义了 wallet 类之后，我们可以轻松地创建一个新的 Wallet，指定它的初始余额和限额:

```
$my_wallet = new Wallet(200, 100); 
```

通过`deposit()`和`withdraw()`方法:
限制`$balance`属性的变化

```
$my_wallet->deposit(1450.25);
$my_wallet->withdraw(500.50); 
```

多亏了`__toString()`魔法方法:
，我们可以看到一个钱包对象的字符串表示

```
print($my_wallet); 
```

最后，我们正在验证在撤销或创建新的 Wallet 对象时是否违反了*限制*:

```
# Will throw an Exception: "Insuficient funds"
$my_wallet->withdraw(2000.00);

# Will throw an Exception: Can't create a wallet with balance under limit.
$other_wallet = new Wallet(-225.15, 150); 
```

*封装*的主要目标是保护整个对象数据不被其客户端访问。我们关于*限制*的业务规则对客户代码是隐藏的。我们用对数据进行操作的方法模糊了数据。