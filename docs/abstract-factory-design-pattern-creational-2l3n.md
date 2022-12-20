# 抽象工厂设计模式

> 原文：<https://dev.to/itscoderslife/abstract-factory-design-pattern-creational-2l3n>

使用抽象工厂设计模式的动机是创建属于一个组但从几个不同的基类继承或实现不同协议的对象。

*   用于创建对象的实际类仍然是隐藏的，这让我们无需重构调用者的代码就可以修改工厂。
*   当您需要创建一组相关对象时，应该应用抽象工厂设计模式。
*   这种方法使得在不修改调用者的情况下改变工厂类成为可能。
*   要创建单个对象，请选择工厂方法模式。

> 抽象工厂模式与工厂模式相同，但是它隐藏了创建过程中涉及的类

抽象工厂定义了一个方法，该方法将返回工厂协议或工厂基类的实现。选择具体的实现类，它是实现工厂方法设计模式的类。

**举例**:

*   电脑商店——用户或商店供应商永远不会知道零件是在哪里以及如何制造的
*   汽车工厂——这里也适用于电脑商店

**总结**:通过隐藏用于创建对象的具体工厂，这种模式允许我们在不影响调用者的情况下修改工厂。如果我们想要创建一组属于一个组的对象，但是不实现相同的协议或基类，那么应该使用抽象工厂模式。抽象工厂定义了一个方法，该方法将返回工厂协议或工厂基类的实现。选择具体的实现类，它是实现工厂方法设计模式的类。最后，调用工厂方法，并创建所需的实例。