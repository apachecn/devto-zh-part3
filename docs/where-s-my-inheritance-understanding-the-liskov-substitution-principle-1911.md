# 我的遗产呢？理解利斯科夫替代原理

> 原文：<https://dev.to/martingaston/where-s-my-inheritance-understanding-the-liskov-substitution-principle-1911>

什么是利斯科夫替代原理？在稳固的球队中，利斯科夫在阿森纳任职期间被替换的次数肯定比奥利维尔·吉鲁多。说真的，Barbara Liskov 是一位非常棒的计算机科学先驱，她的维基百科页面非常值得一读。

本质上，设计原则指定了子类应该可以替换其超类。让我们就此打住，澄清一些术语:

*   **超类**:被继承的*父*类(对象)。
*   **子类**:接收(通过`extends`关键字)继承的*子*类。

是的，我们在谈论遗产。现在，当你听到*继承*这个词时，你可能会和我一样:格式化你的电脑，然后逃之夭夭。这是最安全的方法。

当然，遗传是强大的。在 JavaScript 中轻松、自然地访问所有这些`Array`和`String`方法是很棒的。在 Ruby 中从`Object`获得所有这些功能是非常棒的。当继承融入到语言规范中时，我通常很乐意使用继承。但也许它也太强大了，就像绿巨人一样，当它失去控制时，它会粉碎你的应用程序，让你心碎。我不认为我们应该总是避免遗传，但我确实认为对它感到紧张是一种健康的生存本能。是火。

为了说明情况，让我们完全违反利斯科夫替代原理。让我们[为一位杰出而受人尊敬的商业大亨创造测试](https://jestjs.io/)，他可以接受采访并为他们的巨额财富工作。然后，让我们构建一个快速类来传递我们设计的示例。

### liskov.test.js

```
const Liskov = require("./liskov");

describe("Tycoon", () => {
  const tycoon = new Liskov.Tycoon();
  it("Gives Long Interviews To Big Newspapers", () => {
    expect(tycoon.interview()).toBeTruthy();
  });

  it("Goes to work for their fortune", () => {
    expect(tycoon.work()).toBeTruthy();
  });
}); 
```

### 利斯科夫. js

```
class Tycoon {
  constructor() {
    // initialisation
  }

  work() {
    return true;
  }

  interview() {
    return true;
  }
} 
```

厉害！多么令人印象深刻的勤劳资本主义的展示。现在，让我们为我们的`Tycoon`创建一个子`Socialite`类，它应该能够整齐地被交换到任何我们可能想要使用我们的`Tycoon`来进行迷人的面试或企业工作的实例中。

```
class Socialite extends Tycoon {
  constructor() {
    // initialisation
    super();
  }

  work() {
    throw new Error("Sounds horrible");
  }

  interview() {
    return true;
  }
} 
```

Drat。我们的工业，我们经济的支柱，将会完全崩溃，如果我们试着在我们的`Socialite`阶层交换的话！

那么，我们继承了我们的类，但是它们的预期**行为**是完全不同的，尽管它们看起来经常是一样的。通常，继承被用来指可以通过(语言允许的)类或接口继承的`IS-A`关系。在学术上，它可以归结为正方形/长方形问题(T4)，而在现实生活中，它可以被简洁地提炼为在你该回家前两分钟发生的一切。

违反 Liskov 替换原则是认识到现实世界的对象概念如何不能直接映射到面向对象的编程的好方法，当我第一次意识到这一点时，我有点害怕，因为这将完全取消一些人为的“面向对象的介绍”的例子，充满了类比，我们已经习惯了。

在 SOLID principles 中，Liskov 替换与 Open/Closed principles(开放/封闭原则)非常相似，后者声明一个类应该被封闭以进行修改——简而言之，[我们应该覆盖原始的类以向我们的应用程序添加新的功能](https://gaston.space/2019-04-25-exploring-the-open-closed-principle-with-the-ninja-turtles/)。我们花哨的新子类可以为聚会带来额外的功能，但它们仍然能够达到与它们所来自的类相同的音符。

但是，始终值得记住的是，不要过度设计我们自己，不要增加这些模式的复杂性，直到需要它们来进一步扩展我们的应用程序。在适当的情况下，Liskov 替换原则是另一种设计模式，可以帮助我们计划应用程序在增长时不可避免的变化。