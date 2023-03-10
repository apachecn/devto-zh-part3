# 原型设计模式[创造]

> 原文：<https://dev.to/itscoderslife/prototype-design-pattern-creational-4a4g>

当构造新实例效率低下时，应该使用原型模式。当对象的初始化开销很大时，应该考虑使用原型模式。

*   原型模式从原型对象创建克隆实例。
*   克隆是通过复制原型实例的所有属性来创建的。
*   克隆体是独立的对象。
*   修改克隆不会影响原型，反之亦然。
*   值类型最终在赋值时被复制，而我们需要实现引用类型的复制，也就是类的复制。
*   原型模式通过复制原型对象来创建新对象。如果初始化对象的开销很大，请使用这种模式。
*   值类型使得实现原型模式变得非常容易，因为它们在赋值时会被自动复制。
*   对象的克隆是原型模式的核心。
*   为了正确地克隆对象，我们必须理解[浅拷贝和深拷贝](https://wordpress.com/block-editor/post/itscoderslife.wordpress.com/930)之间的区别。我也将为此提供代码示例。
*   原型:通过委托创作。工厂法:通过继承创造。

> 原型模式通过复制原型对象来创建新对象。如果初始化对象的开销很大，请使用这种模式。

示例:地址簿–在为组织中的员工创建新联系人时，基本信息、地址、雇佣详细信息、注释和链接等详细信息将有空的预填充。

链接了解深度 vs 浅度文案:[https://WordPress . com/block-editor/post/itscoderslife . WordPress . com/930](https://wordpress.com/block-editor/post/itscoderslife.wordpress.com/930)

**总结**:原型模式通过克隆一个原型对象并复制其所有属性来创建新的实例，而不是构造新的对象。复制的实例是一个独立的对象。更改其属性不会影响克隆的对象。当创建给定类型的新实例效率较低时，使用这种模式。