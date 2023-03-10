# 为什么要测试 POJOs？

> 原文：<https://dev.to/scottshipp/why-test-pojos-1lfb>

路易斯·里德在 Unsplash 上的照片

POJO(Plain Old Java Objects)和它们的同类如[值对象](https://en.wikipedia.org/wiki/Value_object)和 [JavaBeans](https://en.wikipedia.org/wiki/JavaBeans) 在现代 Java 应用中占据了独特的位置。他们有数百个略有不同的用例。它们通常被序列化和反序列化为 JSON、XML 或 SQL。它们构成了复杂应用程序中各层之间的桥梁。它们代表特定领域的语言。

并且它们经常未经测试。

反对测试它们的论点通常基于这样的假设，即 POJOs 有状态但没有行为。POJO 通常只是一包带有 getters 和 setters 的数据。如果你曾经见过典型的 POJO getter 的单元测试——那种直接返回变量的单元测试——这似乎毫无意义。为什么要测试语言能做什么？

测试这样一个 getter 的想法甚至没有通过最热心的测试驱动倡导者的最简单的测试。在一篇[栈溢出的回答](https://stackoverflow.com/questions/674408/junit-tests-for-pojos)，[罗伯特·c·马丁](https://en.wikipedia.org/wiki/Robert_C._Martin)中说:

> 我个人的原则是，我将为任何做出决定或做出不仅仅是琐碎计算的函数编写测试。我不会为 i+1 编写测试，但我可能会为 if (i<0)编写测试...而且肯定会 for(-b+math . sqrt(b * b-4 * a * c))/(2 * a)。

这当然是一个公平的观点。我敢打赌，大多数人*实际上*因为另一个原因而避免测试 POJOs(因为它很乏味)，但是我想知道至少有一个支持开发人员懒惰的坚实论据是很好的。

不过，我想从另一个角度来审视这个问题，我认为这个角度改变了一切:面向对象设计。

## 解耦和统一接入原理

让我们从[统一访问原则](https://en.wikipedia.org/wiki/Uniform_access_principle) (UAP)开始。在当今世界上基本上被遗忘的 UAP 指出:

> 一个模块提供的所有服务都应该通过一个统一的符号可用，这不会泄露它们是通过存储还是通过计算实现的

用 Java 术语来说，UAP 是在问，“当你在一个类(模块)上调用一个 getter(服务)时，收到的值仅仅是一段数据还是从其他数据中计算(或导出)出来的？”

客户端代码不应该知道。

*那又怎样？*你可能会问。*我们都知道 POJOs 只是一些数据包装器。为什么要假装不是呢？*

好吧，UAP(有点迂腐地)指出，客户端代码不能做出这样的假设。如果一个模块把它的实现泄露给了客户端代码，或者客户端代码对这个值的来源做了假设，那么它就违反了基本的 OO 设计原则。

从那个镜头，你甚至应该测试吸气剂。它们可能只是返回一个值，但如果不是呢？或者，如果他们是，但在未来改变了呢？

## 这种心态的好处

OO 的一个目标是将接口从实现中分离出来，使实现能够在不改变协作代码的情况下发展。在 Java 应用程序的早期，这种分离是通过为每个类创建一个接口来实现的。这种显式的物理分离(两个独立的文件)强调了这样一种思想，即当前类只是接口的一种可能的实现，而接口可能有许多种实现。

从第四个维度来考虑,“潜在的许多”实现也可能包括同一个 POJO 在应用程序生命周期中的不同发展。

因此，在接口的上下文中查看应用程序的好处是我们最大化了它的可扩展性。使用接口作为应用程序设计的单元，我们可以开发应用程序中任何需要更改的部分，而不会将这些更改强加给任何协作类。它们与实现是分离的。界面就像是变化的栅栏。只要*接口*不变，协作者也不需要。

实际上，这意味着我们可以“快速行动”以应对用户需求和/或业务环境的变化。

## 测试的目的

好的，这和测试有什么关系？嗯，只有当开发人员能够快速安全地开发实现时，快速移动才是可能的。所以我相信，测试应该和编译器一起提供这种安全性。测试应该保证实现遵循它们所宣传的接口。

当我在这个上下文中说“接口”时，我不仅仅是指 Java 的[接口](https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html)关键字，而是指 [OO 术语](https://stackoverflow.com/questions/2866987/what-is-the-definition-of-interface-in-object-oriented-programming#2867064)。一个类公开的方法的行为是否符合客户的期望？如果返回类型是对象，那么 null 是什么意思？如果是一个原语，是否有像-1 这样的值来表示某个东西不存在？对传入的输入进行或不进行什么验证？该输入需要什么？

基本上，将测试视为确保[方法契约](https://en.wikipedia.org/wiki/Design_by_contract)的一种方式。在现代 Java 中，测试是最好的方法。测试提供了编译器不能提供的对方法契约的验证。我们不想测试语言做了什么(编译器已经做了)，但是还有其他的事情我们需要知道。

## 实际例子

这不仅仅是理论上的。它总是出现。这里有一些例子。

### 构造者或设置者与获取者之间的协议

尽管 POJO 是一个“哑对象”,但在构造后，对象的 getters 会返回实例化该对象时使用的值。如果它是一个可变对象(您应该避免这种情况，但这可能是以后的话题)，那么在您调用 setter 之后，对其相应 getter 的调用应该会返回新值。

事实上，如此多的 POJO 或 POJO 部件都是通过复制和粘贴创建的，这使得这一点不那么确定。对于两个 getters 来说，比如说`getFirstName`和`getLastName`很容易被容易犯错的人发现:

```
public String getFirstName() {
  return firstName;
}

public String getLastName() {
  return firstName; // ack!
} 
```

Enter fullscreen mode Exit fullscreen mode

编译器会发现吗？没有。一个匆忙的开发者会吗？有时候？好的单元会测试吗？是的。

### 投保数据的含义

即使是“哑”数据也可以有契约。我之前提到过这个问题，当我问“如果这是一个对象，null 的含义是什么？”有各种各样的情况，其中数据有特殊的值，如 null，-1、来自枚举的值或其他值。在很多情况下，测试应该真正帮助我们保证我们的应用程序工作正常。另一类纯数据问题产生于多个数据字段之间的一致性。例如，如果我在一个工资支付期内工作时间为零，我就不可能还在加班。类似地，如果在给定的 POJO 中有任何类型的验证，也应该进行测试。如果一个 getter 返回一个 int，但是这个 int 表示一个百分比，那怎么办？如果这个数字小于零或大于 100，可以吗？该应用程序的测试如何防止开发人员的错误？

顺便说一下，从 getter 返回的百分比是 UAP 完全适用的一个很好的例子。一个百分比可能是从另外两个值中计算出来的，但是谁知道呢？

### 在原本没有的地方增加行为

也许你在哲学上反对 POJOs 拥有行为，但是在现实生活中，它有时比其他选择更好。一个经典的例子是重构来消除[功能嫉妒](http://wiki.c2.com/?FeatureEnvySmell)。Feature Envy 是马丁·福勒在《重构*一书中介绍的一个著名的代码气味，它也出现在罗伯特·c·马丁的经典著作《清理代码》中，在*气味和启发*章节中以 G14 的形式出现。在那里，给出了下面的例子:* 

```
public class HourlyPayCalculator {
  public Money calculateWeeklyPay(HourlyEmployee e) {
    int tenthRate = e.getTenthRate().getPennies();
    int tenthsWorked = e.getTenthsWorked();
    int straightTime = Math.min(400, tenthsWorked);
    int overTime = Math.max(0, tenthsWorked - straightTime);
    int straightPay = straightTime * tenthRate;
    int overtimePay = (int)Math.round(overtime*tenthRate*1.5);
    return new Money(straightPay + overtimePay);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面给出特征羡慕的解释:

> `calculateWeeklyPay`方法进入`HourlyEmployee`对象以获取它所操作的数据。`calculateWeeklyPay`方法*嫉妒*范围`HourlyEmployee`。它“希望”它能在`HourlyEmployee`里面。

当然，答案是将`calculateWeeklyPay`移入`HourlyEmployee`。不幸的是，如果你有一个规则将`HourlyEmployee`的包排除在测试和代码覆盖之外的代码库，那就成问题了。

### 平等和同一性

最后一个简单的例子。POJOs 的主要用途之一是为应用程序提供有意义的等式逻辑。但是用例不同；你想检查平等还是身份？定义一个[值对象](https://en.wikipedia.org/wiki/Value_object)的主要原因是，当它们各自的成员都相等时，它等于另一个值对象。但是其他类型的 POJOs 可能需要根据身份进行区分。哪个是哪个总是很明显吗？您是否相信您的开发团队(或您自己)能够自动知道差异，并随着事情的变化添加或删除测试。您应该提供测试来保证这种行为吗？

## 你怎么看？

我希望这至少向最热心的 POJO 测试对手表明，有充分的理由测试他们。如果这些情况出现在您现有的 POJOS 中，也许现在您至少会*添加*测试。你怎么想呢?如果你现在不测试 POJOs，你认为你会开始吗？还是你已经测试过了？如果有，为什么？你的理由是什么？请在下面的评论中告诉我你的想法。*