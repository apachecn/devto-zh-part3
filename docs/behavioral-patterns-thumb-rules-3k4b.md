# 行为模式——经验法则

> 原文：<https://dev.to/itscoderslife/behavioral-patterns-thumb-rules-3k4b>

##### *本帖直接抄袭[资料来源](https://sourcemaking.com/design_patterns/behavioral_patterns)，只是为了方便读者查阅这段知识。*

**如何以及何时应用**的规则:

1.  行为模式与对象之间的责任分配有关，或者说，将行为封装在对象中，并将请求委托给它。
2.  责任链、命令、仲裁者和观察者，解决了如何解耦发送者和接收者，但是有不同的权衡。责任链沿着潜在接收者链传递发送者请求。
3.  命令通常指定带有子类的发送者-接收者连接。
4.  仲裁者让发送者和接收者间接相互参照。Observer 定义了一个非常解耦的接口，允许在运行时配置多个接收器。
5.  责任链可以使用命令将请求表示为对象。
6.  责任链通常与复合相结合。在那里，一个组件的父组件可以作为它的后继组件。
7.  命令和纪念品就像魔法令牌一样被传递和调用。在命令中，令牌代表一个请求；在 Memento 中，它代表一个对象在特定时间的内部状态。多态对 Command 很重要，但对 memento 不重要，因为它的接口太窄，Memento 只能作为值传递。
8.  命令可以使用 Memento 来维护撤消操作所需的状态。
9.  宏命令可以用 Composite 实现。
10.  在被放入历史列表之前必须被复制的命令充当原型。
11.  解释器可以使用状态来定义解析上下文。
12.  解释器的抽象语法树是复合的(因此迭代器和访问者也适用)。
13.  解释器的抽象语法树中的终端符号可以与 Flyweight 共享。
14.  迭代器可以遍历复合对象。访问者可以对复合应用操作。
15.  多态迭代器依赖工厂方法来实例化适当的迭代器子类。
16.  中介者和观察者是相互竞争的模式。它们之间的区别在于，观察者通过引入“观察者”和“主体”对象来分发通信，而中介对象封装了其他对象之间的通信。我们发现制作可重用的观察者和主体比制作可重用的中介更容易。
17.  另一方面，Mediator 可以利用 Observer 动态注册同事并与他们通信。
18.  Mediator 类似于 Facade，它抽象了现有类的功能。
19.  中介器抽象/集中同事对象之间的任意通信，它通常“增加价值”，并且同事对象知道/引用它(即，它定义了多向协议)。相比之下，Facade 为子系统定义了一个更简单的接口，它不添加新的功能，并且不为子系统类所知(即，它定义了一个单向协议，其中它向子系统类发出请求，但反之亦然)。
20.  Memento 通常与迭代器一起使用。迭代器可以使用备忘录来捕捉迭代的状态。迭代器在内部存储纪念品。国家就像战略，除了它的意图。
21.  Flyweight 解释了状态对象何时以及如何被共享。
22.  状态对象通常是单态的。
23.  策略让你改变一个对象的内部。装饰器让你改变皮肤。
24.  策略就是算法。就像建造者对于创造一样。
25.  策略有两种不同的实现，第一种类似于状态。区别在于绑定时间(策略是一种绑定一次的模式，而状态更具动态性)。
26.  策略对象通常是很好的轻量级对象。
27.  策略类似于模板方法，只是粒度不同。
28.  模板方法使用继承来改变算法的一部分。策略使用委托来改变整个算法。
29.  访问者模式就像一个更强大的命令模式，因为访问者可以启动任何适合于它所遇到的对象类型的命令。