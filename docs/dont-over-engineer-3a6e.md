# 不要过度工程化！

> 原文：<https://dev.to/samipietikainen/dont-over-engineer-3a6e>

如今，大多数(如果不是所有)程序员都熟悉干净代码的概念。简而言之，干净代码原则意味着一种专注于易于编写、理解和维护的代码的开发风格。然而，似乎这些相同的原则并没有应用到整体设计中。

> 任何傻瓜都能写出计算机能理解的代码。优秀的程序员编写人类能够理解的代码。
> 
> *   马丁·福勒

尽管如此，即使代码库有清晰的命名实践、有意义的注释，并且函数级的代码是可理解的和干净的，整体设计仍然可能难以理解，项目也难以维护。这通常是因为应用于小代码的干净的编码方法并不适用于整体设计。这个设计是过度设计的。

有时代码库看起来好像作者读了“四人帮”的设计模式书，并试图在一个代码库中应用其中的每一个模式。只有外观、装饰者、适配器、观察者和其他设计模式一个接一个，整体设计几乎不可能掌握。其他时候，作者似乎有一种重新发明所有轮子的冲动。拥有自己的容器。自己的锁定原语。自己的算法。一切从零开始写。

过度复杂、过度工程化的代码就像糟糕的意大利面条式代码一样难以理解和维护。当然，有时候项目真的很复杂，需要抽象和架构，很难理解。当然，在某些情况下，实现自己的算法或容器是正确的。重要的是应该有一个清楚的理由来解释为什么额外的复杂性是必要的。设计不应该只是为了复杂而复杂。事实上，人为的复杂性，在简单就足够的时候强迫使用过度复杂的设计，是马丁·福勒描述的常见的*代码味道*之一。

项目可能因为很多原因而变得过于复杂。开始时，设计可能是干净简单的，但是当新的特性和其他不太适合架构的变化被粘在一起而没有重构时，设计就变得混乱复杂了。当设计试图适应未来可能发生的所有可想象的变化时，其他项目可能从一开始就过于复杂。

事实是，设计一个好的坚固的建筑并不容易。在现实世界的项目中，需求会不断变化，并且会引入开始时不知道的新特性。此外，当项目成熟时，其他的修正、调整和更新也会随之应用。最重要的是，项目进度通常会增加快速完成工作的压力。设计应该足够灵活，以合理的努力允许所有这些变化，同时仍然尽可能简单。即使没有所有这些外部变化，也很难一次就把设计做好。通常需要一些迭代和改进来收敛到最终的设计

不幸的是，没有总能给出最佳架构的灵丹妙药。但是将在功能和类级别上工作的相同的干净的编码原则应用于架构是避免混乱和过度工程化设计的好方法。也许最重要的建议是保持事情简单。仅在真正需要时添加配置和使用设计模式。只有当抽象对设计有积极的贡献时，才创建抽象，例如通过删除重复或者分离模块。不要试图预测未来。使用目前已知的需求来设计系统。

当未来真的到来，设计需要适应的时候，重要的是系统要容易改变。通常，新的特性需要重构现有的设计，否则它们只是被粘在里面，破坏了架构。很快，即使是简单的更改也会从多个地方出乎意料地破坏系统，并引入细微的错误。因为设计太脆弱，改变开始需要越来越长的时间来实现。

让设计易于更改的关键是让它易于测试。当功能可以被容易地和自动地验证时，开发人员可以进行重构，而不用害怕破坏东西。当系统易于测试时，就更容易持续重构，从而保持小代码和大设计的整洁。小瑕疵和问题会越来越多，所以要立即处理它们。

> 一扇破碎的窗户，长时间无人修理，会给大楼里的居民灌输一种被遗弃的感觉——一种当权者不关心大楼的感觉。所以又一扇窗户被打破了。人们开始乱扔垃圾。涂鸦出现。严重的结构破坏开始了。在相对较短的时间内，建筑物的损坏超出了所有者修复它的愿望，被遗弃感成为现实。
> 
> *   务实的程序员安迪·亨特

不要住在破窗户里。确保代码和更大的架构保持简单和干净。不断地重构以适应变化，只有当它是合理的并且对设计有积极的贡献时，才增加抽象和复杂性。不要过度工程化。

最初发布在我的个人博客