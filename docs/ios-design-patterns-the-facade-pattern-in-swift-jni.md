# iOS 设计模式:Swift 中的门面模式

> 原文：<https://dev.to/mrcflorian/ios-design-patterns-the-facade-pattern-in-swift-jni>

在过去的两年里，我们已经开发了 20 多个功能齐全的 iOS 模板。正如你所想象的，当创建一个 iOS 应用程序时，代码的可重用性是我们的首要任务之一。在本文中，我们来看看外观模式。让我们了解它是什么，以及它采用什么架构方法。

[![adapter pattern](img/41d017a7269036f6529d3136ae1cf5cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pj2tZzLF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sf2az5da4p4bq1yea8tf.png)

facade 模式(也拼写为 facade)是面向对象编程中常用的软件设计模式。类似于架构中的外观，外观是一个对象，作为一个面向前端的接口，屏蔽更复杂的底层或结构代码。你可以在维基百科上阅读官方的一般门面定义。

如果您曾经创建了一个简单的 APIClient / APIManager 类，那么您就使用了 Facade 设计模式。创建一个简单的 APIClient/APIManager 对象来封装网络，实际上是 Alamofire 或 URLSession 功能的一个门面。

## 立面示例

如果你曾经使用过钥匙链，你就会知道苹果的 API 是非常麻烦的。事实上，几乎没有人使用那些 API。我们知道的大多数 iOS 开发人员通常使用第三方开源 Swift 项目，该项目包装了 KeyChain 的逻辑，隐藏了所有那些可怕的接口。这些快捷图书馆本身就是门面。

对于一个简单的 facade 示例，让我们构建一个简单的 facade 对象来存储 Keychain
中的值

```
class KeychainFacade {

    func save(value: String, forKey key: String) throws {
        // save to Keychain logic
    }

    func readValue(forKey key: String) -> throws String {
        // read value from Keychain logic
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

facade 设计模式在行业中广泛传播，您可能已经使用过它，但没有意识到。它不是 Swift 特有的，而是一种通用的设计模式，在业内广泛传播。事实上，它甚至可以在 Javascript 或 [React 原生应用](https://www.instamobile.io/)中使用。它用于隐藏实现细节和抽象出接口，以便消费者可以处理较低层次的细节。虽然它增加了一个额外的抽象层，但是 facade 设计模式显著提高了代码的可读性和架构。