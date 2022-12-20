# Boss 开发人员的实用编码模式#1:特例

> 原文：<https://dev.to/jamesmh/practical-coding-patterns-for-boss-developers-1-special-case-517n>

*[最初发表于我的博客](https://www.blog.jamesmichaelhickey.com/Practical-Coding-Patterns-For-Boss-Developers-Special-Case)*

设计模式对于你开始获得设计和重构软件的高级理解和能力是必要的(在我看来)。

这些模式还为开发人员提供了一种通用语言来谈论某些代码结构。

例如，如果你正面临某个问题，我说“试试策略模式……”那我就不用花一个小时解释你该怎么做了。

你可以去查一下，或者，如果你已经知道了模式，就去实现它！

# 不是你典型的设计模式

每个人都在谈论著名的四人帮书[设计模式](https://amzn.to/2WP2P8w)中的设计模式:

*   战略
*   建设者
*   工厂
*   适配器
*   等等。

但是，还有更多本书中没有的软件设计模式，我觉得非常有帮助。

其中一些是我从马丁·福勒那里学到的，另一些是从 T2 领域驱动设计和其他来源学到的。

我想我应该开始通过分享它们来对其中的一些进行分类！

作为一般规则，我将为我的代码示例使用 TypeScript。

# 近亲...

我想从这个模式开始，它与我之前在 tweeted 上提到的 null object 模式非常相似:

液体错误:内部

空对象模式是避免围绕`null`状态的问题的一种方式(就像你需要做的所有额外的`null`检查一样😒).

您创建了一个类的专用版本来表示它处于“空”状态，它公开了与基对象相同的 API 或接口。

换句话说，它有点像树桩。

这里有一个我马上想到的例子，打字稿:

```
// Concrete Order
class Order {
    private _items: any[];

    constructor(items: any[]) {
        this._items = items;
    }

    public placeOrder() {
        // API call or whatever...
    }
}

// Null "version" of the Order
class NullOrder extends Order {
    constructor() {
        super([]);
    }

    public placeOrder() {
        // We just do nothing!
        // No errors are thrown!
    }
}

// Usage:
const orders: Order[] = [
    new Order(['fancy pants', 't-shirt']), new NullOrder()
];

for (const order of orders) {
    // This won't throw on nulls since we've
    // used the null object pattern.
    order.placeOrder();
} 
```

Enter fullscreen mode Exit fullscreen mode

# 一个例子场景

假设我们有一个预定的后台进程，它获取多个订单并尝试下订单。

像亚马逊一样，我们可能有一个复杂的下单流程，它并不像我们想象的那样线性。

我们可能希望在您点击“下单”和订单真正被**下单**之间有一个缓冲期。这将使取消订单成为一个简单的过程(它避免了必须删除信用卡费用等)。)

注意:我以后可能会写这个模式。😉

在这种情况下，我们可能会尝试处理已经更改的订单:

*   取消订单
*   付款被拒绝
*   支付网关没有响应，所以我们需要等待...
*   等等。

空对象模式可以帮助解决这种情况。

但是更好的是，当你有这种“特殊”情况的多个版本时，特殊情况模式就在这里拯救你！

# 特例模式

特例模式在实现中本质上是相同的，但是我们可以使用相同的技术来建模任何特殊的或者非典型的情况，而不是建模特定的“空”状态。

使用上面的代码示例，通过使用特例模式，我们可以实现我们的`Order`类:
的更多语义和目标变体，而不是我们的`Order`的“空”版本

```
class IncompleteOrder extends Order {
    constructor() {
        super([]);
    }

    public placeOrder() {
        // Do nothing...
    }
}

class CorruptedOrder extends Order {
    constructor() {
        super([]);
    }

    public placeOrder() {
        // Try to fix the corruption?
    }
}

class OrderOnFraudulentAccount extends Order {
    constructor() {
        super([]);
    }

    public placeOrder() {
        // Notify the fraud dept.?
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这种模式有助于我们非常具体地在代码中建模特殊情况。

# 好处

一些好处是:

*   避免`null`异常问题
*   让班级承担更有针对性的单一责任
*   能够处理特殊情况，而不会让我们的`Order`类因为大量的逻辑而崩溃
*   类名的语义使得我们的代码更容易理解
*   引入新的案例包括创建新的类别和改变现有的类别([参见开闭原则](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle)

# 重构到特例模式

那么，什么时候应该使用该模式呢？

### 构造函数

每当你在类的构造函数中看到这种类型的逻辑时，你可能会考虑这种模式:

```
constructor() {
    if(this.fraudWasDetected()) {
        this._fraudDetected = true;
    } else {
        this.fraudWasDetected = false;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*注意:对此的重构将从下面的[哎呀，责任太多了](#oops-too-many-responsibilities)部分开始。*

### 外“问”

当您看到如下内容时，您可能需要考虑特殊情况模式:

```
const order = getOrderFromDB();

if(order.fraudWasDetected()) {
    order.doFraudDetectedStuff();
} else if(!order.hasItems()) {
    order.placeOrder();
} 
// ... and more ... 
```

Enter fullscreen mode Exit fullscreen mode

关注这个例子，为什么这是一个潜在的“代码味道”？

从表面上看，这种类型的逻辑应该“融入”订单类。

不管是谁在使用这个命令，都不应该知道这个逻辑。这都是订单特定的逻辑。不应该是“询问”`Order`细节，然后决定如何使用`Order`。

更多信息，请参见[告诉不要问原则](https://www.martinfowler.com/bliki/TellDontAsk.html)——大多数时候，它确实表明你的逻辑可能更适合你正在使用的对象。

第一个修正是将这个逻辑移动到`Order`类
的内部的

```
class Order {

    public placeOrder() {
        if(this._fraudWasDetected()) {
            this._doFraudDetectedStuff();
        } else if(!this._hasItems()) {
            this._placeOrder();
        } 
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 哎呀，责任太多了！

但是，现在我们遇到了一些问题:我们正在处理不同的职责(下订单、欺诈检测、物品损坏等。)在一个班！😓

*注意:下面的内容也适用于构造函数重构。*

**特殊情况下的救援模式！**

```
// Note: I'm just highlighting the main parts, this won't compile 😋
class CorruptedOrder extends Order {

    public placeOrder() {
        this._fixCorruptedItems();
        super.placeOrder();
    }
}

class OrderOnFraudulentAccount extends Order {

    public placeOrder() {
        this._notifyFraudDepartment();
    }
}

class IncompleteOrder extends Order {

    public placeOrder() {
        // Do nothing...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 组合模式

太好了！但是我们如何实例化这些不同的类呢？

设计模式的美妙之处在于它们通常最终会协同工作。在这种情况下，我们可以使用工厂模式:

```
class OrderFactory {

    public static makeOrder(accountId: number, items: any[]): Order {
        if (this._fraudWasDetected(accountId)) {
            return new OrderOnFraudulentAccount();
        } else if (items === null || items.length === 0) {
            return new EmptyOrder();
        }
        // and so on....
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:这是一个非常清楚的例子，说明了什么时候你真的想使用工厂模式——我发现这很容易被过度解释。希望这能帮助你明白为什么你首先要使用工厂。

我们已经将我们的`Order`类分成几个更特殊的类，每个类可以处理一个特殊的情况。

比方说，即使处理可疑欺诈账户/订单的逻辑非常复杂，我们也已经将这种复杂性隔离到一个有针对性的专门类别中。

就这些阶层的消费者而言——他们根本不知道幕后发生了什么！他们可以简单地调用`order.placeOrder()`，让每个类处理自己的特殊情况。

# 资源

*   [企业应用架构的模式](https://amzn.to/2MZYQGr)
*   [马丁·福勒的特例模式](https://www.martinfowler.com/eaaCatalog/specialCase.html)
*   [空对象设计模式](https://sourcemaking.com/design_patterns/null_object)

# 思想？

你遇到过特例模式吗？或者我提到的其他人？

# 保持联系

不要忘记通过以下方式与我联系:

*   [推特](https://twitter.com/jamesmh_dev)
*   [LinkedIn](https://www.linkedin.com/in/jamesmhickey/)

你也可以在我的网站[www.jamesmichaelhickey.com](https://www.jamesmichaelhickey.com)找到我。

# 浏览您的软件开发职业简讯

一封电子邮件简讯，将帮助您提升软件开发人员的职业水平！有没有想过:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？✔，有人愿意和我一起走走，回答我的问题吗？

听起来有趣吗？加入社区吧！*