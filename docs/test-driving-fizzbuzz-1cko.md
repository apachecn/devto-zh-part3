# 试驾 FizzBuzz

> 原文：<https://dev.to/ayelishaamoah/test-driving-fizzbuzz-1cko>

### 什么是 TDD？

测试驱动开发是一种开发方法，包括首先编写您的测试，然后编写最简单的代码让您通过测试，让测试来指导您的应用程序代码。这个过程根据需要重复多次。

### 单元测试

单元测试应该测试一个单独的代码单元——通常是一个方法/函数，并且应该只关注那个单元的输出。我们可以使用诸如 mocks、stubs 和测试框架之类的工具来简化单元测试。

通常，单元测试包括四个部分:

*   设置——被测系统(通常是一个类、对象或方法)被设置好，例如`account = Account.new`
*   练习-被测系统被执行`account.deposit(1000)`
*   验证-对照期望值检查结果`expect(account.show_balance).to eq 1000`
*   拆卸-重置测试中的系统(通常由语言框架处理)

### TDD 流程

一种常见的 TDD 方法是红绿重构生命周期:

*   红色-写一个失败的测试
*   绿色-编写最简单的代码通过测试
*   重构——清理编写的代码

### swift 中的 TDD

当我开始 swift 之旅时，我对接触 XCTest 框架很感兴趣。为了做到这一点，我将尝试 TDD 一个基本的 FizzBuzz 应用程序。这涉及到用 XCode 启动一个项目。

Fizzbuzz 是一种编程形式，可以用来学习 TDD，也是用一种新语言探索 TDD 的好方法。

### 流程

*   为测试创建文件
*   编写第一个测试，例如

```
 func testIsDivisibleByThree() {
        let brain = Brain()
        let result = brain.isDivisibleByThree(number: 3)
        XCTAssertEqual(result, true)
    } 
```

Enter fullscreen mode Exit fullscreen mode

XCode 会抛出一些你想要逐步解决的错误。

*   将`@testable import FizzBuzz`放在`import XCTest`下的文件顶部，将应用程序导入到您的测试文件中
*   编写最简单的代码来通过测试

```
class Brain {
    func isDivisibleByThree(number: Int) -> Bool {
        return true
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   使用⌘ + U 运行测试

当代码开始变得重复时，用重构对每个功能重复这个过程。

这是一篇关于 Medium [开始使用 Swift 中的 TDD](https://medium.com/@ynzc/getting-started-with-tdd-in-swift-2fab3e07204b)的很棒的文章，我是为了在 Swift 中构建 FizzBuzz 而跟随这篇文章的。