# 面向对象的设计原则

> 原文：<https://dev.to/noahkuwae/brief-summary-of-design-principles-for-oop-14mg>

如果您正在使用面向对象编程开发软件，设计原则通过增加灵活性和可重用性，对您有很大帮助。
所有设计模式都遵循以下设计原则。

# 利斯科夫替代原理

*   描述
    *   超类的实例必须可以被子类的实例替换
    *   因此，子类的功能应该足够通用
*   利益
    *   它使客户端代码免受影响
    *   开闭原理也可以得到满足

# 开闭原理

*   描述
    *   类应该对扩展开放，对变化封闭
    *   在下列情况下，当设计和实现完成时，类被认为是关闭的
        *   经测试，其功能正常
        *   所有的属性和行为都被封装了
        *   类和实例不会阻塞系统，并且它们被确认是稳定的
*   利益
    *   它通过防止意外变化来保持稳定性
    *   它允许添加功能(例如通过继承)

# 依存倒置原则

*   描述
    *   访问高级概括
    *   不要直接访问低级的具体类
*   例子
    *   创建一个概括具体类的接口
    *   客户端仅访问界面
*   利益
    *   它松耦合，实现了灵活性和可重用性

# 构图对象原理(构图超过继承)

*   描述
    *   通过组合(聚合)而不是继承来扩展功能
*   例子
    *   当您想要使用某个类的功能时，添加其实例的属性，而不是继承它
*   利益
    *   通过避免继承超类的所有属性和方法，它失去了耦合

# 界面偏析原理

*   描述
    *   分离接口，并避免强制实现不需要的方法
*   利益
    *   它避免了不必要的依赖，并减少了耦合

# 最少知识原理(德米特定律)

*   描述
    *   方法只能调用下列其他方法
        *   在同一个对象中
        *   方法参数
        *   在方法中实例化的对象
        *   同一对象中的实例变量
*   利益
    *   它通过避免类之间不必要的交互来减少耦合

# 引用

[Coursera 设计模式](https://www.coursera.org/learn/design-patterns)