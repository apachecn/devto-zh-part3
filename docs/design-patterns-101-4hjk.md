# 设计模式 101

> 原文：<https://dev.to/aaahmedaa/design-patterns-101-4hjk>

你可以在[https://aa-ahmed-aa.github.io/design-patterns-101/](https://aa-ahmed-aa.github.io/design-patterns-101/)找到主博客

### 目录

*   这个博客是给谁的
*   开始之前
*   快速回顾
    1.  南国际劳工组织
    2.  依赖注入
    3.  组成和聚集
*   设计模式
*   现实世界中的模式

## 这个博客是为谁写的

*   如果你想成为一个更好的程序员(阅读别人的代码-增强你的技能)。

*   如果你在写代码的时候(无意中)在重复自己。

*   那些认为设计模式是黑箱的人，并不能真正知道什么是设计模式。

*   有时你会看到你不理解的编程语言
    中的代码片段，你会想为什么这些代码是这样设计的。

## 开始前

本博客简要介绍了 Head first 设计模式中的 14 种设计模式，
因此，为了获得本博客的最大益处，
我建议您遵循以下步骤

1.  阅读模式定义。
2.  理解所介绍的问题，并尝试在更大的范围内想象它。
3.  阅读 UML。
4.  跟踪代码示例。

在我们继续之前，你需要知道两件重要的事情

#### 第一

设计模式只是解决问题的一种干净的方式，也为你的代码提供了灵活性，所以不要增强任何模式，直到你确保这种增强被 solid 成功应用(我们稍后会谈到这一点)，所以你可以将 SOLID 视为你的刹车，它将阻止你的幻想违反模式。

#### 第二

在这篇博客的最后，你可以像在 MVC 中看到的那样，一起使用许多模式。

这个博客是基于[这个回购](https://github.com/aa-ahmed-aa/Design_Pattern)
所以继续吧，开始吧。

## 快速回顾

### S.O.L.I.D

每个字母代表一个特定的单词和特定的概念，所以让我们马上开始吧。

> #### S —— Principle of Single Responsibility
> 
> > A class should have and only have one reason to change, which means that a class should have only one job.
> 
> #### Principle of O-on-off
> 
> > Objects or entities should be open to extension, but closed to modification.
> 
> #### L-Liskov Substitution Principle
> 
> > Let q(x) be a provable property about T-type object X, then q(y) should be provable for S-type object Y, where S is a subtype of T.
> 
> #### I-Principle of Interface Segregation
> 
> > Never force clients to implement interfaces that they don't use, or to rely on methods that they don't use.
> 
> #### D-Principle of Dependency Inversion
> 
> > Entities must rely on abstraction, not concreteness. It states that high-level modules cannot depend on low-level modules, but they should depend on abstractions.

*   注意:你可以在这里找到更多关于实体和代码示例[的信息。](https://scotch.io/bar-talk/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)

### 依赖注入

我们使用这种技术从一个依赖对象向另一个对象提供更多的功能。

#### 举例

这里我们将 A 类注入到 b 类中

```
class A{
    public function show()
    {
        print "Hello from A Class";
    }
}

class B{
    public $A;

    public function __construct()
    {
        $this->A = new A();
    }

    public function showFromA()
    {
        print $this->A->show();
    }

}

$b = new B();
$b->showFromA(); 
```

### 组成和聚集

组合实际上是一种所有权关系，而聚合是一种“包含”关系。在组合中，部分不能存在于包含它们的事物之外，但是单个的事物可以作为集合中的独特实体而独立存在。

#### 为了让这篇博客简洁明了，我推荐你阅读这篇简短的博客[组成和聚合](https://www.brainbell.com/tutorials/php/abstract-interface-composition-aggregation.html)

## 设计图案

跟随每个模式的链接，查看模式定义、UML 和代码示例

> 不要忘记追踪代码，这是熟悉模式最重要的一步

### [战略模式](https://github.com/aa-ahmed-aa/Design_Pattern/tree/master/Strategy%20pattern)

定义一系列算法，封装每一个算法并使它们可以互换。策略让算法独立变化

### [观察者模式(保持你的对象处于知晓状态)](https://github.com/aa-ahmed-aa/Design_Pattern/tree/master/Observer%20pattern)

定义对象之间的一对多依赖关系，这样当一个对象改变状态时，它的所有依赖对象都会得到通知并自动更新。

> 您可能会看到这种模式在前端框架中大量使用，因为它需要跟踪同步到 UI 的数据。

### [【装饰图案】(装饰对象)](https://github.com/aa-ahmed-aa/Design_Pattern/tree/master/Decorator%20pattern)

装饰器提供了一种灵活的替代方式来继承扩展功能。

### [工厂模式(以 oo 优度为后盾)](https://github.com/aa-ahmed-aa/Design_Pattern/tree/master/Factory%20pattern)

工厂模式分为两种主要类型(工厂方法和抽象工厂)，在这种模式中，我将关注两种子集模式之间的差异，并将只编写工厂方法。

> 检查**抽象工厂**和**工厂方法**的区别

### [【单例模式(同类对象之一)](https://github.com/aa-ahmed-aa/Design_Pattern/tree/master/Singleton%20pattern)

确保一个类有一个实例，并提供对它的全局访问点，我们可以说这个模式是我们创建只有一个实例的独一无二的对象的入场券。

### [命令模式(封装调用)](https://github.com/aa-ahmed-aa/Design_Pattern/tree/master/Command%20pattern)

将请求封装为一个对象，从而允许您用不同的请求参数化客户端，对请求进行排队或记录，并支持可撤消的操作。

### [适配器模式](https://github.com/aa-ahmed-aa/Design_Pattern/tree/master/Adapter%20pattern)

把一个类的接口转换成客户期望的另一个接口。让我们的类一起工作，否则会因为不兼容的接口而无法工作。

> 检查两种适配器类型

### [立面图案](https://github.com/aa-ahmed-aa/Design_Pattern/tree/master/Facade%20pattern)

为子系统外观中的一组接口提供统一的接口定义了一个更高级别的接口，使子系统更易于使用。

### [【模板方法模式(封装算法)](https://github.com/aa-ahmed-aa/Design_Pattern/tree/master/Template%20Method%20pattern)

在操作中定义算法的框架，将一些步骤推迟到子类。模板方法允许子类在不改变算法结构的情况下重新定义算法的某些步骤。

### [迭代器模式](https://github.com/aa-ahmed-aa/Design_Pattern/tree/master/Iterator%20pattern)

提供一种顺序访问聚合对象的方法，而无需公开其底层表示。

### [复合图案](https://github.com/aa-ahmed-aa/Design_Pattern/tree/master/Composite%20pattern)

将对象组合成树结构以请求部分整体层次结构，组合让客户统一处理单个对象和对象的组合。

### [代理模式](https://github.com/aa-ahmed-aa/Design_Pattern/tree/master/Proxy%20pattern)

为另一个对象提供代理或占位符，以控制对它的访问

### [状态模式](https://github.com/aa-ahmed-aa/Design_Pattern/tree/master/State%20pattern)

允许一个对象改变它的行为，当它的内部状态改变时，这个对象将会改变它的类。

## 现实世界中的模式

将模式分类有助于我们理解模式之间的区别，也有助于我们记忆它们。

#### 将模式归类为(结构-行为-创造)

> #### Structural
> 
> > Decorator-Combination-Proxy-Appearance-Adapter
> 
> #### Behavior
> 
> > Template Method-Iterator-Command 15]
> > 
> > #### Creation
> > 
> > > Single abstract factory-factory method

#### 归类处理(类对象)的模式

> #### Class
> 
> > Template method-adapter-factory method
> 
> #### Object
> 
> > Command-Decoration-Proxy-Composite-Appearance-Iteration

#### 用模式思考

##### 保持简单(亲亲)

尽可能用最简单的方式解决问题，你的目标应该是简单，而不是“我应该如何应用一个模式”。

##### 设计模式不是灵丹妙药；事实上，它们甚至不是子弹！

模式不是一颗神奇的子弹，你不能插上一颗，编译，然后吃一顿早午餐，要使用模式，你还需要考虑对你设计的其余部分的后果。

##### 你知道你需要一个模式的时候...

一旦你确定一个简单的解决方案不能满足你的需求，你就应该考虑问题
以及解决方案运行所需的一系列约束——这些将帮助你将你的问题与一个模式相匹配。

如果您有任何问题，请在 github repo [这里](https://github.com/aa-ahmed-aa/Design_Pattern/issues)提出您的问题。