# 反应类与函数比较

> 原文：<https://dev.to/diomedes/react-classes-vs-funcomps-3cg4>

[https://thepractical dev . S3 . Amazon AWS . com/I/6q 6a 5 bxfcd 67920 xaru 6 . png](https://thepracticaldev.s3.amazonaws.com/i/6q6a5bxfcd67920xaru6.png)

编写一个函数或编写一个基于类的组件。

决定一个组件是类组件还是功能组件的一种方法是考虑组件内部应该有什么。如果它是一个简单的组件，返回一些没有逻辑的 jsx。这是一个功能性的组件，只会把东西呈现给 DOM，其他人称之为虚拟组件。

基于类的组件更容易组织
可以使用状态对象根据用户输入进行更改和交互。这些组件还处理和理解生命周期事件。生命周期是应用程序的各个阶段。例如加载屏幕。

规则类组件
必须是 javascript 类
必须是 externt(子类)React。组件
必须定义一个“渲染”方法来返回一些 jsx

# 只是这个日期前后写的笔记